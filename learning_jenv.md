[](学习jenv)

![java](./logos/java_logo.png)

# 背景

生活不只是眼前的苟且, 还有诗和远方.

上个月工作需要启动了一个小项目, 按最初的计划会用JDK8. 但当计划报上去后, 运维部门出于后续升级维护的考虑, 不允许使用已经出来4年多的JDK8了, 建议使用JDK9或10. 连官方都说了, JDK9和10是两个过度版本, 真正的惊喜在JDK11. 对于开发人员来说这就悲喜交加了. 悲的是: JDK9开始java最大的动作就是已经不怎么向下兼容了, 如果使用必将会遇到各种坑. 喜的是: 可以使用JDK新版本, 尝试新特性. 后面几个开发一合计, 反正是踩坑了, 就定了用JDK10, 后续升级到JDK11 预计成本较小.

然后我们就开始踩坑了, 遇到最多的问题就是JDK9引入的模块化, 导致很多JDK的包需要明确声明才能使用.

新技术用起来了, 老项目还的维护啊. 另一个大坑就是JDK10对JDK8的向下兼容真的不怎么好. 所以我们每次切换项目时, 本地的环境变量JAVA_HOME就要跟着变一下.

这种无聊的改来改去的工作做多了, 我就开始找能省点力气的方法, 就找到了这个工具`jenv`.

# jenv是什么

官方给的定义: jEnv is a command line tool to help you forget how to set the JAVA_HOME environment variable.

这个工具可以方便的帮助我们切换JDK版本.

# jenv 安装

使用[brew](https://brew.sh/)安装jenv很方便的, 具体步骤如下:

1. 安装
	
	```
	$> brew install jenv
	```
2.  设置

	```
	$> echo 'export PATH="$HOME/.jenv/bin:$PATH"' >> ~/.bash_profile
	$> echo 'eval "$(jenv init -)"' >> ~/.bash_profile
	```

3. 重启shell, 检验jenv是否安装成功

	```
	$> type jenv
	jenv is a shell function from /Users/qyf404/.bash_profile
	```

# 使用jenv安装不同版本的JDK

在mac上安装JDK是需要root权限的, 所以jenv是不能直接安装JDK的, 只能先[手动安装](http://www.oracle.com/technetwork/java/javase/downloads/index.html)好, 之后将JDK添加到jenv中.

```
$> jenv add /Library/Java/JavaVirtualMachines/jdk1.8.0_73.jdk/Contents/Home
$> jenv add /Library/Java/JavaVirtualMachines/jdk-10.0.1.jdk/Contents/Home
```

添加完JDK后, 可以通过命令`jenv versions`查看已经添加的JDK版本


```
$> jenv versions
* system
  1.8
  1.8.0.73
  10.0
  10.0.1
  oracle64-1.8.0.73
  oracle64-10.0.1
```

# 使用jenv切换不同版本的JDK

1. 当前工作目录切换JDK版本使用命令`jenv local 10.0.1`.(切换后会在当前目录增加一个`.java-version`文件记录使用的JDK版本)

	```
	$> jenv local 10.0.1
	```
2. 检查是否切换成功使用命令`jenv version`.

	```
	$> jenv version
	10.0.1 (set by /Users/yfqi/tmp/.java-version)
	
	$> java -version
	java version "10.0.1" 2018-04-17
	Java(TM) SE Runtime Environment 18.3 (build 10.0.1+10)
	Java HotSpot(TM) 64-Bit Server VM 18.3 (build 10.0.1+10, mixed mode)
	```

# 设置全局JDK版本

使用`jenv global 10.0.1`可以设置全局的JDK版本

# JDK版本设置优先级

JDK版本设置生效的优先级依次是:

当前目录的设置 > 全局的设置

* 当前目录的设置在文件`./.java-version`中配置

	```./.java-version
	10.0.1
	```
	
* 全局的设置在文件`~/.jenv/version`中配置

	```~/.jenv/version
	10.0.1
	```

# 启用和停用插件

有很多构建工具运行时是需要JDK的, 但是很多这种工具会在安装时指定固定的JDK, 而这就会导致jenv配置的JDK对于这些构建工具失效了. 要解决这种问题就需要启动对应的插件.注意这是一个全局配置.

1. 查看jenv支持的插件列表

	```
	$> jenv plugins
	ant
	export
	golo
	gradle
	grails
	groovy
	lein
	maven
	sbt
	scala
	springboot
	vlt
	```
	
2. 启动插件

	```
	$> jenv enable-plugin maven
	maven plugin activated
	```

3. 停用插件

	```
	$> jenv disable-plugin maven
	maven disabled
	```

# 参考


* [jenv github](https://github.com/gcuisinier/jenv)

日期:2018-06-21 作者:[辵鵵](https://about.me/qyf404)