## Linux远程管理

| 命令 | 说明 |
|:-|:-|
| `logout [n]`|注销登录的shell。不在shell执行报错|
| [`shutdown [-options] [time]`](#1shutdown命令)|关机/重启|
| [`ifconfig [-options]`](#2ifconfig命令)|查看或配置网卡信息|
| [`ping [-options] destination`](#3ping命令)|检测到目标地址连接通讯是否正常|
| [`ssh [-options] [user@hostname]`](#4ssh命令)|远程连接服务器|
| <a href='#5scp命令'>`scp [-options] [[user@]host1:]file1 [[user@]host2:]file2`</a>|远程复制文件|

### 1.shutdown命令
```
# 命令格式
$ shutdown [-options] [time]
```
* `shutdown`命令一般需要root权限执行
* 远程维护服务器时，最好不要关闭系统(关闭后启动不方便)，一般选择重启系统

##### 1) options

options | 含义
:-|:-
缺省 |  默认行为为关机
`-r` | 重新启动。`shutdown -r now` 等价于 `reboot`
`-c` | 取消代执行的关机或重启任务，必须在关机或重启之前执行

##### 2) time

options | 含义
:-|:-
缺省 |  默认为1分钟后执行
`+m` | `m`分钟后执行
`now` | 现在立即执行
`hh:mm`| 指定时间执行,只能设置小时和分钟

```
# 1分钟后关机
$ shutdown

# 立即重启
$ shutdown -r now

# 10分钟后关机
$ shutdown +10

# 今天20:30 重启
$ shutdown -r 20:30

# 取消关机或重启任务
$ shutdown -c
```

### 2.ifconfig命令
```
# 命令格式
$ ifconfig [-options]
```

* 如果命令不存在，需要先进行安装`sudo apt install net-tools`
* 一台计算机可能有一个物理网卡和多个虚拟网卡，在Linux中物理网卡名字通常为`ensXX`

```
# 查看网卡配置信息
$ ifconfig

# 过滤查看IP地址
$ ifconfig | grep inet
```

### 3.ping命令
```
# 命令格式
$ ping [-options] destination
```

* `ping`一般用于检测当前计算机到目标计算机之间的网络是否通畅。我们给目标IP发送一个数据包，对方返回一个包，根据返回包的时间我们可以确定目标计算机是否存在并且工作正常以及网络链接速度。
* Linux中`ping`命令不会自动停止，可以使用`Ctrl + C`退出。
* `ping 127.0.0.1` 可以测试本机网卡是否正常工作 

### 4.ssh命令
#### 4.1 ssh基础使用
* ssh客户端是一种使用`Secure Shell(ssh)`协议连接到运行了ssh服务端的远程服务器上。
* ssh是目前较可靠，专为远程登录会话和其他网络服务提供安全性的协议
    * 有效防止远程管理过程中的信息泄漏
    * 传输 **数据加密**，能够防止DNS和IP欺骗
    * 传输 **数据压缩**，加快传输速度
* Mac和Linux中默认已安装ssh客户端，可直接在终端中使用ssh命令。Windows则需手动安装ssh客户端，较常用的Windows SSH客户端有`PuTTY`和`XShell`

```
# 命令格式
$ ssh [-options] [user@hostname]
```
* `user`远程服务器登录的用户名，默认为当前用户
* `hostname`远程服务器地址。可以是IP/域名/别名
* ssh server监听的端口号默认为`22`,默认端口可以省略。若为其他端口可通过`-p`指定。
* `exit`或`logout`命令均可退出当前登录

```
# 以colin用户登录192.168.1.196的到ssh服务器
$ ssh colin@192.168.1.196

# 以colin用户登录到192.168.1.198的ssh服务器，使用2222端口
$ ssh -p 2222 colin@192.168.1.198 
```

#### 4.2 ssh高级配置
ssh配置信息都保存在`~/.ssh` 中

配置文件|作用
:-|:-
known_hosts|作为客户端。记录曾连接服务器授权。ssh第一次连接一台服务器会有一个授权提示，确认授权后会记录在此文件中，下次连接记录中的服务器时则不再需要进行授权确认提示
authorized_keys|作为服务端。客户端的免密连接公钥文件
config|作为客户端。记录连接服务器配置的别名

##### 1) 服务器别名
远程管理命令(如ssh,scp等)连接一台服务器市一般都需要提供 服务器地址、端口、用户名 ，每次输入比较繁琐，我们可以把经常使用的服务器连接参数打包记录到配置文件中并为其设置一个简单易记的别名。这样我们就可以通过别名方便的访问服务器，而不需要提供地址、端口、用户名等信息了。

配置方法如下：
* 创建或打开 `~/.ssh/config`，在文件追加服务器配置信息
* 一台服务器配置格式如下
```
Host ColinMac
    HostName 192.168.1.196
    User colin
    Port 22
```
配置完成后远程管理命令中就可以直接使用别名访问了，如
```
$ ssh ColinMac
$ scp 123.txt ColinMac:Desktop
```

##### 2) 免密登录
* 远程管理命令(如ssh,scp等)每次都需要提供用户密码保证安全。除此之外，我们也可配置使用非对称加密方式，避免每次输入密码
* 配置免密登录后，ssh连接和scp等远程管理命令都不需要再输密码
* 配置只有以下两步：

```
# 客户端生成密钥对
$ ssh-keygen

# 上传公钥到服务器
ssh-copy-id user@hostname   # 文件会自动上传为服务器特定文件 ～/.ssh/authorized_keys
```

### 5.scp命令
```
# 命令格式
$ scp [-options] [[user@]host1:]file1 [[user@]host2:]file2
```

* `scp`是secure copy缩写，可以在Linux中远程拷贝文件.以ssh连接方式书写远程地址，拷贝于`cp`命令类似。
* `scp`命令只能在Unix内核系统(如Linux/mac OS) 中运行。在Windows中文件传输推荐使用FTP工具,如FileZilla

options|含义
:-|:-
`-r`|远程拷贝文件或递归拷贝目录
`-P`|指定远程服务器端口号，默认22端口可以省略

```
# 将本地123.txt远程拷贝到192.168.1.196服务器的colin用户的Desktop目录并重命名为test.txt
$ scp 123.txt colin@192.168.1.196:Desktop/test.txt

# 将192.168.1.196服务器的colin用户的Desktop/test.txt远程拷贝到本地当前目录并重命名为123.txt
$ scp conlin@192.168.1.196:Desktop/test.txt 123.txt

# 将本地~/Desktop/Python拷贝到192.168.1.196服务器的colin用户的Desktop目录下
$ scp -r ~/Desktop/Python colin@192.168.1.196:Desktop
```
