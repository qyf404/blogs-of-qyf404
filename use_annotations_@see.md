[](使用java中的注解@see)

![](./logos/java_logo.png)

# 缘起

在写java时,有时需要写注释,而为了更好的描述,需要引用和参考其他代码.为了让阅读者更好的体验,javadoc中支持链接跳转,这就需要用到注解`@see`.

# @see用法

注解`@see`可以在注释中实现链接跳转.`@see`可以指向包,类,方法,属性.

一个完整的使用方法是这样的`@see package.class#member`.


Typical forms for @see package.class#member 

如果指向的在当前类中,可以只写井号后面的.

```
@see #field
@see #method(Type, Type,...)
@see #method(Type argname, Type argname,...)
@see #constructor(Type, Type,...)
@see #constructor(Type argname, Type argname,...) 
```

如果指向的在当前包中,可以省略包名.

```
@see Class#field
@see Class#method(Type, Type,...)
@see Class#method(Type argname, Type argname,...)
@see Class#constructor(Type, Type,...)
@see Class#constructor(Type argname, Type argname,...)
@see Class.NestedClass
@see Class 
```

如果在其他包中,需要指向全路径.

```
@see package.Class#field
@see package.Class#method(Type, Type,...)
@see package.Class#method(Type argname, Type argname,...)
@see package.Class#constructor(Type, Type,...)
@see package.Class#constructor(Type argname, Type argname,...)
@see package.Class.NestedClass
@see package.Class
@see package
```

# 参考

* [Java Platform, Standard Edition Tools Reference](https://docs.oracle.com/javase/8/docs/technotes/tools/windows/javadoc.html)

[关于作者](http://about.me/qyf404)