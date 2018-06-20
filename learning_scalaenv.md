[](学习scalaenv)

![](./logos/scala_logo.png)

# 背景

最近由于工作需要, 我总是在不同的scala项目间流动开发. 这就遇到一个很棘手的问题, 这几个项目用的scala版本不一致, 老项目用的是 `scala 2.11.8`, 新项目用的是 `scala 2.12.6`. 写scala项目最要命的可能就是高版本总是不兼容低版本.

如果你用的是IDE开发, 项目版本不同必不会给你带来问题, 因为IDE会帮你管理各个项目的scala版本.但是如果你用命令行去干一些事情, 就会发生版本的问题了.于是你就需要不停的安装卸载来切换scala版本.

我想这种切换版本的事情我应该不是第一个遇到到, 于是我各种搜索后(我已然忘记了我是怎么搜的了), 发现了这个很好用的工具 `scalaenv`.

# scalaenv 是什么

[scalaenv](https://github.com/scalaenv/scalaenv)给了定义: Groom your app's Scala environment with scalaenv like rbenv :)
本质scalaenv干的事情就像rbenv, scalaenv能轻松的切换scala版本.

# scalaenv 安装

使用[brew](https://brew.sh/)安装scalaenv很方便的, 具体步骤如下:

1. 安装

	```
	$> brew install scalaenv
	```
	
2. 添加~/.scalaenv/bin 到 $PATH

	```
	$> 	echo 'export PATH="${HOME}/.scalaenv/bin:${PATH}"' >> ~/.bash_profile
	```
	
3. 添加 scalaenv init 到 shell

	```
	$> 	echo 'eval "$(scalaenv init -)"' >> ~/.bash_profile
	```
	
4. 重启shell, 检验scalaenv是否安装成功

	```
	$> 	type scalaenv
	scalaenv is a shell function from /Users/qyf404/.bash_profile
	```


# 使用scalaenv安装不同的scala版本

光把scalaenv安装了, 还不能工作呢, 还需要安装你需要的scala版本.

1. 使用命令`scalaenv install -l`查询能安装的scala版本

	```
	$> scalaenv install -l
	Available versions:
	```
	> 我不清楚为什么我的机器敲这个命令后没有展示出可以安装的版本号. 但是如果我知道某个版本一定是有的, 也可以按照第二步成功安装scala的. 	

2. 安装指定版本的scala

	```
	$> scalaenv install scala-2.11.8
	```
	
3. 检查是否安装成功, 使用命令`scalaenv versions`能查看本机安装的所有scala版本.

	```
	$> scalaenv versions
	scala-2.11.8
	```

# 用svalaenv切换不同的scala版本


1. 当前工作目录切换scala版本使用命令`scalaenv local scala-2.11.8`. (切换后会在当前目录增加一个`.scala-version`的文件来记录使用的scala版本)

	```
	$> scalaenv local scala-2.11.8
	```
	
2. 检查是否切换成功使用命令`scalaenv version`.

	```
	$> scalaenv version
	scala-2.11.8 (set by /tmp/.scala-version)
	```

# 进阶

## 手动安装scala

像上面说的, 我使用命令`scalaenv install -l`看不到我要安装的scala版本, 这时可以采取手动安装的方式安装scala.

1. 去[官网](https://www.scala-lang.org/files/archive/)上找到要安装的版本.
2. 下载tgz压缩包, 比如我下的 `scala-2.12.6.tgz`.
3. 减压到`~/.scalaenv/versions/`目录. (减压后scala命令的磁盘路径应该是`/Users/qyf404/.scalaenv/versions/scala-2.12.6/bin`)

	```
	$> tar xf scala-2.12.6.tgz -C ~/.scalaenv/versions/
	```
6. 检查是否安装成功

	```
	$> scalaenv versions
  	scala-2.11.8
	scala-2.12.6
	```

## 设置全局scala版本

使用`scalaenv global scala-2.11.8`可以设置全局的scala版本.

## scala版本设置优先级
	
scala版本设置生效的优先级依次是:

项目的设置 > 当前目录的设置 > 全局的设置

* 项目的设置在文件`./build.sbt`中配置.

	```./project/build.properties
	...
	scalaVersion := "2.11.8"
	...
	```

* 当前目录的设置在文件`./.scala-version`中配置

	```./.scala-version
	scala-2.11.8
	```
	
* 全局的设置在文件`~/.scalaenv/version`中配置

	```~/.scalaenv/version
	scala-2.11.8
	```

# 参考

* [scalaenv github](https://github.com/scalaenv/scalaenv)

日期:2018-06-20 作者:[辵鵵](https://about.me/qyf404)