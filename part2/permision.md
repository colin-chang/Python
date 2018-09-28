# 用户(组)权限管理

* [1. 用户组管理](#1-用户组管理)
* [2. 用户管理](#2-用户管理)
* [3. 权限管理](#3-权限管理)

## 1. 用户组管理

用户组可以方便批量管理用户，每个用户必须属于一个组或多个组。为一个组赋权限后，组中所有用户自动获得相应权限

命令 | 说明
:-|:-
`groupadd [-options] GROUP` | 添加用户组
`groupdel [-options] GROUP` | 删除用户组
`cat /etc/group` | 查看系统所有用户组
`chgrp [-options] GROUP FILE`|修改文件或目录所在组。`-R`表示递归修改

```
# 添加dev用户组
$ sudo groupadd dev

# 删除dev用户组
$ sudo groupdel dev

# 查看系统用户组
$ cat /etc/group

# 将Code目录及其子目录和文件所在组递归修改为dev
$ sudo chgrp -R dev Code
```

## 2. 用户管理

### 2.1 root用户
* Linux系统中`root`账号通常用于系统维护和管理，其对操作系统所有资源 **具有所有访问权限**
* Linux安装过程中，系统会自动创建一个用户账号，而这个默认用户称为“标准用户”
* 标准用户执行命令没有权限时，可以在命令开始添加`sudo`来执行。
* `sudo`命令用来以其他身份来执行命令，预设的身份为`root`
* 用户使用`sudo`时需要输入密码，密码5分钟有效 
* 只有`sudo`用户组中用户才可使用`sudo`,未经授权用户使用`sudo`会发警告邮件给管理员

```
sudo rm -r Application      # 以管理员身份删除Application目录
```

---

命令 | 说明 
:-|:-
[`useradd [-options] user`](#1-useradd命令)|创建新用户
[`passwd [-options] [user]`](#2-passwd命令)|设置用户密码
`userdel [-options] [user]`|删除用户。`-r`可以**连同删除用户主目录**
[`cat /etc/passwd`](#1-passwd文件)|查看系统所有用户信息
[`id [-options] [user]`](#2-id命令)|查看用户和所在组信息
`who [-options]`| 查看当前登录到系统的活跃用户
`whoami`| 查看当前用户名
[`usermod [-options] user`](#2-usermod命令)|修改用户信息
[`su [-options] [user]`](#54-切换用户)|切换用户

### 2.2 创建用户
#### 1) useradd命令
```
# 命令格式
$ useradd [-options] user
```
options|含义
:-|:-
`-m`|自动创建用户主目录
`-g`|指定用户所属组。**若不指定会自动创建一个用户组**

创建用户最好添加`-m`创建用户主目录，否则后续创建目录、设置权限非常繁琐。如果忘记，**最简单的方法就是立即删除用户，重新创建**

```
# 创建colin用户并分配到dev组，同时创建用户主目录
$ sudo useradd -m -g dev colin
```

#### 2) passwd命令
```
# 命令格式
$ passwd [-options] [user]
```

* `passwd`不指定用户默认给当前用户修改密码
* 创建用户后必须设置用密码之后才可以登录

```
# 给colin设置密码
$ passwd colin
```

### 2.3 查看用户信息
#### 1) passwd文件
`/et/passwd`文件存在的是所有用户的信息，每条记录是一个用户，记录7个字段，以":"分割，形如
```
$ cat /etc/passwd

# 结果示例
# colin:x:1000:1000:Colin Chang,,,:/home/colin:/bin/bash
# test:x:1001:1001::/home/test:/bin/sh
```

用户名|密码|UID|GID|用户全名或本地账号|主目录|Shell
:-|:-|:-|:-|:-|:-|:-
colin|x|1000|1000|Colin Chang,,,|/home/colin|/bin/bash
test|x|1001|1001||/home/test|/bin/sh

* 密码`x`表示密码加密不显示，即使没有设置密码
* UID是用户唯一的ID标识
* GID是用户主组唯一的ID标识
* Shell表示用户登录时使用的终端。可[修改shell](#2-修改shell)
* 没有内容的项全部留空

#### 2) id命令
```
# 命令格式
$ id [-options] [user]
```
`id`命令可以快速方便的查看用户和所在组信息。不指定用户默认显示当前用户信息。命令执行结果示例如下：

```
# 查看colin用户信息
$ id colin

# 执行结果如下
# uid=1000(colin) gid=1000(colin) groups=1000(colin),4(adm),24(cdrom),27(sudo)
```
groups列出了用户的所有所在组(主组+附加组)。gid为主组ID

#### 3) 其他相关命令
```
# 查看登录到系统的活跃用户
$ who

# 查看当前当前登录用户
$ whoami
```

### 2.4 修改用户信息
#### 2.4.1 主组与附加组
一个用户可以隶属与多个组。其中至少包含一个主组和任意多个附加组。
* 主组：通常在用户创建时指定。主组ID在`/etc/passwd`的第四列GID。通过`id`命令快速查看主组信息
* 附加组：用于指定 **用户附加权限**。在`/etc/group`中最后一列表示该组所有用户列表。通过`id`命令结果`groups`可以快速查看所有所在组(主组+附加组)

#### 2.4.2 usermod命令
```
# 命令格式
$ usermod [-options] user
```
`usermod`命令可以修改用户信息，如用户主组、附加组、主目录、shell等。

options|含义
:-|:-
`-g`|修改用户主组。用户创建后一般不会修改主组
`-G`|修改用户附加组
`-s`|修改shell

##### 1) 修改组
* 修改用户组后需要**重新登录**才能生效
* useradd添加的用户默认无法以`root`身份执行命令。若要使用可以将用户附加到`sudo`中

```
# 将colin用户附加到sudo组中
$ sudo usermod -G sudo colin
```
##### 2) 修改Shell
* Ubuntu中`useradd`添加的用户默认shell为dash(`/bin/sh`)，而系统用户使用的shell默认为bash(`/bin/bash`)。bash在颜色渲染和使用上更加方便。
* 修改用户Shell后需要重新登录方可生效

```
# 修改test用户的shell为bash
$ sudo usermod -s /bin/bash test
```

### 2.5 切换用户
```
# 命令格式

# 切换用户
$ su [-options] [user]

# 退出当前登录用户
$ exit
```

* `options`为`-`或`-l`会在切换用户时同时切换到用户主目录
* `user`为空会切换到`root`用户。不安全，不推荐
* 切换和退出用户示意图：

<img src='../img/suexit.jpg' width='450'>

```
# 切换test用户并切换到其主目录(/home/test)
$ su - test

# 退出test用户
$ exit

# 切换root用户
$ su
```

## 3. 权限管理
### 3.1 权限介绍

权限|数字代码|缩写|英文
:-|:-|:-|:-
读|4|r|read
写|2|w|write
执行|1|x|execute
无权限|0||

**目录**可读权限表示是否可以读取目录下内容，可写表示是否可以修改目录下内容，**可执行表示是否可以在此目录执行命令**

### 3.2 修改权限
Linux修改权限有一下三种方式:

命令|作用
:-|:-
[`chown [-options] [owner] FILE`](#321-chown命令) |修改所有者
[`chgrp [-options] GROUP FILE`](#1用户组管理) |修改所在组
[`chmod [-options] MODE FILE`](#322-chmod命令)|直接修改权限

#### 3.2.1 chown命令
```
# 将123.txt所有者修改为test用户
$ sudo chown test 123.txt

# 将code目录所有者修改为test用户
# sudo chown test code
```

#### 3.2.2 chmod命令
`chmod`是change mode缩写，其功能是修改用户(组)对文件或目录的权限
##### 1) 加减方式
```
# 命令格式
$ chmod +/-r|w|x file|dir
```

* 添加权限使用`+`,移除权限使用`-`
* 此种方式会同时修改 所有者、所在组、其他组 权限，不能精确修改三者各自权限

```
chmod +x 123.txt    # 添加123.txt文件的可执行权限
chmod -rw demo      # 移除demo目录的读写权限
```

##### 2) 数字方式
```
# 命令格式
$ chmod [-options] xxx file
```
* `-R`表示递归修改文件目录权限
* `chmod`可以简单的使用三个数字分别设置 **拥有者/所在组/其他组**权限
* `xxx`代表三个0-7的数字。**每位数字含义为各部分权限数字代码的和值**。权限数字代码结构如下表
<table style='text-align:center'>
<tr><th colspan='3'>所有者权限</th><th colspan='3'>所在组权限</th><th colspan='3'>其他组权限</th></tr>
<tr><td>r</td><td>w</td><td>x</td><td>r</td><td>w</td><td>x</td><td>r</td><td>w</td><td>x</td></tr>
<tr><td>4</td><td>2</td><td>1</td><td>4</td><td>2</td><td>1</td><td>4</td><td>2</td><td>1</td></tr>
</table>
<table style='text-align:center'>
<tr><th>权限</th><td rowspan='9'></td><th colspan='3'>各位权限码</th><th rowspan='9'></th><th>权限值</th></tr>
<tr><td>rwx</td><td>4</td><td>2</td><td>1</td><td>7</td></tr>
<tr><td>rw-</td><td>4</td><td>2</td><td>0</td><td>6</td></tr>
<tr><td>r-x</td><td>4</td><td>0</td><td>1</td><td>5</td></tr>
<tr><td>r--</td><td>4</td><td>0</td><td>0</td><td>4</td></tr>
<tr><td>-wx</td><td>0</td><td>2</td><td>1</td><td>3</td></tr>
<tr><td>-w-</td><td>0</td><td>2</td><td>0</td><td>2</td></tr>
<tr><td>--x</td><td>0</td><td>0</td><td>1</td><td>1</td></tr>
<tr><td>---</td><td>0</td><td>0</td><td>0</td><td>0</td></tr>
</table>

* 常见权限组合有
<table style='text-align:center'>
<tr><th>权限组合</th><th colspan='3'>所有者权限</th><th colspan='3'>所在组权限</th><th colspan='3'>其他组权限</th></tr>
<tr><td>777</td><td>r</td><td>w</td><td>x</td><td>r</td><td>w</td><td>x</td><td>r</td><td>w</td><td>x</td></tr>
<tr><td>755</td><td>r</td><td>w</td><td>x</td><td>r</td><td>-</td><td>x</td><td>r</td><td>-</td><td>x</td></tr>
<tr><td>644</td><td>r</td><td>w</td><td>-</td><td>r</td><td>-</td><td>-</td><td>r</td><td>-</td><td>-</td></tr>
</table>

```
# 递归修改ColinBlog目录的权限为 所有者可读可写可执行,所在组可读可执行,其他组可读可执行
$ chmod -R 755 ColinBlog
```