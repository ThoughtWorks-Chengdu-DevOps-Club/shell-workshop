## Tldr

厌烦了ManPage冗长的说明，试试Tldr吧，它能罗列出命令的常用用法，出家旅行必备良品。

现在支持通过npm,gem,pip,brew等多种包管理器安装。安装详情请参照官网<http://tldr-pages.github.io/>

你可以通过简单的`tldr <查询命令>`的方式调用，如`tldr tar`的形式。

## Ping

ping命令经常用来测试网络的连通性。

可以用以下两种方式使用ping命令。

```bash
ping ip地址
ping 域名（如www.baidu.com)
```

ps: ping命令使用了ICMP协议。

## SSH

ssh全称Secure Shell，专为提供安全的远程登陆会话实现的协议。几乎所有的linux系统都带有ssh客户端环境。

### 简单的使用ssh登陆远程主机

```
ssh [用户名（默认和本地用户使用相同用户名）]@远程主机域名或者ip [-p 端口|默认22]
```

比如`ssh vagrant@192.168.33.10`

### 免密码登陆

ssh可以通过设置证书认证的方式免密码登陆。

证书生成

```bash
ssh-keygen [-f 证书名字（默认为id_rsa）] 
```

生成的证书一般在HOME目录下的`.ssh`目录下。默认会生成两份文件，一份私钥，一份公钥。

而生成的公钥需要放置到远程主机的`authorized_keys`文件里。

可以通过`scp`和`cat`加上`IO重定向`实现这个过程。
比如

```bash
scp .ssh/公钥文件（默认以.pub结尾）vagrant@192.168.33.10:.ssh
ssh vagrant@192.168.33.10
cat .ssh/公钥文件 >> .ssh/authorized_keys
```

或者通过`ssh-copy-id`命令直接完成这个过程，这个命令只有linux系统中才有。

```
ssh-copy-id -i 公钥文件 vagrant@192.168.33.10
```

然后你就可以通过`ssh`命令登陆而无须输入密码了。

```
ssh [-i 私钥文件（如果生成的证书不是id_rsa）] vagrant@192.168.33.10
```

### 别名登陆

通过配置`ssh`可以进一步简化远程登陆的命令。

你只需要在`.ssh`目录下创建`config`文件，然后编写类型下面的内容。

```
Host 你指定的别名
  HostName  远程主机的地址
  User  远程主机登陆用户名
  IdentityFile 指定登陆的私钥
```

然后你就可以使用类似`ssh aws`这样的短命令登陆远程主机了。

## Curl

curl几乎可以模拟一切的网络请求，我们这里学习它的简单用法。

获取网页内容

```
curl 网址
```

比如
```
curl https://www.baidu.com
```

下载文件

```
curl 远程文件地址 -o 存储地址
```

比如

```
curl https://apache.../tomcat.tar.gz -o tomcat.tar.gz
```

## Tar

tar是一个打包命令，经常还会和压缩操作一起进行。

### 打包

```bash
tar cvzf backup.tar.gz file1 file2....
```

cvzf是tar的参数，c是create，指定这是一个打包操作，v是verbose，指定打包明细都输出到控制台，z是gzip，指压缩算法是gzip，f是file，指定打包后文件名称，如上面的`backup.tar.gz`

最后一些列`file[1-...]`是指定要被打包的文件，可以使用通配符。

### 解包

和打包类似，除了将打包参数c换成解包参数x之外。

```bash
tar xvzf backup.tar.gz [-C 解包存放的目录地址，默认当前目录]
```

# 权限控制

Linux 基本的文件权限，可以通过 `ls -l` 查看。对于一个文件的权限，运行此命令我们可以在最前面看到诸如 rwxr-xr-- 这种标志。其自左至右大致可以分为三组，分别是 user, user group, others 控制的权限。权限有 read, write , execute 三种，以 r, w , x 标志。

我们可以通过 `chmod` 更改文件的权限。如果我们将这三种权限认为是一个三位的二进制数即可以得到一个对应的权限数字，例如 r-x 就可以看做 101, 也就是十进制数字 5。对应到三个组上， rwxrwxrwx 便是 777 权限。 我们可以通过 `chmod 777` 来为其赋值。

除此之外， chmod 命令还有很多其他用法，例如 `chmod u+x` 便可以迅速给 user 增加可以执行的权限。

与 chmod 对应的，我们可以通过 `chown`, `chgrp` 来更改文件的所有者和组。

除了基本的三种权限， Linux 还有 setuid , setgid, stick bit 三种特殊权限。请自行查阅


# netstat

当我们想要获取当前的网络状况时， `netstat`, 也就是 network status。是简单好用的命令。

常用的组合例如 `netstat -apn` ， 会获得所有端口（all), 进程的信息（process）,并将端口以数字的形式显示（numeric)


# ps , grep, pipe

process status , 也即是 `ps` 命令，是我们获得当前进程快照的常用命令。但是其默认情况下，仅显示当前用户在前台的进程信息。我们一般使用 ps aux 来获得所有用户的所有进程信息。

如果我们相对其进行筛选，常常搭配使用的是 `grep` 命令，即是 Global search REgular expression and Print out the line。可以使用正则表达式来进行搜索匹配。

为了将 ps 的输出，作为 grep 的输入。我们通常使用 \*nix 的管道系统，也即是 | 操作符。它便可以把管道左边，作为右边的输入。

eg:
` ps aux | grep java `便可以找到所有含有 java 的进程。


# kill

kill 命令后街 pid ，用来杀死进程。但是我们不能简单把它认为是一个仅仅可以用来杀死进程的工具。通过指定其发送给目标进程的信号量。我们还可以做额外的事情。常用的信号量有：

    1       HUP (hang up)
    2       INT (interrupt)
    3       QUIT (quit)
    6       ABRT (abort)
    9       KILL (non-catchable, non-ignorable kill)
    14      ALRM (alarm clock)
    15      TERM (software termination signal)

譬如。我们发送给 tomcat 一个 ` kill -3 ` 便可以让 JVM 执行正常的 thread dump 流程，为我们的日志提供更多信息。


# 后台运行
譬如我们打开了一个 vim ，此时却想切去其他目录做一些事情。这里提供一种办法。

使用 `ctrl + z` 将其挂起，需要时直接 `fg` 就可以唤回。

这方面相关的便是 `fg` （前台运行）, `bg`（后台运行）, `ctrl+z`（挂起）， 以及 `command &`（ 后台运行 ）。以及通过 `jobs` 来查看这些任务。不再赘言。

# 日志查看和打包
使用 tail -n 来查看最后 n 行后的日志。
使用 du, disk usage 命令来查看占用的磁盘量。通常使用 ` du -sh filename ` 来获得一个 file 的 size, 并以人易读的方式展示。
如果我们发现日志文件已经过大，想要打包，可以继续使用之前提到的 tar 命令。
` tar czvf filename files ` 是常用的命令。参数分别是 (c)ompress, (z)ip, (v)erbose, (f)ile。

# 批量重命名

我们可以通过 ‘sed’ ，达到字符替换的效果。它是 stream editor ， 流编辑器的简称。其最基本的用法为
`sed 's/find/replace/' filename`, 可将文件中第一次出现的某种模式替换掉。我们用它来对一百个形如 {1..100}.torrent 的文件进行命名为对应的 .jpg 文件。

首先 `ls | grep torrent`

获得所有 torrent 文件的名字。然后继续 `| while read line;` 按行读入。
进入 pipe while 模式。可以较为方便的输入循环体内容。
输入 do <enter>, mv $line \`echo $line | sed s/torrent/jpg\`<enter> done;
即可。

其实可以直接通过 `rename` 命令进行更改。我们这里只是为了展示一种更清晰的写 while 循环的方式。
