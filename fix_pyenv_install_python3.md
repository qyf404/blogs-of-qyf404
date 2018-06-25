[](解决在Mac上用pyenv安装python3失败)

![python](./logos/python_logo.png)

# 背景

前段时间在本地Mac系统上要跑一个python3写的压测脚本. Mac默认安装的是python2, 而且很多软件依赖的也是python2. 为了不影响现有系统其它软件, 当时安装了pyenv来实现多个python版本管理. 然后就遇到问题了. 

在执行命令`pyenv install -v 3.6.5`安装python3时失败了, 报错内容如下:

```
$> pyenv install -v 3.6.5
...
zipimport.ZipImportError: can't decompress data; zlib not available
```

# 解决

解决方法也很简单, 就是更新两个软件:

1. 更新xcode

	```
	$> xcode-select --install
	```
2. 安装zlib

	```
	$> brew install zlib
	```

之后在重新执行`pyenv install -v 3.6.5`就成功将python3安装上了.



# 参考

* [利用pipenv和pyenv管理多个相互独立的Python虚拟开发环境](https://blog.csdn.net/liuchunming033/article/details/79582617)
* [使用 pyenv + virtualenv 打造多版本python开发环境](http://pylixm.cc/posts/2016-06-19-Virtualenv-install.html)
* [pipenv 试用过程分享](http://pylixm.cc/posts/2018-01-13-python-pipenv.html)
* [mac python install zlib not available](http://www.cnblogs.com/zxpo/p/5228015.html)

日期:2018-06-25 作者:[辵鵵](https://about.me/qyf404)