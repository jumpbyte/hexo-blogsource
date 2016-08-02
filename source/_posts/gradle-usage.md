---
title: gradle日常使用之Java插件
date: 2016-07-17 00:34:58
categories: "Java"
tags: ['gradle','构建工具']
---


### 介绍

Java 插件向一个项目添加了 Java 编译、 测试和 bundling 的能力。它是很多其他 Gradle 插件的基础服务。

### 如何引入

要使用 Java 插件，请在构建脚本(build.gradle)中加入：
```groovy
apply plugin: 'java'
```

### Java项目默认布局

|目录|含义|
|:----|:----|
|src/main/java|	产品的Java源代码
|src/main/resources	|产品的资源
|src/test/java	|Java 测试源代码
|src/test/resources	|测试资源
|src/sourceSet/java	|给定的源集的Java源代码
|src/sourceSet/resources	|给定的源集的资源

### 自带任务列举

|任务名称|依赖于|类型|描述|
|:----|:----|:----|:-----|
|compileJava|产生编译类路径中的所有任务。这包括了用于编译配置中包含的项目依赖关系的jar任务。|JavaCompile|使用 javac 编译产品中的 Java 源文件。
|processResources|	-| Copy	|把生产资源文件拷贝到生产的类目录中。|
|classes|compileJava和processResources。一些插件添加了额外的编译任务|Task|组装生产的类目录。
|compileTestJava	|compile，再加上所有能产生测试编译类路径的任务。	|JavaCompile|	使用 javac 编译 Java 的测试源文件。
|processTestResources|	-	|Copy|	把测试的资源文件拷贝到测试的类目录中。
|testClasses	|compileTestJava和processTestResources。一些插件添加了额外的测试编译任务。	Task	|组装测试的类目录。
|jar	|compile	|Jar	|组装 JAR 文件
|javadoc	|compile	|Javadoc	|使用 Javadoc 生成生产的 Java 源代码的API文档
|test	|compile， compileTest，再加上所有产生测试运行时类路径的任务。	|Test	|使用 JUnit 或 TestNG运行单元测试。
|uploadArchives	|使用archives的配置生成构件的任务，包括jar。	|Upload	|使用archives配置上传包括 JAR 文件的构件。
|clean	|-	|Delete	|删除项目的build目录。
|cleanTaskName|	-	|Delete	|删除由指定的任务所产生的输出文件。例如， cleanJar将删除由jar任务中所创建的 JAR 文件，cleanTest将删除由test任务所创建的测试结果。

### 源集概念

Java 插件引入了一个源集的概念。一个源集只是一组用于编译并一起执行的源文件。这些源文件可能包括 Java 源代码文件和资源文件。其他有一些插件添加了在源集里包含 Groovy 和 Scala 的源代码文件的能力。一个源集有一个相关联的编译类路径和运行时类路径。

源集的一个用途是，把源文件进行逻辑上的分组，以描述它们的目的。例如，你可能会使用一个源集来定义一个集成测试套件，或者你可能会使用单独的源集来定义你的项目的 API 和实现类。

Java 插件定义了两个标准的源集，分别是main和test。main源集包含你产品的源代码，它们将被编译并组装成一个 JAR 文件。test源集包含你的单元测试的源代码，它们将被编译并使用 JUnit 或 TestNG来执行

### Java 插件-源集任务

对于每个你添加到该项目中的源集，Java 插件将添加以下的编译任务：

|任务名称|依赖于|类型|描述|
|:----|:----|:----|:-----|
|compileSourceSetJava	|所有产生源集编译类路径的任务。	|JavaCompile	|使用 javac 编译给定的源集中的 Java 源文件。
|processSourceSetResources	|-	|Copy	|把给定的源集的资源文件拷贝到类目录中。
|sourceSetClasses	|compileSourceSetJava 和 processSourceSetResources。某些插件还为源集添加了额外的编译任务。	|Task	|组装给定源集的类目录。


### Java 插件-生命周期任务

Java 插件还增加了大量的任务构成该项目的生命周期：

|任务名称|依赖于|类型|描述|
|:----|:----|:----:|:-----|
|assemble	|项目中的所有归档项目，包括jar任务。某些插件还向项目添加额外的归档任务。	|Task	|组装项目中所有的归类文件。
|check	|项目中的所有核查项目，包括test任务。某些插件还向项目添加额外的核查任务。	|Task	|执行项目中所有的核查任务。
|build	|check 和 assemble	|Task	|执行项目的完事构建。
|buildNeeded	|build任务，以及在testRuntime配置的所有项目库依赖项的build任务。	|Task	|执行项目本身及它所依赖的其他所有项目的完整构建。
|buildDependents	|build任务，以及在testRuntime配置中对此项目有库依赖的所有项目的build任务。	|Task	|执行项目本身及依赖它的其他所有项目的完整构建。
|buildConfigurationName	|使用配置ConfigurationName生成构件的任务。	|Task	|组装指定配置的构件。该任务由Base插件添加，并由Java插件隐式实现。
|uploadConfigurationName	|使用配置ConfigurationName上传构件的任务。	|Upload	|组装并上传指定配置的构件。该任务由Base插件添加，并由Java插件隐式实现。

### Java插件-任务依赖关系图

![Java插件-任务依赖关系图](http://oaefo3hoy.bkt.clouddn.com/16-7-17/48492582.jpg)


### Java插件 ​​- 依赖配置

Java插件向项目添加了许多依赖配置，如下图所示。它对一些任务指定了这些配置，如compileJava和test。

|名称|继承自|在哪些任务中使用|意义|
|----|------|----------------|----|
|compile|	-	|compileJava	|编译时依赖
|runtime	|compile	|-	|运行时依赖
|testCompile|	compile	|compileTestJava	|用于编译测试的其他依赖
|testRuntime	|runtime, testCompile	|test	|只用于运行测试的其他依赖
|archives	|-	|uploadArchives	|由本项目生产的构件（如jar包）。
|default	|runtime	|-	|本项目上的默认项目依赖配置。包含本项目运行时所需要的构件和依赖。


Java插件 ​​- 依赖配置 对于每个你添加到项目中的源集，Java插件都会添加以下的依赖配置：

![默认添加的依赖配置](http://oaefo3hoy.bkt.clouddn.com/16-7-17/64820034.jpg)

### Java插件的常规属性

Java插件向项目添加了许多常规属性，如下表格所示。您可以在构建脚本中使用这些属性，就像它们是project对象的属性一样

|属性名称|类型|默认值|描述|
|:----|:----|:----:|:-----|
|reportsDirName	|String	|reports	|相对于build目录的目录名称，报告将生成到此目录。
|reportsDir	|File (read-only)	|buildDir/reportsDirName|	报告将生成到此目录。
|testResultsDirName	|String	|test-results	|相对于build目录的目录名称，测试报告的.xml文件将生成到此目录。
|testResultsDir	|File (read-only)	|buildDir/testResultsDirName	|测试报告的.xml文件将生成到此目录。|
|testReportDirName	|String	|tests	|相对于build目录的目录名称，测试报告将生成到此目录。
|testReportDir	|File (read-only)	|reportsDir/testReportDirName	|测试报告生成到此目录。
|libsDirName	|String	|libs	|相对于build目录的目录名称，类库将生成到此目录中。
|libsDir	|File (read-only)|	buildDir/libsDirName	|类库将生成到此目录中。
|distsDirName	|String	|distributions	|相对于build目录的目录名称，发布的文件将生成到此目录中。
|distsDir	|File (read-only)	|buildDir/distsDirName	|要发布的文件将生成到此目录。
|docsDirName	|String	|docs	|相对于build目录的目录名称，文档将生成到此目录。
|docsDir|	File (read-only)	|buildDir/docsDirName	|要生成文档的目录。
|dependencyCacheDirName	|String	|dependency-cache	|相对于build目录的目录名称，该目录用于缓存源代码的依赖信息。
|dependencyCacheDir	|File (read-only)	|buildDir/dependencyCacheDirName	|该目录用于缓存源代码的依赖信息。
|sourceSets	|SourceSetContainer (read-only)	|非空	|包含项目的源集。
|sourceCompatibility	|JavaVersion.	|可以使用字符串或数字来设置，例如"1.5"或1.5。	|当前JVM所使用的值 当编译Java源代码时所使用的Java版本兼容性。
|targetCompatibility|	JavaVersion.	|可以使用字符串或数字来设置，例如"1.5"或1.5。	|sourceCompatibility 要生成的类的 Java 版本。
|archivesBaseName	|String	|projectName|	像JAR或ZIP文件这样的构件的basename
|manifest	|Manifest	|一个空的清单	|要包括的所有 JAR 文件的清单。


### 使用源集

你可以使用sourceSets属性访问项目的源集。这是项目的源集的容器，它的类型是 SourceSetContainer。除此之后，还有一个sourceSets{}的脚本块，可以传入一个闭包来配置源集容器。源集容器的使用方式几乎与其他容器一样，例如tasks。

示例代码：访问源集的属性
```gradle
// Various ways to access the main source set
println sourceSets.main.output.classesDir
println sourceSets['main'].output.classesDir
sourceSets {
    println main.output.classesDir
}
sourceSets {
    main {
        println output.classesDir
    }
}

// Iterate over the source sets
sourceSets.all {
    println name
}
```

示例代码：配置源集的源代码目录

build.gradle文件
```gradle
sourceSets {
    main {
        java {
            srcDir 'src/java'
        }
        resources {
            srcDir 'src/resources'
        }
    }
}
```


#### 源集属性列举

下表列出了一些重要的源集属性。我们可以在SourceSet的 API 文档中查看更多的详细信息。

|属性名称|类型|默认值|描述
|:--------|:----:|:------:|:----
|name	|String (read-only)	|非空	|用来确定一个源集的源集名称。
|output	|SourceSetOutput (read-only)	|非空	|源集的输出文件，包含它编译过的类和资源。
|output.classesDir|	File	|*buildDir/classes/name*	|要生成的该源集的类的目录。
|output.resourcesDir	|File	|*buildDir/resources/name*	|要生成的该源集的资源的目录。
|compileClasspath	|FileCollection |compileSourceSet 配置 |该类路径在编译该源集的源文件时使用。
|runtimeClasspath |FileCollection |output + runtimeSourceSet 配置|该类路径在执行该源集的类时使用。
|java |SourceDirectorySet (read-only)| 非空 |该源集的Java源文件。仅包含Java源文件目录里的.java文件，并排除其他所有文件。
|java.srcDirs |Set<File> 可指定一组输入文件来设置|[*projectDir/src/name/java*]|该源目录包含了此源集的所有Java源文件。
|resources |SourceDirectorySet (read-only) |非空 |此源集的资源文件。仅包含资源文件，并且排除在资源源目录中找到的所有 .java文件。其他插件，如Groovy 插件，会从该集合中排除其他类型的文件。
|resources.srcDirs| Set<File> 指定一组输入文件来设置。| [*projectDir/src/name/resources*] |该源目录包含了此源集的资源文件。
|allJava |SourceDirectorySet (read-only) |java |该源集的所有.java 文件。有些插件，如Groovy 插件，会从该集合中增加其他的Java源文件。
|allSource |SourceDirectorySet (read-only)| resources + java |该源集的所有源文件。包含所有的资源文件和Java源文件。有些插件，如Groovy 插件，会从该集合中增加其他的源文件。


### 多项目构建

### 创建多项目总结

1. 一个多项目构建必须在根项目的根目录下包含settings.gradle文件，因为它指明了那些包含在多项目构建中的项目。

2. 如果需要在多项目构建的所有项目中加入公用的配置或行为，我们可以将这项配置加入到根项目的build.gradle文件中(使用allprojects)

3. 如果需要在根项目的子项目中加入公用的配置或行为，我们可以将这项配置加入到根项目的build.gradle文件中(使用subprojects)
