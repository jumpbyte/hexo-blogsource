---
title: 在windows,linux下搭建zookeeper集群环境
date: 2016-08-13 17:56:29
categories: ['安装部署']
tags: ['zookeeper','教程','环境搭建']
---
## Zookeeper简介

Zookeeper是一个分布式服务框架，它是 Apache Hadoop 的一个子项目，主要是用来解决分布式应用中经常遇到的一些数据管理问题，如：统一命名服务、状态同步服务、集群管理、分布式应用配置项的管理等。本文将介绍Zookeeper如何在windows和linux下的进行集群部署，以便为我们使用zookeeper的相关功能做好环境支持

<!--more-->

## 安装前提

zookeeper本身要依赖java jdk环境，在搭建前需要确认你的系统安装了java jdk，并配置好了jdk环境变量

## win下搭建zookeeper集群

由于机器有限，本次教程准备在单台机器搭建三个zookeeper节点(集群节点至少为三个)，来模拟zookeeper集群环境

### 安装包下载

访问[https://zookeeper.apache.org/](https://zookeeper.apache.org/ 'zookeeper官网')找到并下载我们需要安装的版本，这里我选择下载[zookeeper 3.4.6版本](http://apache.org/dist/zookeeper/zookeeper-3.4.6/zookeeper-3.4.6.tar.gz)(注：zookeeper安装包不区分操作系统平台)

### 安装包解压，集群节点建立

1. 创建集群目录如`D:\ProgramFiles\zk_cluster`,并在集群目录下创建三个目录s1,s2,s3，代表三个zookeeper集群节点，如图:
![三个zookeeper节点目录](http://oaefo3hoy.bkt.clouddn.com/16-8-13/78596671.jpg)

2. 将下载的安装包解压到指定zookeeper节点目录，本教程我先解压到上文中新建的s1目录(D:\ProgramFiles\zk_cluster\s1),并重命名zookeeper-3.4.6为zookeeper

3. 在s1目录(D:\ProgramFiles\zk_cluster\s1)新建data和dataLog两个目录(下文配置会用到)，最终s1目录结构如图：
![s1目录结构](http://oaefo3hoy.bkt.clouddn.com/16-8-13/9287916.jpg)

### 配置zoo.cfg文件参数

在目录D:\ProgramFiles\zk_cluster\s1\zookeeper\conf 有一个zoo_sample.cfg文件，先将其重命名或者复制为zoo.cfg(zookeeper在启动时会找这个文件作为默认配置文件),然后修改zoo.cfg为如下内容([zoo.cfg各个配置参数的含义](#zoo_cfg_table))
```
tickTime=2000
initLimit=10
syncLimit=5
dataDir=D:\\ProgramFiles\\zk_cluster\\s1\\data
dataLogDir=D:\\ProgramFiles\\zk_cluster\\s1\\dataLog
clientPort=2181
autopurge.snapRetainCount=3
autopurge.purgeInterval=1
server.1=127.0.0.1:2887:3887
server.2=127.0.0.1:2888:3888
server.3=127.0.0.1:2889:3889
```
除了修改zoo.cfg配置文件，集群模式下我们还要在上面dataDir指定的目录创建一个文件myid，文件内容为一个数字1，Zookeeper启动时会读取这个文件，拿到里面的数字匹配zoo.cfg中server.1，从而判断那个是当前的server。

通过上述步骤，我们s1节点配置就已经完成了，接着我们可以按照同样的思路配置其他两个节点，为了方便，我们直接将s1节点目录下面的所有内容分别复制到s2,s3节点目录下然后做如下调整
1.  修改s2,s3各自myid文件内容
    s2\data\myid -->2
    s3\data\myid -->3
2. 修改s2,s3各自的zoo.cfg配置参数，主要修改dataDir,dataLogDir对应到各自相应的目录,因为是单机模式，为了防止端口冲突，也需要修改clientPort参数
   s2节点的zoo.cfg修改的内容如下
  ```
  dataDir=D:\\ProgramFiles\\zk_cluster\\s2\\data
  dataLogDir=D:\\ProgramFiles\\zk_cluster\\s2\\dataLog
  clientPort=2182
  ```
  s3节点的zoo.cfg修改的内容如下
  ```
  dataDir=D:\\ProgramFiles\\zk_cluster\\s3\\data
  dataLogDir=D:\\ProgramFiles\\zk_cluster\\s3\\dataLog
  clientPort=2183
  ```

### 启动集群中各个节点

到此为止，我们所有节点配置已经完成，接下来在每个节点目录zookeeper/bin目录都有一个zkServer.cmd脚本，分别双击对应脚本，即可以启动每个节点，所有节点启动后，此时会看到集群节点建立的信息，如下图，从图中我们可以看到集群正常启动，s2为集群的Leader,s1、s3为Flower节点
![集群节点](http://oaefo3hoy.bkt.clouddn.com/16-8-13/11539405.jpg)

如果觉得每次都要到对应节点下启动各自zkServer.cmd麻烦的话，我们可以做一个bat脚本来批量执行各自节点的zkServer.cmd；如我在D:\ProgramFiles\zk_cluster添加了一个zk_cluster_start.bat，内容如下，这样可以快速启动集群节点
```shell
start cmd /c call %~dp0%s1\zookeeper\bin\zkServer.cmd
start cmd /c call %~dp0%s2\zookeeper\bin\zkServer.cmd
start cmd /c call %~dp0%s3\zookeeper\bin\zkServer.cmd
```

### 测试集群

打开cmd窗口，切换到任意节点目录下zookeeper/bin, 输入zkCli.cmd -server host:port 连接集群任意节点测试，下面是我测试的结果,通过测试结果可以看出我们的集群可以正常工作，集群环境搭建成功
```
D:\ProgramFiles\zk_cluster\s1\zookeeper\bin>zkCli.cmd -server 127.0.0.1:2183
...省略连接内容...
[zk: 127.0.0.1:2183(CONNECTED) 1] create  /test1  aaaa
Created /test1
[zk: 127.0.0.1:2183(CONNECTED) 5] connect 127.0.0.1:2181
...省略连接内容...
[zk: 127.0.0.1:2181(CONNECTED) 0] get /test1
aaaa
cZxid = 0x700000006
ctime = Sat Aug 13 21:44:50 CST 2016
mZxid = 0x700000006
mtime = Sat Aug 13 21:44:50 CST 2016
pZxid = 0x700000006
cversion = 0
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 4
numChildren = 0
[zk: 127.0.0.1:2181(CONNECTED) 1]
```

windows上搭建zookeeper集群环境部署完毕！

## linux下搭建zookeeper集群

由于机器有限，本次教程准备在单台centos6.5虚拟机搭建三个zookeeper节点(集群节点至少为三个)，来模拟zookeeper集群环境

### 安装包下载解压

进入shell命令窗口,将安装包下载到/usr/local/src目录，zookeeper版本同样使用[3.4.6版本](http://apache.org/dist/zookeeper/zookeeper-3.4.6/zookeeper-3.4.6.tar.gz)

```bash
[root@centos ~]# cd /usr/local/src
[root@centos src]# wget http://apache.org/dist/zookeeper/zookeeper-3.4.6/zookeeper-3.4.6.tar.gz
[root@centos src]# tar -zxvf zookeeper-3.4.6.tar.gz
[root@centos src]# ll
总用量 21332
-rw-r--r--.  1 root root   392756 8月  10 11:31 man-db-2.7.5-3-x86_64.pkg.tar.xz
-rw-r--r--.  1 root root   909077 8月  10 09:21 nginx-1.10.1.tar.gz
drwxrwxr-x.  6 root root     4096 4月   1 2015 redis-3.0.0
-rw-r--r--.  1 root root  1358081 4月   1 2015 redis-3.0.0.tar.gz
drwxr-xr-x. 14 root root     4096 8月  10 11:07 xz-5.2.2
-rw-r--r--.  1 root root  1464228 8月  10 11:04 xz-5.2.2.tar.gz
drwxr-xr-x. 10 1000 1000     4096 8月   8 11:13 zookeeper-3.4.6
-rw-r--r--.  1 root root 17699306 8月   8 10:54 zookeeper-3.4.6.tar.gz
```

### 配置安装目录及环境变量
```
[root@centos src] ln -s zookeeper-3.4.6  /usr/local/zookeeper
```
这里我将zookeeper软连接到/usr/local/zookeeper下面，以后升级zookeeper版本可以不用修改让任何环境变量，直接更改软连接指向就可以完成升级

配置环境变量
vi 编辑/etc/profile文件，导入ZK_HOME环境变量，路径指向刚才的软连接目录，如下：
```
#export env path
export JAVA_HOME=/usr/local/java/jdk
export PATH=$PATH:$JAVA_HOME/bin:$JAVA_HOME/jre/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib:$JAVA_HOME/jre/lib
export PATH=$PATH:$CLASSPATH
export ZK_HOME=/usr/local/zookeeper
export PATH=$PATH:$ZK_HOME/bin
for i in /etc/profile.d/*.sh ; do
    if [ -r "$i" ]; then
        if [ "${-#*i}" != "$-" ]; then
            . "$i"
        else
            . "$i" >/dev/null 2>&1
        fi
    fi
done
unset i
unset -f pathmunge
```
这里注意jdk环境变量和zookeeper环境变量，要配置在第8行的上面，因为下文我们的登录自动启动脚本会放到/etc/profile.d/下，所以要在脚本执行前导入环境变量

### 配置集群节点

创建集群目录

1. 在/usr/local目录创建zookeeper_cluster目录，然后在zookeeper_cluster目录先创建一个集群节点目录server1
2. 在server1目录创建data,logs目录
3. 最后将/usr/local/zookeeper/conf/zoo_sample.cfg 拷贝重命名为zoo.cfg到server1目录

操作过程如下
```
[root@centos local]# mkdir zookeeper_cluster
[root@centos local]# cd zookeeper_cluster
[root@centos zookeeper_cluster]# mkdir server1
[root@centos zookeeper_cluster]# cd server1
[root@centos server1]# mkdir data logs
[root@centos server1]# cp /usr/local/zookeeper/conf/zoo_sample.cfg zoo.cfg
```

配置zoo.cfg文件参数

修改server1节点目录下zoo.cfg文件的内容([zoo.cfg各个配置参数的含义](#zoo_cfg_table))
```
[root@centos server1]# vi zoo.cfg
```
内容修改如下
```
tickTime=2000
initLimit=10
syncLimit=5
dataDir=/usr/local/zookeeper_cluster/server1/data
dataLogDir=/usr/local/zookeeper_cluster/server1/logs
clientPort=2181
autopurge.snapRetainCount=3
autopurge.purgeInterval=1
server.1=127.0.0.1:2887:3887
server.2=127.0.0.1:2888:3888
server.3=127.0.0.1:2889:3889
```

创建myid文件

除了修改zoo.cfg配置文件，集群模式下我们还要在上面dataDir指定的目录创建一个文件myid，文件内容为一个数字1，Zookeeper启动时会读取这个文件，拿到里面的数字匹配zoo.cfg中server.1，从而判断那个是当前的server。
```
[root@centos server1]# echo 1 > data/myid
```

配置另外两个节点

通过上述步骤，我们server1节点配置就已经完成了，接着我们可以按照同样的思路配置其他两个节点，为了方便，我们直接将server1节点目录复制为server2,server3
```
[root@centos zookeeper_cluster] cp -rf server1  server2
[root@centos zookeeper_cluster] cp -rf server1  server3

```
1.  修改server2,server3各自myid文件内容
    ```
    [root@centos zookeeper_cluster] echo 2 > server2/myid
    [root@centos zookeeper_cluster] echo 3 > server3/myid
    ```
2. 修改server2,server3各自的zoo.cfg配置参数，主要修改dataDir,dataLogDir对应到各自相应的目录,因为是单机模式，为了防止端口冲突，也需要修改clientPort参数
  server2节点zoo.cfg修改内容如下
  ```
  dataDir=/usr/local/zookeeper_cluster/server2/data
  dataLogDir=/usr/local/zookeeper_cluster/server2/logs
  clientPort=2182
  ```
  server3节点zoo.cfg修改内容如下
  ```
  dataDir=/usr/local/zookeeper_cluster/server3/data
  dataLogDir=/usr/local/zookeeper_cluster/server3/logs
  clientPort=2183
  ```

### 启动集群

这里我们先在/etc/profile.d/ 创建一个集群启动的脚本zk_cluster_strat.sh，脚本内容如下：

 ```
 #!/bin/bash
 #description:auto start zookeeper cluster nodes when user login
 zkServer.sh start  /usr/local/zookeeper_cluster/server1/zoo.cfg
 zkServer.sh start  /usr/local/zookeeper_cluster/server2/zoo.cfg
 zkServer.sh start  /usr/local/zookeeper_cluster/server3/zoo.cfg
 ```
 脚本创建好之后，我们来手动执行下此脚本
 ```
[root@centos ~]# .  /etc/profile.d/zk_cluster_start.sh
JMX enabled by default
Using config: /usr/local/zookeeper_cluster/server1/zoo.cfg
Starting zookeeper ... STARTED
JMX enabled by default
Using config: /usr/local/zookeeper_cluster/server2/zoo.cfg
Starting zookeeper ... STARTED
JMX enabled by default
Using config: /usr/local/zookeeper_cluster/server3/zoo.cfg
Starting zookeeper ... STARTED
```

### 测试集群
接着我们用zkCli.sh测试每个节点连接
```
[root@centos ~]# zkCli.sh -server 127.0.0.1:2181
省略连接内容...
[zk: 127.0.0.1:2181(CONNECTED) 0] create /test  hello,world! #创建一个节点目录
Created /test
[zk: 127.0.0.1:2181(CONNECTED) 0] connect 127.0.0.1:2182
省略连接内容...
[zk: 127.0.0.1:2182(CONNECTED) 4] get /test  #获取刚才在127.0.0.1:2181创建/test节点内容
hello,world!
cZxid = 0x900000002
ctime = Sun Aug 14 11:51:05 CST 2016
mZxid = 0x900000002
mtime = Sun Aug 14 11:51:05 CST 2016
pZxid = 0x900000002
cversion = 0
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 12
numChildren = 0
127.0.0.1:2181
[zk: 127.0.0.1:2182(CONNECTED) 5] connect 127.0.0.1:2183
省略连接内容...
[zk: 127.0.0.1:2183(CONNECTED) 2] get /test #获取刚才在127.0.0.1:2181创建/test节点内容
hello,world!
cZxid = 0x900000002
ctime = Sun Aug 14 11:51:05 CST 2016
mZxid = 0x900000002
mtime = Sun Aug 14 11:51:05 CST 2016
pZxid = 0x900000002
cversion = 0
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 12
numChildren = 0
```

linux上搭建zookeeper集群环境部署完毕！

<hr/>

## <span id='zoo_cfg_table'>zoo.cfg各个配置参数的含义</span>

|配置参数|含义|
|:----:|:-----|
|**tickTime**|这个时间是作为 Zookeeper 服务器之间或客户端与服务器之间维持心跳的时间间隔，也就是每个tickTime时间就会发送一个心跳。
|**initLimit**|这个配置项是用来配置Zookeeper接受客户端（这里所说的客户端不是用户连接Zookeeper服务器的客户端，而是Zookeeper服务器集群中连接到Leader的Follower服务器）初始化连接时最长能忍受多少个心跳时间间隔数。当已经超过10个心跳的时间（也就是tickTime）长度后Zookeeper服务器还没有收到客户端的返回信息，那么表明这个客户端连接失败。总的时间长度就是5\*2000=10秒
|**syncLimit**|这个配置项标识Leader与Follower之间发送消息，请求和应答时间长度，最长不能超过多少个 tickTime 的时间长度，总的时间长度就2\*2000=4 秒
|**dataDir**|顾名思义就是Zookeeper保存数据的目录，默认情况下，Zookeeper将写数据的日志文件也保存在这个目录里。
|**dataLogDir**|顾名思义就是Zookeeper记录数据变化的日志目录，如果不配置，默认日志文件会保存到dataDir数据目录中。
|**clientPort**|这个端口就是客户端连接Zookeeper服务器的端口，Zookeeper会监听这个端口，接受客户端的访问请求。
|**autopurge.snapRetainCount**|指定了需要保留的快照文件数目。默认是保留3个，需要和autopurge.purgeInterval搭配使用。
|**autopurge.purgeInterval**|指定了自动清理快照和事务日志的频率，单位是小时，需要填写一个1或更大的整数，默认是0，表示不开启自己清理功能
|**server.A=B：C：D**|其中A是一个数字，表示这个是第几号服务器；B是这个服务器的ip地址；C表示的是这个服务器与集群中的Leader服务器交换信息的端口；D 表示的是万一集群中的Leader服务器挂了，需要一个端口来重新进行选举，选出一个新的Leader，而这个端口就是用来执行选举时服务器相互通信的端口。如果是伪集群的配置方式，由于B都是一样，所以不同的Zookeeper实例通信端口号不能一样，所以要给它们分配不同的端口号。


## 参考资料

1. [ZooKeeper 基础知识、部署和应用程序](http://www.ibm.com/developerworks/cn/data/library/bd-zookeeper/)
2. [分布式服务框架 Zookeeper -- 管理分布式环境中的数据](https://www.ibm.com/developerworks/cn/opensource/os-cn-zookeeper/)
