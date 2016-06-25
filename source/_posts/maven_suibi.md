---
title: maven随笔纪录
date: 2016-06-25 11:41:41
tags: [maven,随笔,maven使用教程]
---


# 写在前面

此篇随笔，主要用来纪录博主在日常使用maven过程中的技术知识碎片，里面会包括maven的基本操作和使用过程的问题


# maven知识整理

## 向本地仓库添加已有jar包  

在maven仓库找不到引用的包，同时还要使用maven来管理，此时我们就需要先去其他官网找到我们自己要用的jar包，然后使用`mvn install`将其放到我们的本地的maven仓库，之后后在项目pom.xml里面添加此依赖包就可以

比如引用sqljdbc4.jar包，maven仓库中没有，所以我们先从微软官网下载下来，然后使用下面的命令将期添加到自己的本地仓库中去

```
D:\soft\develop\java-jars>mvn install:install-file -Dfile=sqljdbc4.jar -DgroupId=com.microsoft.sqlserver -DartifactId=sqljdbc4 -Dversion=4.0 -Dpackaging=jar
[INFO] Scanning for projects...
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] Building Maven Stub Project (No POM) 1
[INFO] ------------------------------------------------------------------------
[INFO]
[INFO] --- maven-install-plugin:2.3.1:install-file (default-cli) @ standalone-pom ---
[INFO] Installing D:\soft\develop\java-jars\sqljdbc4.jar to C:\Users\yuanjinan\.m2\repository\com\microsoft\sqlserver\sqljdbc4\4.0\sqljdbc4-4.0.jar
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 0.427s
[INFO] Finished at: Sat Jun 25 13:32:34 CST 2016
[INFO] Final Memory: 5M/119M
[INFO] ------------------------------------------------------------------------
```
接着在pom.xml添加依赖

```xml
<dependency>
  <groupId>com.microsoft.sqlserver</groupId>
  <artifactId>sqljdbc4</artifactId>
  <version>4.0</version>
</dependency>
```
此方法也可以方便把我们自己的项目的jar包安装到本地的maven仓库中去
