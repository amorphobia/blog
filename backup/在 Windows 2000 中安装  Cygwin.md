# Windows 2000

这应该是一部分人第一次使用的 NT 内核的 Windows 系统，已于 2010 年停止支持。而 Cygwin 也已停止对 Windows XP / 7 / 8 的支持，更别说 Windows 2000 了。

# Cygwin Time Machine

有怀旧者创建了 [Cygwin Time Machine](http://www.crouchingtigerhiddenfruitbat.org/Cygwin/timemachine.html) (CTM)，保留了 Cygwin 以及软件仓库的历史，旧系统可以在这里找到对应的最后支持版本。

## 安装

一般来说，下载对应的 setup.exe，对照 CTM 的教程安装即可；比如这里 Windows 2000 对应的安装包 [setup-2.774.exe](http://ctm.crouchingtigerhiddenfruitbat.org/pub/cygwin/setup/snapshots/setup-2.774.exe) 下载后，附加 `-X` 选项启动以跳过签名检查，并使用以下服务器链接地址：

```
http://ctm.crouchingtigerhiddenfruitbat.org/pub/cygwin/circa/2013/06/04/121035
```

但有两个小问题可能需要注意，第一个是有可能会卡死在搜索服务器的一步，解决方法是不要用直接连接 (Direct Connection)，而是使用 IE 代理设置 (Use Internet Explorer Proxy Settings)，即使 IE 代理就是直接连接……

第二个问题是这版 Cygwin 的 setup 程序在安装后会报错，查看日志会发现以下信息：

```
/usr/bin/update-desktop-database: No such file or directory
/usr/bin/update-mime-database: No such file or directory
```

解决方案很简单，安装 `desktop-file-utils` 和 `shared-mime-info` 这两个包即可。