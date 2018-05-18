
[](解决MAC下修改系统文件没权限的问题)

# 问题

用brew在mac上可以轻松的管理软件, 不过最新的mac系统升级后, `brew`执行`update`命令时会报权限不足的错误, 而且会提示执行命令`sudo chown -R $(whoami) /usr/local`来赋权. 但是执行该命令后依然会报权限不足`Operation not permitted`.

# 解决

解决方法其实就是要让用户具有对`/usr/local`目录的读写权限. 解决这个问题要先进入系统恢复界面, 然后关闭一个安全策略,具体步骤如下:

1. 重启电脑时按住 `Command+R`两个按键, 就能进入恢复模式.
2. 在菜单栏找到`Terminal`, 并打开.
3. 执行命令`csrutil disable`, 关闭这个Rootless策略.
4. 执行命令`reboot`重启电脑.
5. 正常开机后执行命令`sudo chown -R $(whoami) /usr/local`赋权.
6. 此时可以正常执行命令`brew update`了.
7. 在按照第一步进入恢复模式, 执行命令`csrutil enable`来开启Rootless策略.

[关于作者](https://about.me/qyf404)