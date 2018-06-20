[](学习sbtenv)
![](./logos/sbt_logo.png)

# 背景

最近由于工作需要, 我总是在不同的scala项目间流动开发. 这就遇到一个很棘手的问题, 这几个项目用的sbt版本不一致, 老项目用的是 `sbt 0.13.15`, 新项目用的是 `sbt 1.0.2`. 写scala项目最要命的可能就是高版本总是不兼容低版本.

一开始着急干活, 就很暴力的卸载了老版本, 安装需要的sbt版本. 但是当一天当中都需要处理两个项目的时候, 这种切换版本的方式就好浪费生命. 起初我是想自己写个shell来实现自动版本切换的, 但一想估计有人遇到过我这问题, 各种搜索后(我已然忘记了我是怎么搜的了), 发现了这个很好用的工具 `sbtenv`.

# sbtenv 是什么

官网给了个定义: Groom your sbt environment.
本质sbtenv干的事情就像nvm, sbtenv能很轻松的切换sbt版本, 这样就可以不同的项目用不同版本的sbt, 同时本机上可以装多个版本的sbt.


# sbtenv 安装

使用brew安装sbtenv很方便的, 具体步骤如下:

1. 安装

	```
	brew install sbtenv
	```
2.	添加 ~/.sbtenv/bin 到 $PATH

	```
	echo 'export PATH="${HOME}/.sbtenv/bin:${PATH}"' >> ~/.bash_profile
	```
3. 添加 sbtenv init 到 shell 

	```
	echo 'eval "$(sbtenv init -)"' >> ~/.bash_profile
	```
4. 重启shell, 检验sbtenv是否安装成功

	```
	type sbtenv
	sbtenv is a shell function from /Users/qyf404/.bash_profile
	```

# 用sbtenv安装不同版本的sbt

光把sbtenv安装了, 还不能工作呢, 还需要安装你需要的sbt.

1. 使用命令`sbtenv install -l`查询能安装的sbt版本
	
	```	
	$> sbtenv install -l
	All available versions:
	sbt-0.13.15
	sbt-0.13.16
	sbt-0.13.17
	sbt-0.13.2
	sbt-0.13.5
	sbt-0.13.6
	sbt-0.13.7
	sbt-0.13.8
	sbt-0.13.9
	sbt-1.0.0
	sbt-1.0.1
	sbt-1.0.2
	sbt-1.0.3
	sbt-1.0.4
	sbt-1.1.0
	sbt-1.1.1
	```
2. 安装指定版本的sbt

	```
	$> sbtenv install sbt-0.13.17
	```

3. 检查是否安装成功, 使用命令`sbtenv versions`能查看本机安装的所有sbt版本.

	```
	$> sbtenv versions
	system
	sbt-0.13.17
	```

# 用sbtenv切换不同版本的sbt

1. 当前工作目录切换sbt版本使用命令`sbtenv local sbt-0.13.17`. (切换后会在当前目录增加一个`.sbt-version`的文件来记录使用的sbt版本)

	```
	$> sbtenv local sbt-0.13.17
	```
	
2. 检查是否切换成功使用命令`sbtenv version`.

	```
	$> sbtenv version
	sbt-sbt-0.13.17 (set by /Users/yfqi/.sbt-version)
	```


# 进阶

## 手动安装sbt

有时候`sbtenv install -l`里面没有我们要安装的sbt版本, 这时我们可以采取手动安装的方式.

1. 去[github](https://github.com/sbt/sbt/releases)上找到我们要安装的版本.
2. 下载tgz的压缩包, 比如我下的是 `sbt-1.1.6.tgz`.
3. 创建存放目录

	```
	$> mkdir -p ~/.sbtenv/versions/sbt-1.1.6
	```
4. 将下载的压缩包移到创建好的目录里

	```
	$> mv ~/Downloads/sbt-1.1.6.tgz ~/.sbtenv/versions/sbt-1.1.6
	```

5. 解压并删除压缩包 (解压后sbt命令的磁盘路径应该是`/Users/qyf404/.sbtenv/versions/sbt-1.1.6/sbt/bin`)

	```
	$> cd ~/.sbtenv/versions/sbt-1.1.6
	$> tar xf sbt-1.1.6.tgz && rm -f *.tgz
	$> ls
	sbt
	```

6. 检查是否安装成功

	```
	$> sbtenv versions
	system
	sbt-0.13.17
	sbt-1.1.6
	```

## 设置全局sbt版本

使用`sbtenv global sbt-1.1.6`可以设置全局的sbt版本.

## sbt版本设置优先级

sbt版本设置生效的优先级依次是:

项目的设置 > 当前目录的设置 > 全局的设置

* 项目的设置在文件`./project/build.properties`中配置.

	```./project/build.properties
	sbt.version=0.13.15
	
	```

* 当前目录的设置在文件`./.sbt-version`中配置

	```./.sbt-version
	sbt-0.13.17
	```
	
* 全局的设置在文件`~/.sbtenv/version`中设置

	```~/.sbtenv/version
	sbt-1.1.6
	```

# 参考

[sbtenv github](https://github.com/sbtenv/sbtenv)

日期:2018-06-20 作者:[辵鵵](https://about.me/qyf404)