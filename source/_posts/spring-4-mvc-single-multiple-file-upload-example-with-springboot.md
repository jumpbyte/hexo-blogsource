---
title: Spring Boot+SpringMVC4实现单文件多文件上传
date: 2016-09-02 17:33:38
categories: SpringBoot
tags: ['Spring Boot','java','文件上传','示例','原创翻译']
---

本文我们将学习如何在SpringMVC4中实现文件的上传，示例将提供单文件和多文件的上传的演示。使用更少的XML配置，我们将需要为文件上传配置一个MultipartConfigElement Bean对象，控制器中方法的参数应该使用MultipartFile类接受上传文件。同时在JSP页面表单，enctype属性要设置成多部分表单数据(multipart/form-data),现在来说下我们的示例代码
<!--more-->

### 开发工具

  运行此示例我们需要准备好以下软件环境
- JDK7
- Eclipse
- Tomcat7
- Gradle 2.0 for Spring Boot

### Eclipse示例项目结构

为了更好的理解，我们先整体看下示例类文件和JSP页面的存放结构
![ Eclipse示例项目结构](http://oaefo3hoy.bkt.clouddn.com/16-9-2/15447753.jpg)

### 控制器：使用MultipartFile类

本示例我们要演示单文件和多文件上传，所以我们创建了两个不同上传文件的方法。

单文件上传方法里需要包含有如下的一个参数：

```Java
@RequestParam("file") MultipartFile file
```
多文件上传方法中，方法参数如下：
```java
@RequestParam("file") MultipartFile[]  file
```
从MultipartFile对象中获取上传文件的名称保存到你期望保存路径位置，整个上传文件代码逻辑如下：

**文件清单：FileUploadController.java**
```
package com.concretepage;
import java.io.BufferedOutputStream;
import java.io.File;
import java.io.FileOutputStream;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.multipart.MultipartFile;
@Controller
public class FileUploadController {
    @RequestMapping(value="/singleUpload")
    public String singleUpload(){
    	return "singleUpload";
    }
    @RequestMapping(value="/singleSave", method=RequestMethod.POST )
    public @ResponseBody String singleSave(@RequestParam("file") MultipartFile file, @RequestParam("desc") String desc ){
    	System.out.println("File Description:"+desc);
    	String fileName = null;
    	if (!file.isEmpty()) {
            try {
                fileName = file.getOriginalFilename();
                byte[] bytes = file.getBytes();
                BufferedOutputStream buffStream =
                        new BufferedOutputStream(new FileOutputStream(new File("F:/cp/" + fileName)));
                buffStream.write(bytes);
                buffStream.close();
                return "You have successfully uploaded " + fileName;
            } catch (Exception e) {
                return "You failed to upload " + fileName + ": " + e.getMessage();
            }
        } else {
            return "Unable to upload. File is empty.";
        }
    }
    @RequestMapping(value="/multipleUpload")
    public String multiUpload(){
    	return "multipleUpload";
    }
    @RequestMapping(value="/multipleSave", method=RequestMethod.POST )
    public @ResponseBody String multipleSave(@RequestParam("file") MultipartFile[] files){
    	String fileName = null;
    	String msg = "";
    	if (files != null && files.length >0) {
    		for(int i =0 ;i< files.length; i++){
	            try {
	                fileName = files[i].getOriginalFilename();
	                byte[] bytes = files[i].getBytes();
	                BufferedOutputStream buffStream =
	                        new BufferedOutputStream(new FileOutputStream(new File("F:/cp/" + fileName)));
	                buffStream.write(bytes);
	                buffStream.close();
	                msg += "You have successfully uploaded " + fileName +"<br/>";
	            } catch (Exception e) {
	                return "You failed to upload " + fileName + ": " + e.getMessage() +"<br/>";
	            }
    		}
    		return msg;
        } else {
            return "Unable to upload. File is empty.";
        }
    }
}
```
### 使用MultipartConfigElement上传配置类

在配置代码里面,我们需要使用到MultipartConfigElement和UrlBasedViewResolver两个配置类。MultipartConfigElement支持文件上传，例如我们可以设置最大文件大小,最大请求大小等。MultipartConfigElement需要在WebApplicationInitializer中被配置到Dispatcher servlet中。UrlBasedViewResolver定义JSP位置和文件扩展名的输出模式

**代码清单：AppConfig.java**
```
package com.concretepage;
import javax.servlet.MultipartConfigElement;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.boot.context.embedded.MultipartConfigFactory;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;
import org.springframework.web.servlet.view.JstlView;
import org.springframework.web.servlet.view.UrlBasedViewResolver;
@Configuration
@ComponentScan
@EnableWebMvc
@EnableAutoConfiguration
public class AppConfig {
    @Bean
    public MultipartConfigElement multipartConfigElement() {
        MultipartConfigFactory factory = new MultipartConfigFactory();
        factory.setMaxFileSize("128KB");
        factory.setMaxRequestSize("128KB");
        return factory.createMultipartConfig();
    }
    @Bean  
    public UrlBasedViewResolver setupViewResolver() {  
        UrlBasedViewResolver resolver = new UrlBasedViewResolver();  
        resolver.setPrefix("/views/");  
        resolver.setSuffix(".jsp");  
        resolver.setViewClass(JstlView.class);
        return resolver;  
    }
}
```

### WebApplicationInitializer初始化

WebApplicationInitializer使我们的应用程序不再依赖web.xml文件，也一样可以支持所有应用功能。为了支持文件上传，我们的dispatcher必须注入多文件上传配置类

**文件清单：WebAppInitializer.java**
```
package com.concretepage;
import javax.servlet.MultipartConfigElement;
import javax.servlet.ServletContext;  
import javax.servlet.ServletException;  
import javax.servlet.ServletRegistration.Dynamic;  
import org.springframework.web.WebApplicationInitializer;  
import org.springframework.web.context.support.AnnotationConfigWebApplicationContext;  
import org.springframework.web.servlet.DispatcherServlet;  
public class WebAppInitializer implements WebApplicationInitializer {
	public void onStartup(ServletContext servletContext) throws ServletException {  
        AnnotationConfigWebApplicationContext ctx = new AnnotationConfigWebApplicationContext();  
        ctx.register(AppConfig.class);  
        ctx.setServletContext(servletContext);
        ctx.refresh();
        Dynamic dynamic = servletContext.addServlet("dispatcher", new DispatcherServlet(ctx));  
        dynamic.addMapping("/");  
        dynamic.setLoadOnStartup(1);
        dynamic.setMultipartConfig(ctx.getBean(MultipartConfigElement.class));
   }  
}  
```

### 视图页面配置

使用jsp视图，为了支持文件上传,必须设置enctype为多部分表单数据,并添加一个file输入框。我们有两个JSP，找到单文件上传的JSP页面如下
**文件清单：singleUpload.jsp**
```
<html>
<body>
<h1>Single File Upload</h1>
	<form method="post" enctype="multipart/form-data" action="singleSave">
		Upload File: <input type="file" name="file">
		<br /><br />
		Description: <input type="text" name="desc"/>
		<br/><br/><input type="submit" value="Upload">
	</form>
</body>
</html>
```
找到多文件上传JSP页面，我们需要注意每个file输入框name必须一致，这样可以在后台通过一个集合数组来访问
**文件清单：multipleUpload.jsp**
```
<html>
<body>
    <h1> Multiple File Upload </h1>
	<form method="post" enctype="multipart/form-data" action="multipleSave">
		Upload File 1: <input type="file" name="file"> <br/>
		Upload File 2: <input type="file" name="file"> <br/>
		Upload File 3: <input type="file" name="file"> <br/>
		Upload File 4: <input type="file" name="file"> <br/>
		<br /><br /><input type="submit" value="Upload">
	</form>
</body>
</html>
```
### 使用Gradle管理Spring Boot包依赖

在gradle脚本添加JAR依赖项，构建项目创建WAR文件。此示例用到了Spring boot web和security依赖
**文件清单：build.gradle**
```
apply plugin: 'java'
apply plugin: 'eclipse'
apply plugin: 'war'
archivesBaseName = 'CP'
repositories {
    mavenCentral()
}
dependencies {
   compile 'org.springframework.boot:spring-boot-starter-web:1.1.4.RELEASE'
   compile 'org.springframework.boot:spring-boot-starter-security:1.1.4.RELEASE'
   compile 'javax.servlet:jstl:1.2'
   compile 'commons-fileupload:commons-fileupload:1.3.1'
}  
```
### Tomcat7部署示例运行
到项目build目录，在lib下会得到一个war文件， 将此war部署到tomcat运行测试

**单个文件上传输出**

访问URL[http://localhost:8080/CP/singleUpload](http://localhost:8080/CP/singleUpload)
![单个文件上传](http://oaefo3hoy.bkt.clouddn.com/16-9-2/84536250.jpg)
![单个文件上传]](http://oaefo3hoy.bkt.clouddn.com/16-9-2/16535556.jpg)

**多文件上传输出**
访问URL[http://localhost:8080/CP/multipleUpload](http://localhost:8080/CP/multipleUpload)
![多文件上传](http://oaefo3hoy.bkt.clouddn.com/16-9-2/6931726.jpg)
![多文件上传](http://oaefo3hoy.bkt.clouddn.com/16-9-2/8079858.jpg)

### 源码下载

[spring-4-mvc-single-multiple-file-upload-example-with-tomcat.zip](http://www.concretepage.com/spring-4/download/spring-4-mvc-single-multiple-file-upload-example-with-tomcat.zip)

### 博主声明

本文属于博主原创英文技术文章翻译，原文连接请点击[这里](http://www.concretepage.com/spring-4/spring-4-mvc-single-multiple-file-upload-example-with-tomcat),翻译过程博主对原文会加入自己的理解，文章结构也会有稍微调整以更适合阅读

由于翻译和理解能力有限，文章难免会存在一定瑕疵希望读者见谅，如有任何错误纰漏欢迎在下方评论指正，谢谢！
