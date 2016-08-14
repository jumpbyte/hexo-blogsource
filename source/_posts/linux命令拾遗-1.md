---
title: linux命令拾遗(1)-whatis info man which whereis
date: 2016-08-07 14:47:17
categories: linux
tags: ['linux','linux命令']
---

### 简述

接触和学习过linux的同学相比都知道linux命令的繁多和复杂，无论是linux初学者还是老司机，也不可能把所有linux的命令都记住，好在linux有一一些强大的帮助命令，让我们不用担心不熟悉或者暂时遗忘的命令的情况，下面我们来看怎么去正确的使用这些命令
<!--more-->

>1. 在只记得部分关键字的时候，可以通过man -k 来搜索
>2. 查看某个命令的简单功能描述，可以通过whatis
>3. 查看某个命令更详细的说明可以用info 命令
>4. 要查看某个命令的具体位置，可以使用which
>5. 对于要查看某个命令的具体参数的含义及使用方法，可以借助强大的man命令

### whatis

whatis命令是用于查询一个命令执行什么功能，并将查询结果打印到终端上

>whatis命令在用catman -w命令创建的数据库中查找command参数指定的命令、系统调用、库函数或特殊文件名。whatis命令显示手册部分的页眉行。然后可以发出man命令以获取附加的信息。whatis命令等同于使用man -f命令

#### 用法

```
whatis 命令名称
```

#### 使用

```
[root@centos etc]# whatis cp
cp                   (1)  - copy files and directories
[root@centos etc]# whatis ls
ls                   (1)  - list directory contents
[root@centos etc]# whatis chown
chown                (1)  - change file owner and group
[root@centos etc]# whatis rm
rm                   (1)  - remove files or directories
```

### info

查看某个命令更详细的说明

>就内容来说，info页面比man page编写得要更好、更容易理解，也更友好，但man page使用起来确实要更容易得多。一个man page只有一页，而info页面几乎总是将它们的内容组织成多个区段（称为节点），每个区段也可能包含子区段（称为子节点）。理解这个命令的窍门就是不仅要学习如何在单独的Info页面中浏览导航，还要学习如何在节点和子节点之间切换。可能刚开始会一时很难在info页面的节点之间移动和找到你要的东西，真是具有讽刺意味：原本以为对于新手来说，某个东西比man命令会更好些，但实际上学习和使用起来更困难。

#### 用法
```
info [选项] [参数]
```
#### 选项列举
|命令选项|说明|
|:---:|:----|
|-d|添加包含info格式帮助文档的目录|
|-f|指定要读取的info格式的帮助文档|
|-n|指定首先访问的info帮助文件的节点|
|-o|输出被选择的节点内容到指定文件|


#### 使用

```
[root@centos etc]# info cp

File: coreutils.info,  Node: cp invocation,  Next: dd invocation,  Up: Basic operations

11.1 `cp': Copy files and directories
=====================================
`cp' copies files (or, optionally, directories).  The copy is
completely independent of the original.  You can either copy one file to
another, or copy arbitrarily many files to a destination directory.
Synopses:

     cp [OPTION]... [-T] SOURCE DEST
     cp [OPTION]... SOURCE... DIRECTORY
     cp [OPTION]... -t DIRECTORY SOURCE...

   * If two file names are given, `cp' copies the first file to the
     second.

   * If the `--target-directory' (`-t') option is given, or failing
     that if the last file is a directory and the
     `--no-target-directory' (`-T') option is not given, `cp' copies
     each SOURCE file to the specified directory, using the SOURCEs'
     names.

   Generally, files are written just as they are read.  For exceptions,
see the `--sparse' option below.
省略...
```
#### 快捷键
在info命令执行后处于帮助文档显示状态时，可以使用如下快捷操作

|快捷键|说明|
|:---:|:----|
|?键|它就会显示info的常用快捷键。
|N键|显示（相对于本节点的）下一节点的文档内容。
|P键|显示（相对于本节点的）前一节点的文档内容。
|U键|进入当前命令所在的主题。
|M键|敲M键后输入命令的名称就可以查看该命令的帮助文档了。
|G键|敲G键后输入主题名称，进入该主题。
|L键|回到上一个访问的页面。
|SPACE键|向前滚动一页。
|BACKUP或DEL键|向后滚动一页。
|Q|退出info。

### man

man命令是Linux下的帮助指令，通过man指令可以查看Linux中的指令帮助、配置文件帮助和编程帮助等信息

#### 用法

```
man [选项] [参数]
```

#### 选项列举

|命令选项|说明|
|:---:|:----|
|-a|在所有的man帮助手册中搜索；
|-f|等价于whatis指令，显示给定关键字的简短描述信息；
|-P|指定内容时使用分页程序；
|-M|指定man手册搜索的路径。

#### 使用

```shell
[root@centos etc]# man rm
RM(1)                            User Commands                           RM(1)

NAME
       rm - remove files or directories

SYNOPSIS
       rm [OPTION]... FILE...

DESCRIPTION
       This manual page documents the GNU version of rm.  rm removes each specified file.  By default, it does not remove directories.
....省略以下输出...       
```

### which

which命令用于查找并显示给定命令的绝对路径，环境变量PATH中保存了查找命令时需要遍历的目录。which指令会在环境变量$PATH设置的目录里查找符合条件的文件。也就是说，使用which命令，就可以看到某个系统命令是否存在，以及执行的到底是哪一个位置的命令。

#### 用法

```
which [选项] [参数]
```

#### 选项列举

|命令选项|说明|
|:---:|:----|
|-n<文件名长度>|制定文件名长度，指定的长度必须大于或等于所有文件中最长的文件名；
|-p<文件名长度>|与-n参数相同，但此处的<文件名长度>包含了文件的路径；
|-w|指定输出时栏位的宽度；
|-V|显示版本信息。

#### 使用

```
[root@centos etc]# which -V chown
GNU which v2.19, Copyright (C) 1999 - 2008 Carlo Wood.
GNU which comes with ABSOLUTELY NO WARRANTY;
This program is free software; your freedom to use, change
and distribute this program is protected by the GPL.
[root@centos etc]# which adduser
/usr/sbin/adduser
[root@centos etc]# which pwd
/bin/pwd
```
### whereis

whereis命令用来定位指令的二进制程序、源代码文件和man手册页等相关文件的路径。whereis命令只能用于程序名的搜索，而且只搜索二进制文件（参数-b）、man说明文件（参数-m）和源代码文件（参数-s）。如果省略参数，则返回所有信息。

>和find相比，whereis查找的速度非常快，这是因为linux系统会将 系统内的所有文件都记录在一个数据库文件中，当使用whereis和下面即将介绍的locate时，会从数据库中查找数据，而不是像find命令那样，通 过遍历硬盘来查找，效率自然会很高。 但是该数据库文件并不是实时更新，默认情况下时一星期更新一次，因此，我们在用whereis和locate 查找文件时，有时会找到已经被删除的数据，或者刚刚建立文件，却无法查找到，原因就是因为数据库文件没有被更新。

#### 用法

```
whereis [选项] [参数]
```

#### 选项列举

|命令选项|说明|
|:---:|:----|
|-b|只查找二进制文件；
|-B<目录>|只在设置的目录下查找二进制文件；
|-f|不显示文件名前的路径名称；
|-m|只查找说明文件；
|-M<目录>|只在设置的目录下查找说明文件；
|-s|只查找原始代码文件；
|-S<目录>只在设置的目录下查找原始代码文件；
|-u|查找不包含指定类型的文件。

#### 使用

```
[root@centos etc]# which pwd
/bin/pwd
[root@centos etc]# whereis pwd
pwd: /bin/pwd /usr/include/pwd.h /usr/share/man/mann/pwd.n.gz /usr/share/man/man1/pwd.1.gz
[root@centos etc]# whereis -b  pwd
pwd: /bin/pwd /usr/include/pwd.h
[root@centos etc]# whereis -s  pwd
pwd:
```

### 参考资料
1. linux命令手册大全：[http://man.linuxde.net/](http://man.linuxde.net/ 'linux命令手册大全')
