[](如何查看Maven项目的jar包依赖)

![](./logos/maven_logo.png)

# 问题

十年以前写java项目总会干这么一个事情： 调包。 java项目往往依赖了很多第三方jar包，而这些jar包又有他自己依赖的第三方jar包，从而就能形成一个依赖树。 而程序运行要把这些所有的依赖都加载到内存中的， 如果有一个第三方jar包被依赖了两次或两次以上， 而且版本还不一样，就会依赖冲突，此时就需要调包， 调包就是调整依赖包。 调包其实是一个很繁琐和头疼的事情。

然后maven出现了，我第一次接触maven就让他的依赖管理吸引了，这让程序员从调包这种苦力活中解脱出来了。虽然maven的依赖管理功能降低了我们的调包成本，但是发生依赖冲突时，还是需要人工参与的。这时我们就需要知道项目对第三方jar包的依赖情况。

# 解决

maven提供了一个命令`mvn dependency:tree`可以直接打印出依赖树。


```
[INFO] com.qyf404:test-spring-boot:jar:1.0.0
[INFO] +- org.springframework.boot:spring-boot-starter:jar:1.5.4.RELEASE:compile
[INFO] |  +- org.springframework.boot:spring-boot:jar:1.5.4.RELEASE:compile
[INFO] |  |  \- org.springframework:spring-context:jar:4.3.9.RELEASE:compile
[INFO] |  +- org.springframework.boot:spring-boot-autoconfigure:jar:1.5.4.RELEASE:compile
[INFO] |  +- org.springframework.boot:spring-boot-starter-logging:jar:1.5.4.RELEASE:compile
[INFO] |  |  +- ch.qos.logback:logback-classic:jar:1.1.11:compile
[INFO] |  |  |  \- ch.qos.logback:logback-core:jar:1.1.11:compile
[INFO] |  |  +- org.slf4j:jcl-over-slf4j:jar:1.7.25:compile
[INFO] |  |  +- org.slf4j:jul-to-slf4j:jar:1.7.25:compile
[INFO] |  |  \- org.slf4j:log4j-over-slf4j:jar:1.7.25:compile
[INFO] |  +- org.springframework:spring-core:jar:4.3.9.RELEASE:compile
[INFO] |  \- org.yaml:snakeyaml:jar:1.17:runtime

```

日期:2018-09-13 作者:[辵鵵](https://about.me/qyf404)