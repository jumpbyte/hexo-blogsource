---
title: 基于Spring Boot运行Spring MVC 应用程序
date: 2016-08-20 11:52:31
categories: SpringBoot
tags: ["SpringBoot","Java","原创翻译"]
---
### 前言

本教程适合对于有兴趣学习Spring Boot如何结合Spring MVC一起开发的初学者。通过本教程,您可以使用Spring Boot运行一个简单的Spring MVC应用程序
<!--more-->
>SpringBoot是一个Spring IO中的一个项目，它旨在减少Spring应用程序启动配置和连接。开发人员只专注于编写应用程序，SpringBoot将简化打包和部署的流程，而且不需要任何显示的声明配置。它就是如此在容器之外工作的! 在这里,我将解释如何使用Spring Boot运行一个简单且带有执行CRUD操作的Spring MVC应用程序。开始这个例子之前,请[在eclipse中创建web项目](http://www.javabeat.net/maven-eclipse/)。

- 扩展阅读:[http://www.javabeat.net/spring-boot/](http://www.javabeat.net/spring-boot/)

### 开始

我已经实现了一个例子，例子做了如下的工作
1. 创建一个Web 页面
2. 用户可以想数据库中添加员工
3. 用户可以在浏览器页面查看已添加到数据中的员工列表(JSON形式的结果)

上面展现的工作使用到了Spring MVC, Hibernate and Spring Boot这些技术

### 创建领域对象

第一步，创建一个用来存储员工详细信息的Employee类，它是一个简单的POJO对象

```java
@Entity
public class Employee {

    @Id
    @NotNull
    @Size(max = 64)
    @Column(name = "id", nullable = false, updatable = false)
    private String id;

    @NotNull
    @Size(max = 64)
    @Column(name = "name", nullable = false)
    private String name;

    @NotNull
    @Size(max = 64)
    @Column(name = "city", nullable = false)
    private String city;

    Employee() {
    }

    public Employee(final String id, final String name,final String city) {
        this.id = id;
        this.name = name;
        this.city = city;
    }

    public String getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getCity() {
        return city;
    }

    public void setCity(String city) {
        this.city = city;
    }

    @Override
    public String toString() {
        return Objects.toStringHelper(this)
                .add("id", id)
                .add("name", name)
                .add(city, city)
                .toString();
    }
}
```

### 创建Spring Data JPA仓库

Spring Data JPA是一个非常简单的方法保存数据到后端，如果你看了下面的代码，这就是你要持久化员工类将要写的代码，Spring Data在内部为你实现了必要的类和CRUD的操作，你只需要像下面一样提供一个接口即可，如果你对于Spring Data有兴趣，请阅读我们的[Spring Data JPA教程](http://www.javabeat.net/spring-data-jpa-tutorial/)

```
public interface EmployeeRepository extends JpaRepository<Employee, String> {
}
```

### 创建服务的实现类

```java
@Service
@Validated
public class EmployeeServiceImpl implements EmployeeService {

    private static final Logger LOGGER = LoggerFactory.getLogger(EmployeeServiceImpl.class);
    private final EmployeeRepository repository;

    @Inject
    public EmployeeServiceImpl(final EmployeeRepository repository) {
        this.repository = repository;
    }

    @Transactional
    public Employee save(@NotNull @Valid final Employee employee) {
        LOGGER.debug("Creating {}", employee);
        return repository.save(employee);
    }

    @Transactional(readOnly = true)
    public List<Employee> getList() {
        LOGGER.debug("Retrieving the list of all users");
        return repository.findAll();
    }
}
```
### 创建SpringMVC控制器

这里对于Spring的开发者来说没有任何新的特性，我们只是创建一个简单实现了对员工的详细信息的添加和列表浏览

```
@RestController
public class EmployeeController {
    private final EmployeeService employeeService;

    @Inject
    public EmployeeController(final EmployeeService employeeService) {
        this.employeeService = employeeService;
    }

    @RequestMapping(value = "/employee", method = RequestMethod.GET)
    public List<Employee> listEmployees() {
        List<Employee> employees = employeeService.getList();
        return employees;
    }
    @RequestMapping(value="/add",method = RequestMethod.GET)
    public void addEmployee(@RequestParam(value = "employeeName", required = false) String employeeName,
    		@RequestParam(value = "employeeId", required = false) String employeeId,
    		@RequestParam(value = "employeeCity", required = false) String employeeCity){
    	Employee employee = new Employee(employeeId,employeeName,employeeCity);
    	employeeService.save(employee);
    }
}
```

### 创建JSP页面

```
<!DOCTYPE html>
<html>
<head>
    <title>Hello WebSocket</title>
</head>
<body>
<form action="/add">
	<input type="input" name="employeeName">
	<input type="input" name="employeeId">
	<input type="input" name="employeeCity">
	<input type="submit">
</form>
</body>
</html>
```

### Spring Boot 应用程序

最后，我们需要写一个Spring Boot application来运行我们的示例，这是一个主应用程序，当你试图执行应用程序时，它将会被SpringBoot调用

- 扩展阅读:[REStful API using Spring Boot](http://www.javabeat.net/restful-springboot-mongodb/)

```
@Configuration
@EnableAutoConfiguration
@ComponentScan
public class Application extends SpringBootServletInitializer {

    public static void main(final String[] args) {
        SpringApplication.run(Application.class, args);
    }

    @Override
    protected final SpringApplicationBuilder configure(final SpringApplicationBuilder application) {
        return application.sources(Application.class);
    }
}
```

**@EnableAutoConfiguration**

这个注解会试图在类路径下去查找启动应用程序所必需的beans，例如，如果你在类路径下有一个tomcat-embedded.jar，你可能会需要一个TomcatEmbeddedServletContainerFactory，这个包下的这个类被@EnableAutoConfiguration注解就有特殊的意义，通常会被作为默认的使用。再比如，当扫描带有@Entity注解的类它将被用到。通常推荐的做法，你可以在一个包的根下使用@EnableAutoConfiguration注解，这样所有子包和类都会被搜索

**SpringApplication.run()**

SpringApplication类是用于引导和启动Spring应用程序的。引导一个Spring应用程序创建适当的应用程序上下文，加载beans，触发任何命令式的bean对象等

**SpringBootServletInitializer**

这个是运行web应用所必须的，它将打开一个 servlet 容器，并将你的应用绑定到此容器内。


### MAVEN配置

这个运行SpringBoot应用非常重要的环节。SpringBoot需要通过在构建脚本文件中确定运行应用程序的包依赖，它能支持各种各样的构建工具，但SpringBoot官方文档中重点强调使用Maven和Gradle配置，因此，明智的做法是选择其中任何一个去简单的实现

这里是使用maven配置如下：
```xml
<dependencies>
       <!-- Spring Boot -->
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter</artifactId>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-test</artifactId>
           <scope>test</scope>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-web</artifactId>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-data-jpa</artifactId>
       </dependency>
       <!-- Hibernate validator -->
       <dependency>
           <groupId>org.hibernate</groupId>
           <artifactId>hibernate-validator</artifactId>
       </dependency>
       <!-- HSQLDB -->
       <dependency>
           <groupId>org.hsqldb</groupId>
           <artifactId>hsqldb</artifactId>
           <scope>runtime</scope>
       </dependency>
       <!-- Guava -->
       <dependency>
           <groupId>com.google.guava</groupId>
           <artifactId>guava</artifactId>
           <version>${guava.version}</version>
       </dependency>
       <!-- Java EE -->
       <dependency>
           <groupId>javax.inject</groupId>
           <artifactId>javax.inject</artifactId>
           <version>1</version>
       </dependency>
   </dependencies>
   <build>
       <plugins>
           <!-- Spring Boot Maven -->
           <plugin>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-maven-plugin</artifactId>
           </plugin>
       </plugins>
   </build>
```

**spring-boot-starter-\***

Starter POM是预定义的作为内置可用的POM集合描述，你不需要去搜索和配置那些依赖。比如，如果你正在为数据访问寻找Spring和JPA依赖，你只需把spring-boot-starter-data-jpa包含到你的POM文件中即可

**spring-boot-maven-plugin**

这个插件允许你将应用程序打包成一个可执行的JAR或WAR文件，它支持Maven 3.2以上版本。在我们的示例，当你使用“mvn install”命令运行项目，maven将构建此项目并且会在target目录创建一个可执行的JAR文件。

**spring-boot-starter-parent**

如果你想从spring-boot-starter-parent继承属性,然后简单地设置此父项目。如果你使用此父项目,版本号可以省略其他starters POM也如此。如果你有你自己的项目为你的项目,那么你可以使用它们代替由SpringBoot默认提供的父项目。配置如下：

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.0.1.RELEASE</version>
</parent>
```

### 最终项目结构

下面图是最终项目的包结构，这里我已经在一个包下面创建了一个Application.java。但好的实践是把这个文件直接放到包的根目录下
![最终项目的包结构](http://oaefo3hoy.bkt.clouddn.com/16-8-20/18274027.jpg)

### 运行SpringBoot可执行Jar包

一旦你创建好了应用程序，运行这个命令来执行应用程序
```
java -jar target/mymodule-0.0.1-SNAPSHOT.jar
```
在你命令行程序运行上面的命令后，SpringBoot应用会在[http://localhost:8080/](http://localhost:8080/)运行此程序。你可以通过[http://localhost:8080/index.jsp](http://localhost:8080/index.jsp)访问应用的首页
![访问应用的首页](http://oaefo3hoy.bkt.clouddn.com/16-8-20/78731535.jpg)

你可以添加很多行员工数据，当你访问[http://localhost:8080/employee](http://localhost:8080/employee),你会在浏览器窗口看到类似下面的输出
```
[{"id":"Krishna","name":"001","city":"Bangalore"},{"id":"Manisha Patil","name":"005","city":"Pune"},{"id":"Sanulla","name":"001","city":"Bangalore"},{"id":"Shunmuga Raja","name":"001","city":"Bangalore"}]
```

### 源码下载

[Spring Boot Spring MVC](http://javabeat.net/download/spring-boot-spring-mvc/?wpdmdl=22124)

### 总结

在本文中,我们已经讨论了如何创建一个maven项目和创建员工所需的不同组件管理Spring MVC应用程序。后来我们已经探讨了SpringBoot配置细节和SpringBoot应用所需的Maven POM文件内容。最后我们已经讨论了运行一个通过Maven打包生成可执行JAR文件。在本教程中,您将能够了解关于SpringBoot和Spring MVC是怎样一起工作

### 博主声明

本文属于博主原创英文技术文章翻译，原文连接请点击[这里](http://javabeat.net/spring-boot-spring-mvc/),翻译过程博主对原文会加入自己的理解，文章结构也会有稍微调整以更适合阅读

由于翻译和理解能力有限，文章难免会存在一定瑕疵希望读者见谅，如有任何错误纰漏欢迎在下方评论指正，谢谢！
