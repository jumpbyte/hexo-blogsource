---
title: 使用Swagger插件丰富增强RESTful服务
date: 2016-09-03 14:04:44
categories: 随笔
tags: ['Swagger','RESTful Service','原创翻译']
---

## 前言

通常来说调用一个RESTful服务接口可以说是一个比较繁杂的事情,因为有很多非常低效的任务要去做。我们非常羡慕使用WS*/SOAP协议服务的人:他们可以很容易地基于正式的WSDL接口规范生成一个客户端API，这样大大的简化了服务的调用。在很长一段时间内REST世界中缺乏广泛的正式规范和生成工具。但是Swagger的出现改变了这种局面
<!--more-->

## 处理RESTful服务现存的问题

当涉及到调用RESTful服务时，你会遇到两个问题：
- 首先要阅读并且理解RESTful服务接口文档
- 使用费劲的RESTful服务，你必须创建一个HTTP请求，包括正确的HTTP方法,正确的HTTP头，URL使用正确的参数，在HTTP Body里使用正确的JSON，这些特别是在服务API的变化时,维护成本特别高

当开发和维护一个RESTful服务时，你会遇到下面问题：
- 必须手动再编写一个api文档
- 当服务改变时，你必须兼顾同步更新api文档，这个过程是比较容易出现错误

然而，Swagger就可以解决这些问题

## 使用Swagger

Swagger的核心是一个用来描述RESTful服务规范的语言。但最重要的是，这个规范如同代码生成器和编辑器,提供了一个强大和积极成熟的生态系统工具,。这就是独特的卖点像WADL其他规范语言一样。

在开发流程中，有两种方式使用[Swagger](http://swagger.io/getting-started/)
- API优先
- 服务优先

### API优先

你可以先写你的Swagger API规范。在此基础上规范可以生成JAX-RS存根资源类和调用服务的客户端库。尽管可以利用[Swagger编辑器](http://editor.swagger.io/),用来创建这个规格，但这是一个非常艰难的任务，你需要学习这个规范语言。

然而,这是必须要做的，因为当你开始一个新项目涉及多个不同公司的团队时,你必须提交一个API规范。这样所有团队可以开始工作,不需要等待服务团队(例如通过生成一个服务的伪实现)

在这有一个很棒的的工具[swagger-inflector](http://swagger.io/writing-apis-with-the-swagger-inflector/)。它简化了基于给定的swagge文件JAX-RS服务器的实现,不再有JAX-RS生成的步骤。swagger-inflector在运行时将请求重定向到你的业务逻辑。如果你不提供业务逻辑,将返回伪实现的数据。因此,swagger-inflector还可用于对于一个给定swagger文件进行模拟服务。

### 服务优先

我更喜欢服务优先这种更加实际的方式，看下下面这工作流程图：
![使用Swagger服务开发优先流程图](http://oaefo3hoy.bkt.clouddn.com/16-9-3/35478687.jpg)

#### 服务端

通常,你开始通过编写JAX-RS资源类开发RESTful服务。然后通过对资源类添加Swaggeer的注解为RESTful API提供额外的文档。大部分的Swaggeer相关注解以前缀“@Api*”开始。资源类最终会像如下的代码:

```java
@Api(value = "customers", description = "RESTful API to interact with customer resources.")
@Path("customers")
public class CustomerResource {
	@Inject
	private CustomerDAO dao;

	@ApiOperation(value = "Get all customers", notes = "Get all customers matching the given search string.", responseContainer = "List", response = Customer.class)
	@GET
	@Path("/")
	@Produces(MediaType.APPLICATION_JSON)
	public List<Customer> getCustomers(
			@ApiParam(value = "The search string is used to find customer by their name. Not case sensetive.", required = false, defaultValue = "") @QueryParam("search") String searchString,
			@ApiParam(value = "Limits the size of the result set", required = false, defaultValue = "50") @QueryParam("limit") int limit) {
		List<Customer> customers = dao.getCustomers(searchString, limit);
		return customers;
	}

	@ApiOperation(value = "Create a new customer", notes = "Creates a new customer with the given name. The URL of the new customer is returned in the location header.")
	@POST
	@Path("/")
	@Consumes(MediaType.APPLICATION_JSON)
	public Response createEmployee(
			@ApiParam(value = "customer's name", required = true) String customerName)
			throws URISyntaxException {
		Customer customer = dao.createCustomer(customerName);

		URI uri = createNewLocationURI(customer.getId());
		Response response = Response.created(uri).build();
		return response;
	}
}
```

现在我们可以让Swagger处理带注解的资源类。它将生成正式规范。典型的应该是基于你的API规范以相同步骤创建的一个HTML文档。有几种方法可以实现这它:

- 可以在Maven构建过程中生成Swagger规范文档，这个可以通过我们的[swagger-maven-plugin](https://github.com/kongchen/swagger-maven-plugin)完成
- 如果正在使用Dropwizard，可以使用其捆绑插件[dropwizard-swagger](https://github.com/federecio/dropwizard-swagger)。它会在服务类启动过程中自动生成Swagger规范并且都发布在特定的url下面。这种方式非常非常不错,因为这样可以确保规范和文档跟已发布的服务保持最新。除此之外，不需要关心如何发布规范和文档。

下面是生成的API规范和我们的RESTful服务：
```yml
swagger: "2.0"
info: {}
basePath: "/"
tags:
- name: "customers"
paths:
  /customers:
    get:
      tags:
      - "customers"
      summary: "Get all customers"
      description: "Get all customers matching the given search string."
      operationId: "getCustomers"
      consumes:
      - "application/json"
      produces:
      - "application/json"
      parameters:
      - name: "search"
        in: "query"
        description: "The search string is used to find customer by their name. Not\
          \ case sensetive."
        required: false
        type: "string"
      - name: "limit"
        in: "query"
        description: "Limits the size of the result set"
        required: false
        type: "integer"
        default: "50"
        format: "int32"
      responses:
        200:
          description: "successful operation"
          schema:
            type: "array"
            items:
              $ref: "#/definitions/Customer"
    post:
      tags:
      - "customers"
      summary: "Creates a new customer"
      description: "Creates a new customer with the given name. "
      operationId: "createEmployee"
      consumes:
      - "application/json"
      parameters:
      - in: "body"
        name: "body"
        description: "customer's name"
        required: false
        schema:
          type: "string"
      responses:
        default:
          description: "successful operation"
```

生成的文档，如图：
![生成的api文档](http://oaefo3hoy.bkt.clouddn.com/16-9-3/92930277.jpg)


那么好处是什么呢？我们有了一个自动保持最新的API文档，文档是自动生成的，我们不需要手工去编写。另外，还提供了一个正式API规范标准。该规范可以用来生成一个客户端库(支持生成多个编程语言)，这是我们接下来要做的。

#### 客户端

客户端希望使用我们的RESTful服务,可以使用我们发布的Swagger规范来生成客户端库。有以下几种方法来生成：

**使用命令行工具**

为此，我们可以使用[swagger-codegen](https://github.com/swagger-api/swagger-codegen)命令行工具。下面是一个使用它的命令示例：
```shell
java -jar modules/swagger-codegen-cli/target/swagger-codegen-cli.jar generate \
  -i http://localhost:8080/swagger.json \
  -l java \
  -o samples/client/customer/java
```
上面示例的命令将生成一个完整的Maven项目，可以构建安装到我们自己的仓库中

**使用Maven插件**

然而,我建议通过[swagger-codegen-maven-plugin](https://github.com/swagger-api/swagger-codegen/tree/master/modules/swagger-codegen-maven-plugin) maven插件在一个现有的项目执行生成操作。通过这种方式,我们可以维护自己项目的pom(而不是生成一个新的项目)，并且完全控制构建配置(SVN/Git位置,构件的仓库url)。这非常适合于一个典型的项目基础设施搭建。

```xml
<properties>
	<yaml.file>${project.basedir}/src/main/resources/prozu-service.yaml</yaml.file>
	<generated-sources-path>${project.build.directory}/generated-sources</generated-sources-path>
	<generated-sources-java-path>main/java</generated-sources-java-path>
	<version.swagger.codegen>2.1.4</version.swagger.codegen>
	<!-- TODO add the properties from target/generated-sources/pom.xml here -->
</properties>
<dependencies>
	<!-- TODO add the dependencies from target/generated-sources/pom.xml here -->
</dependencies>

<build>
<plugins>
	<plugin>
		<groupId>io.swagger</groupId>
		<artifactId>swagger-codegen-maven-plugin</artifactId>
		<version>${version.swagger.codegen}</version>
		<configuration>
			<inputSpec>${yaml.file}</inputSpec>
			<configOptions>
				<sourceFolder>${generated-sources-java-path}</sourceFolder>
			</configOptions>
			<output>${generated-sources-path}</output>
		</configuration>
		<executions>
			<execution>
				<id>generate-swagger-javaclient</id>
				<phase>generate-sources</phase>
				<goals>
					<goal>generate</goal>
				</goals>
				<configuration>
					<language>java</language>
					<modelPackage>${groupId}.prozu.client.model</modelPackage>
					<apiPackage>${groupId}.prozu.client.api</apiPackage>
					<invokerPackage>${groupId}.prozu.client.invoker</invokerPackage>
				</configuration>
			</execution>
		</executions>
	</plugin>
	<plugin>
		<groupId>org.codehaus.mojo</groupId>
		<artifactId>build-helper-maven-plugin</artifactId>
		<version>1.9.1</version>
		<executions>
			<!-- TODO for eclipse/m2e users: install the m2e connector 'buildhelper' by selecting 'Discover new m2e connectors' while hovering over the follwoing execution tag -->
			<execution>
				<id>add-generated-source</id>
				<phase>initialize</phase>
				<goals>
					<goal>add-source</goal>
				</goals>
				<configuration>
					<sources>
						<source>${generated-sources-path}/${generated-sources-java-path}</source>
					</sources>
				</configuration>
			</execution>
		</executions>
	</plugin>
</plugins>

<!-- the following is only necessary if you are using eclipse and m2e -->
<pluginManagement>
	<plugins>
		<plugin>
			<groupId>org.eclipse.m2e</groupId>
			<artifactId>lifecycle-mapping</artifactId>
			<version>1.0.0</version>
			<configuration>
				<lifecycleMappingMetadata>
					<pluginExecutions>
						<pluginExecution>
							<pluginExecutionFilter>
								<groupId>io.swagger</groupId>
								<artifactId>swagger-codegen-maven-plugin</artifactId>
								<versionRange>[${version.swagger.codegen},)</versionRange>
								<goals>
									<goal>generate</goal>
								</goals>
							</pluginExecutionFilter>
							<action>
								<execute />
							</action>
						</pluginExecution>
					</pluginExecutions>
				</lifecycleMappingMetadata>
			</configuration>
		</plugin>
	</plugins>
</pluginManagement>
</build>
```
基于上面的Maven配置，运行`mvn generate-sources`生成客户端库代码到项目的target/generated-sources目录下，运行`mvn package`将会将生成的客户端库代码打包成一个jar包

### 客户端调用

最后，我们就可以在应用项目中使用那个客户端库调用服务了
```java
ApiClient apiClient = new ApiClient();
apiClient.setBasePath("http://localhost:8080");
CustomersApi customerApi = new CustomersApi(apiClient);
List<Customer> customers = customerApi.getCustomers("peter", 40);
```
通过上述调用代码，可以看出这大大的简化了调用RESTful服务的过程，这里我们使用抽象api的调用，非常低级的工作交给了客户端的lib完成

## 博主声明

本文属于博主原创英文技术文章翻译，原文连接请点击[这里](http://blog.philipphauer.de/enriching-restful-services-swagger/),翻译过程博主对原文会加入自己的理解，文章结构也会有稍微调整以更适合阅读

由于翻译和理解能力有限，文章难免会存在一定瑕疵希望读者见谅，如有任何错误纰漏欢迎在下方评论指正，谢谢！
