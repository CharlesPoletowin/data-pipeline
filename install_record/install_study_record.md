# Linux之/etc/fstab文件讲解
/etc/fstab是用来存放文件系统的静态信息的文件。位于/etc/目录下，可以用命令less /etc/fstab 来查看，如果要修改的话，则用命令 vi /etc/fstab 来修改。
当系统启动的时候，系统会自动地从这个文件读取信息，并且会自动将此文件中指定的文件系统挂载到指定的目录。

<br/>

/etc/fstab文件主要包括6段，依次是：
```
<file system>　　<dir>　　<type>　　<options>　　<dump>　　<pass>
```
```
<file system> 要挂载的分区或存储设备
<dir>  挂载的目录位置
<type> 挂载分区的文件系统类型，比如：ext3、ext4、xfs、swap
<options> 挂载使用的参数有哪些。举例如下：
        auto - 在启动时或键入了 mount -a 命令时自动挂载。
        noauto - 只在你的命令下被挂载。
        exec - 允许执行此分区的二进制文件。
        noexec - 不允许执行此文件系统上的二进制文件。
        ro - 以只读模式挂载文件系统。
        rw - 以读写模式挂载文件系统。
        user - 允许任意用户挂载此文件系统，若无显示定义，隐含启用 noexec, nosuid, nodev 参数。
        users - 允许所有 users 组中的用户挂载文件系统.
        nouser - 只能被 root 挂载。
        owner - 允许设备所有者挂载.
        sync - I/O 同步进行。
        async - I/O 异步进行。
        dev - 解析文件系统上的块特殊设备。
        nodev - 不解析文件系统上的块特殊设备。
        suid - 允许 suid 操作和设定 sgid 位。这一参数通常用于一些特殊任务，使一般用户运行程序时临时提升权限。
        nosuid - 禁止 suid 操作和设定 sgid 位。
        noatime - 不更新文件系统上 inode 访问记录，可以提升性能。
        nodiratime - 不更新文件系统上的目录 inode 访问记录，可以提升性能(参见 atime 参数)。
        relatime - 实时更新 inode access 记录。只有在记录中的访问时间早于当前访问才会被更新。（与 noatime 相似，但不会打断如 mutt 或其它程序探测文件在上次访问后是否被修改的进程。），可以提升性能。
        flush - vfat 的选项，更频繁的刷新数据，复制对话框或进度条在全部数据都写入后才消失。
        defaults - 使用文件系统的默认挂载参数，例如 ext4 的默认参数为:rw, suid, dev, exec, auto, nouser, async.
<dump>  dump 工具通过它决定何时作备份. dump 会检查其内容，并用数字来决定是否对这个文件系统进行备份。 允许的数字是 0 和 1 。0 表示忽略， 1 则进行备份。大部分的用户是没有安装 dump 的 ，对他们而言 <dump> 应设为 0。

<pass> fsck 读取 <pass> 的数值来决定需要检查的文件系统的检查顺序。允许的数字是0, 1, 和2。 根目录应当获得最高的优先权 1, 其它所有需要被检查的设备设置为 2. 0 表示设备不会被 fsck 所检查。
```
---
# vim强制退出命令
```
:q!
```
---
# curl
## curl 的用法指南
curl 是常用的命令行工具，用来请求 Web 服务器。它的名字就是客户端（client）的 URL 工具的意思。

它的功能非常强大，命令行参数多达几十种。如果熟练的话，完全可以取代 Postman 

### 不带有任何参数时，curl 就是发出 GET 请求。

    $ curl https://www.example.com

上面命令向www.example.com发出 GET 请求，服务器返回的内容会在命令行输出。

### -A

-A参数指定客户端的用户代理标头，即User-Agent。curl 的默认用户代理字符串是curl/[version]。
```

$ curl -A 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.100 Safari/537.36' https://www.google.com
```

    curl -A '' https://google.com

上面命令会移除User-Agent标头。

可以通过-H参数直接指定标头，更改User-Agent。

    $ curl -H 'User-Agent: php/1.0' https://google.com

<br>
# apt-get update
配置了Kubernetes的apt-get源之后，需要执行apt-get update命令进行更新