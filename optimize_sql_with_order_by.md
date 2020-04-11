[](优化有order by的sql语句)

![](./logos/mysql_logo.png)


# 线上事故回溯

## 需求

最近在做一个数据迁移的动作. 将一张大表中的数据归档到其他表中, 归档的原因就是这张表太大了, 而且增长速度很快, 需要拆表. 

这个表有多大? 存量数据5000万条数据, 每日还在以120万条的数据量增长. 

为了不影响已有的功能使用. 这张表只会保留近15天的数据, 15天估计也就2000万条数据, 15天前的数据会归档到其他表中. 

## 实现方案

拆分一下任务, 预计做这么几步就可以了.
1. 写一个调度接口, 接收一个时间参数. 
1. 按照创建时间作为条件进行过滤, 查询需要迁移的数据量, 确定页数.
2. 按照创建时间作为条件进行过滤, 分页查询出大表中的数据. 
3. 循环查询出来的数据, 插入到小表中.
4. 按照创建时间作为条件进行过滤, 删除大表中的数据.

上线前在测试环境做了验证, 可以按照预期完成数据迁移. 

> 为了方便描述, 这张大表的表结构大概是这样的
>   ```
>   CREATE TABLE  `t_table`(
>     `c_id` bigint(20) unsigned NOT NULL AUTO_INCREMENT COMMENT '自增主键',
>     `c_created_time` bigint(20) NOT NULL  COMMENT '入库时间',
>     PRIMARY KEY (`c_id`),
>     KEY idx_created_time (c_created_time)
>   )ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
>   ```


## 第一条慢sql

上线后第一次运行就产生了一条慢sql. 

就是删除的sql  `delete from t_table where c_created_time <= ${createTime}`.

在数据量小的时候, 这条sql执行时没有任何问题的, 但是当单次删除的数据比较多时就会出问题. 出问题的原因是delete执行时会锁表,  这条语句在删除5000万条数据中的50万条数据时, 需要执行10分钟. 而删除3000万条数据需要执行8小时. 而且由于其在执行时会锁表, 导致其他insert与update语句执行会变慢, 产生雪崩效应.

虽然where条件中的字段有索引. 



# 解决第一条慢sql

其实解决方法也很简单, 就是按照主键一条一条的删.  这样能让单条sql执行速度很快, 解决了锁表带来的坏邻居效应.

删除语句就变成了这样: `delete from t_table where c_id = ${id}`

当然带来的弊端是执行时间变长了. 原来删除50万条数据需要10分钟, 现在需要近2小时. 但是慢sql没了, 能让系统更稳定.

这大概就是性能优化的终章: 削峰填谷.  其并不能把计算的绝对量降低, 只是把计算的峰值消减了, 把计算量平摊到了更长的时间中.



> 坏邻居效应: 由于坏邻居争抢公共资源, 导致影响了正常的住户.



# 第二条慢sql

把最慢的处理了之后, 第二慢的就变成最慢的了. 

`select c_id, c_created_time from t_table where c_created_time<= ${createTime} order by c_id desc limit ${beginRow}, 100` 

如果where之后的数据量是50万, 那这条语句执行起来是没有任何问题的. 但是如果过滤后的数据量是1000万, 这条语句就是个慢sql.

原因出在了order by 之后的c_id. 

我们把执行计划打出来看一下. 

`explain select c_id, c_created_time from t_table where c_created_time<= ${createTime} order by c_id desc limit ${beginRow}, 100`

* 如果条件过滤后剩余50万, 执行计划是这样的:

  | **id** | **select_type** | **table** | **partitions** | **type** | **possible_keys** | **key** | **key_len** | **ref** | **rows** | **filtered** | **Extra**   |
  | ------ | --------------- | --------- | -------------- | -------- | ----------------- | ------- | ----------- | ------- | -------- | ------------ | ----------- |
  | **1**  | SIMPLE          | t_table   |                | index    | idx_created_time  | PRIMARY | 8           |         | 2140     | 4.67         | Using where |

 

* 如果条件过滤后剩余1000万, 执行计划是这样的:
   | **id** | **select_type** | **table** | **partitions** | **type** | **possible_keys** | **key** | **key_len** | **ref** | **rows** | **filtered** | **Extra**   |
   | ------ | --------------- | --------- | -------------- | -------- | ----------------- | ------- | ----------- | ------- | -------- | ------------ | ----------- |
   | **1**  | SIMPLE          | t_table   |                | index    | idx_created_time  | PRIMARY | 8           |         | 200      | 50           | Using where |



从两个执行计划来看感觉好像没什么问题,  从执行上看的话怎么会一个块一个慢呢?



我们把执行计划再改一下, 去掉limit:

`explain select c_id, c_created_time from t_table where c_created_time<= ${createTime} order by c_id desc `



* 如果条件过滤后剩余50万, 执行计划是这样的:

  | **id** | **select_type** | **table** | **partitions** | **type** | **possible_keys** | **key**          | **key_len** | **ref** | **rows** | **filtered** | **Extra**                             |
  | ------ | --------------- | --------- | -------------- | -------- | ----------------- | ---------------- | ----------- | ------- | -------- | ------------ | ------------------------------------- |
  | **1**  | SIMPLE          | t_table   |                | range    | idx_created_time  | idx_created_time | 8           |         | 645780   | 100          | Using index condition; Using filesort |



* 如果条件过滤后剩余1000万, 执行计划是这样的:

  | **id** | **select_type** | **table** | **partitions** | **type** | **possible_keys** | **key** | **key_len** | **ref** | **rows** | **filtered** | **Extra**   |
  | ------ | --------------- | --------- | -------------- | -------- | ----------------- | ------- | ----------- | ------- | -------- | ------------ | ----------- |
  | **1**  | SIMPLE          | t_table   |                | index    | idx_created_time  | PRIMARY | 8           |         | 54929219 | 50           | Using where |



我们会发现50万的那个执行计划type是`range`, Extra中是`Using index condition; Using filesort`.



从执行计划看, 可能有两个地方导致了慢sql:

1. filesort其实会把条件过滤出来的数据进行重排序的. 查询结果如果数据量大, 最好不要做filesort.
2. type是index会遍历索引树. (真棒, 就差来个全表扫描了)



>type的取值及含义:
>
>ALL：Full Table Scan， MySQL将遍历全表以找到匹配的行
>
>index：Full Index Scan，index与ALL区别为index类型只遍历索引树 
>
>range：索引范围扫描，对索引的扫描开始于某一点，返回匹配值域的行，常见于between、<、>等的查询 
>
>ref：非唯一性索引扫描，返回匹配某个单独值的所有行。常见于使用非唯一索引即唯一索引的非唯一前缀进行的查找 
>
>eq_ref：唯一性索引扫描，对于每个索引键，表中只有一条记录与之匹配。常见于主键或唯一索引扫描 
>
>const、system：当MySQL对查询某部分进行优化，并转换为一个常量时，使用这些类型访问。如将主键置于where列表中，MySQL就能将该查询转换为一个常量 
>
>NULL：MySQL在优化过程中分解语句，执行时甚至不用访问表或索引

### 为什么有一个`order by c_id desc`?

这张大表由于数据量大, 只能分页把历史数据查询出来进行处理. 

但是, 由于处理完毕后, 就会删除历史数据, 处理失败的历史数据还要继续留在这个表中.  那么就不能从第一页的第一条数据开始处理了. 只能倒着翻页, 也就是先处理满足条件的最后一页数据, 然后倒数第二页数据, 直到第一页数据.

但倒着处理会发生另外一个事情, 如果使用默认的排序(主键正序), 就会导致旧的数据被最后处理了. 这里我们可能期望旧的数据先处理.  这就需要把满足条件的数据做个倒序排列, 然后最后一条数据也就是最旧的数据了. 



# 解决第二条慢sql

解决思路其实很简单, 就是最好让type是`range`, 而且不要出现 `Using filesort`.



解决方法其实也很简单,  最粗暴的做法就是把order by语句删了. 



当然还有另外一种方式,  由于这张表的入库时间排序基本就是id的排序. 所以可以直接把order by中的c_id换成c_created_time.

`select c_id, c_created_time from t_table where c_created_time<= ${createTime} order by c_created_time desc limit ${beginRow}, 100`

好, 我们继续看一下两种执行计划的结果:

`explain select c_id, c_created_time from t_table where c_created_time<= ${createTime} order by c_created_time desc limit ${beginRow}, 100`

* 如果条件过滤后剩余50万, 执行计划是这样的:

  | **id** | **select_type** | **table** | **partitions** | **type** | **possible_keys** | **key**          | **key_len** | **ref** | **rows** | **filtered** | **Extra**             |
  | ------ | --------------- | --------- | -------------- | -------- | ----------------- | ---------------- | ----------- | ------- | -------- | ------------ | --------------------- |
  | **1**  | SIMPLE          | t_table   |                | range    | idx_created_time  | idx_created_time | 8           |         | 639582   | 100          | Using index condition |

* 如果条件过滤后剩余1000万, 执行计划是这样的:

  | **id** | **select_type** | **table** | **partitions** | **type** | **possible_keys** | **key**          | **key_len** | **ref** | **rows** | **filtered** | **Extra**             |
  | ------ | --------------- | --------- | -------------- | -------- | ----------------- | ---------------- | ----------- | ------- | -------- | ------------ | --------------------- |
  | **1**  | SIMPLE          | t_table   |                | range    | idx_created_time  | idx_created_time | 8           |         | 27461543 | 100          | Using index condition |

从两个执行计划来看感觉好像没什么问题,  从执行上看的话怎么会一个块一个慢呢?



我们把执行计划再改一下, 去掉limit:

`explain select c_id, c_created_time from t_table where c_created_time<= ${createTime} order by c_created_time desc `



* 如果条件过滤后剩余50万, 执行计划是这样的:
	 | **id** | **select_type** | **table** | **partitions** | **type** | **possible_keys** | **key**          | **key_len** | **ref** | **rows** | **filtered** | **Extra**             |
  | ------ | --------------- | --------- | -------------- | -------- | ----------------- | ---------------- | ----------- | ------- | -------- | ------------ | --------------------- |
  | **1**  | SIMPLE          | t_table   |                | range    | idx_created_time  | idx_created_time | 8           |         | 639582   | 100          | Using index condition |


* 如果条件过滤后剩余1000万, 执行计划是这样的:

  | **id** | **select_type** | **table** | **partitions** | **type** | **possible_keys** | **key** | **key_len** | **ref** | **rows** | **filtered** | **Extra**                   |
  | ------ | --------------- | --------- | -------------- | -------- | ----------------- | ------- | ----------- | ------- | -------- | ------------ | --------------------------- |
  | **1**  | SIMPLE          | t_table   |                | ALL      | idx_created_time  |         |             |         | 54922533 | 50           | Using where; Using filesort |



从执行计划上看, 最后一个发生了全表扫描, 不过带上limit分页后, 其执行速度很快的, 12ms就能查出一页数据, 



# 我们学到了什么?

通过这次慢sql优化, 我们学到了:

1. 删除数据时, 如果符合条件的数比较多 (实验得到的结果是, 5000万条数据中删除数据量超过1万就会产生坏邻居效应), 最好是查询出来后, 按照主键一条一条删.
2. 查询数据时, 排序用到的字段最好是where条件中的字段.





# 参考



* [MySQL 执行计划中Extra浅析](https://www.cnblogs.com/kerrycode/p/9909093.html)
