[](解决XMind运行卡顿)

# 问题

[XMind](https://www.xmind.cn/)是一款很好用的脑图工具,它是基于eclipse开发的,而且基础功能是免费的.最近我安装了XMind 8 Pro,但是发现在Mac上运行有卡顿.

# 解决方式

解决这个问题的思路也很简单,软件卡顿一般就是资源不够用了.这里的资源包括cpu和内存.
我观察我的机器,cpu并没有达到100%, 内存也没用完.然后我就猜测可能是JVM的初始内存分的太小了.

XMind是基于eclipse开发的,我直接找到了程序启动时的配置文件`/Applications/XMind.app/Contents/Eclipse/XMind.ini`,在文件末尾追加如下配置信息:

```
-XX:MaxPermSize=256m
-Xms1024m
-Xmx2048m
```

这些配置信息都是JVM的参数,意思就是调大XMind的启动内存和最大内存.

配置好后重启XMind,卡顿消失了.

# 参考

* [Overview eclipse.ini](https://wiki.eclipse.org/Eclipse.ini#-vm_value:_Mac_OS_X_Example)
* [How do I increase the permgen size available to Eclipse?](https://wiki.eclipse.org/FAQ_How_do_I_increase_the_permgen_size_available_to_Eclipse%3F)

[关于作者](https://about.me/qyf404)