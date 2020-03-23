#  **springboot**

## springboot简介

Spring Boot是由Pivotal团队提供的全新框架，其设计目的是用来简化新Spring应用的开发过程。

Spring Boot让我们的Spring应用变的更轻量化。比如：你可以仅仅依靠一个Java类来运行一个Spring应用。你也可以打包你的应用为jar并通过使用java -jar来运行你的Spring Web应用。

Spring Boot的主要优点：

- 为所有Spring开发者更快的入门
- 简化项目配置
- 内嵌式容器简化Web项目

·    springboot整合的框架统一管理版本，不会存在版本冲突

·    springcloud基于springboot开发

Spring Boot 是微服务中最好的 Java 框架. 

## Spring boot自动配置原理

Spring Boot关于自动配置的源码在spring-boot-autoconfigure-x.x.x.x.jar中：

<img src="/img/spring-7.png">

当然，自动配置原理的相关描述，官方文档貌似是没有提及。不过我们不难猜出，Spring Boot的启动类上有一个@SpringBootApplication注解，这个注解是Spring Boot项目必不可少的注解。那么自动配置原理一定和这个注解有着千丝万缕的联系！

@EnableAutoConfiguration

<img src="/img/spring-8.png">

@SpringBootApplication是一个复合注解或派生注解，在@SpringBootApplication中有一个注解@EnableAutoConfiguration，翻译成人话就是开启自动配置，其定义如下：

<img src="/img/spring-9.png">

而这个注解也是一个派生注解，其中的关键功能由@Import提供，其导入的AutoConfigurationImportSelector的selectImports()方法通过SpringFactoriesLoader.loadFactoryNames()扫描所有具有META-INF/spring.factories的jar包。spring-boot-autoconfigure-x.x.x.x.jar里就有一个这样的spring.factories文件。

这个spring.factories文件也是一组一组的key=value的形式，其中一个key是EnableAutoConfiguration类的全类名，而它的value是一个xxxxAutoConfiguration的类名的列表，这些类名以逗号分隔，如下图所示：

<img src="/img/spring-10.png">

这个@EnableAutoConfiguration注解通过@SpringBootApplication被间接的标记在了Spring Boot的启动类上。在SpringApplication.run(...)的内部就会执行selectImports()方法，找到所有JavaConfig自动配置类的全限定名对应的class，然后将所有自动配置类加载到Spring容器中。

**自动配置生效：**

每一个XxxxAutoConfiguration自动配置类都是在某些条件之下才会生效的，这些条件的限制在Spring Boot中以注解的形式体现，常见的条件注解有如下几项：

@ConditionalOnBean：当容器里有指定的bean的条件下。

@ConditionalOnMissingBean：当容器里不存在指定bean的条件下。

@ConditionalOnClass：当类路径下有指定类的条件下。

@ConditionalOnMissingClass：当类路径下不存在指定类的条件下。

@ConditionalOnProperty：指定的属性是否有指定的值，比如@ConditionalOnProperties(prefix=”xxx.xxx”, value=”enable”, matchIfMissing=true)，代表当xxx.xxx为enable时条件的布尔值为true，如果没有设置的情况下也为true。

以ServletWebServerFactoryAutoConfiguration配置类为例，解释一下全局配置文件中的属性如何生效，比如：server.port=8081，是如何生效的（当然不配置也会有默认值，这个默认值来自于org.apache.catalina.startup.Tomcat）。

<img src="/img/spring-11.png">

在ServletWebServerFactoryAutoConfiguration类上，有一个@EnableConfigurationProperties注解：开启配置属性，而它后面的参数是一个ServerProperties类，这就是习惯优于配置的最终落地点。

<img src="/img/spring-12.png">

在这个类上，我们看到了一个非常熟悉的注解：@ConfigurationProperties，它的作用就是从配置文件中绑定属性到对应的bean上，而@EnableConfigurationProperties负责导入这个已经绑定了属性的bean到spring容器中（见上面截图）。那么所有其他的和这个类相关的属性都可以在全局配置文件中定义，也就是说，真正“限制”我们可以在全局配置文件中配置哪些属性的类就是这些XxxxProperties类，它与配置文件中定义的prefix关键字开头的一组属性是唯一对应的。

至此，我们大致可以了解。在全局配置的属性如：server.port等，通过@ConfigurationProperties注解，绑定到对应的XxxxProperties配置实体类上封装为一个bean，然后再通过@EnableConfigurationProperties注解导入到Spring容器中。而诸多的XxxxAutoConfiguration自动配置类，就是Spring容器的JavaConfig形式，作用就是为Spring 容器导入bean，而所有导入的bean所需要的属性都通过xxxxProperties的bean来获得。

可能到目前为止还是有所疑惑，但面试的时候，其实远远不需要回答的这么具体，你只需要这样回答：

**Spring Boot启动的时候会通过@EnableAutoConfiguration注解找到META-INF/spring.factories配置文件中的所有自动配置类，并对其进行加载，而这些自动配置类都是以AutoConfiguration结尾来命名的，它实际上就是一个JavaConfig形式的Spring容器配置类，它能通过以Properties结尾命名的类中取得在全局配置文件中配置的属性如：server.port，而XxxxProperties类是通过@ConfigurationProperties注解与全局配置文件中对应的属性进行绑定的。**

## springboot常见面试题

##### 什么是自动配置？

Spring 和 SpringMVC 的问题在于需要配置大量的参数。

```xml
<bean

 class="org.springframework.web.servlet.view.InternalResourceViewResolver">

<property name="prefix">

​     <value>/WEB-INF/views/</value>

  </property>

  <property name="suffix">

​     <value>.jsp</value>

  </property>

 </bean>

 <mvc:resources mapping="/webjars/**" location="/webjars/"/>
```

我们能否带来更多的智能？当一个 MVC JAR 添加到应用程序中的时候，我们能否自动配置一些 beans？

Spring 查看（CLASSPATH 上可用的框架）已存在的应用程序的配置。在此基础上，Spring Boot 提供了配置应用程序和框架所需要的基本配置。这就是自动配置。

##### Spring 是如何快速创建产品就绪应用程序的？

Spring Boot 致力于快速产品就绪应用程序。为此，它提供了一些譬如高速缓存，日志记录，监控和嵌入式服务器等开箱即用的非功能性特征。

spring-boot-starter-actuator - 使用一些如监控和跟踪应用的高级功能

spring-boot-starter-undertow, spring-boot-starter-jetty, spring-boot-starter-tomcat - 选择您的特定嵌入式 Servlet 容器

spring-boot-starter-logging - 使用 logback 进行日志记录

spring-boot-starter-cache - 启用 Spring Framework 的缓存支持

注：Spring Boot 2.0 需要 Java8 或者更新的版本。Java6 和 Java7 已经不再支持。

推荐阅读：

https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-2.0.0-M1-Release-Notes

#####  创建一个 Spring Boot Project 的最简单的方法是什么？

Spring Initializr是启动 Spring Boot Projects 的一个很好的工具。

 https://start.spring.io/

就像上图中所展示的一样，我们需要做一下几步：

​		登录 Spring Initializr，按照以下方式进行选择：

​		选择 com.in28minutes.springboot 为组

​		选择 studet-services 为组件

​		选择下面的依赖项

​		Web

​		Actuator

​		DevTools

​		点击生 GenerateProject

​		将项目导入 Eclipse。文件 - 导入 - 现有的 Maven 项目

##### Spring Initializr 是创建 Spring Boot Projects 的唯一方法吗？

不是的。

Spring Initiatlizr 让创建 Spring Boot 项目变的很容易，但是，你也可以通过设置一个 maven 项目并添加正确的依赖项来开始一个项目。

我们可以使用两种方法来创建项目。

第一种方法是 start.spring.io 。

另外一种方法是在项目的标题为“Basic Web Application”处进行手动设置。

​	手动设置一个 maven 项目

​	这里有几个重要的步骤：

​	在 Eclipse 中，使用文件 - 新建 Maven 项目来创建一个新项目

​	添加依赖项。

​	添加 maven 插件。

​	添加 Spring Boot 应用程序类。

到这里，准备工作已经做好！

##### 为什么我们需要 spring-boot-maven-plugin?

spring-boot-maven-plugin 提供了一些像 jar 一样打包或者运行应用程序的命令。

spring-boot:run 运行你的 SpringBooty 应用程序。

spring-boot：repackage 重新打包你的 jar 包或者是 war 包使其可执行

spring-boot：start 和 spring-boot：stop 管理 Spring Boot 应用程序的生命周期（也可以说是为了集成测试）。

spring-boot:build-info 生成执行器可以使用的构造信息。

##### 什么是嵌入式服务器？我们为什么要使用嵌入式服务器呢?

思考一下在你的虚拟机上部署应用程序需要些什么。

​	第一步： 安装 Java

​	第二部： 安装 Web 或者是应用程序的服务器（Tomat/Wbesphere/Weblogic 等等）

​	第三部： 部署应用程序 war 包

如果我们想简化这些步骤，应该如何做呢？

让我们来思考如何使服务器成为应用程序的一部分？

你只需要一个安装了 Java 的虚拟机，就可以直接在上面部署应用程序了，
 是不是很爽？

这个想法是嵌入式服务器的起源。

当我们创建一个可以部署的应用程序的时候，我们将会把服务器（例如，tomcat）嵌入到可部署的服务器中。

例如，对于一个 Spring Boot 应用程序来说，你可以生成一个包含 Embedded Tomcat 的应用程序 jar。你就可以像运行正常 Java 应用程序一样来运行 web 应用程序了。

嵌入式服务器就是我们的可执行单元包含服务器的二进制文件（例如，tomcat.jar）。

##### 我们能否在 spring-boot-starter-web 中用 jetty 代替 tomcat？

在 spring-boot-starter-web 移除现有的依赖项，并把下面这些添加进去。

```xml
<dependency>

  <groupId>org.springframework.boot</groupId>

  <artifactId>spring-boot-starter-web</artifactId>

  <exclusions>

​     <exclusion>

​       <groupId>org.springframework.boot</groupId>

​       <artifactId>spring-boot-starter-tomcat</artifactId>

​     </exclusion>

  </exclusions>

</dependency>

<dependency>

  <groupId>org.springframework.boot</groupId>

  <artifactId>spring-boot-starter-jetty</artifactId>

</dependency>
```

##### 如何使用 Spring Boot 生成一个 WAR 文件？

1.第一步：提供一个SpringBootServletInitializer的子类并重写configure方法。Spring Framework的Servlet 3.0支持并允许您在由Servlet容器启动时配置您的应用程序。通常，您需要更新您的应用程序的主类去继承SpringBootServletInitializer:

2.第二步：您需要修改你的pom.xml配置文件，以便于您的项目生成的是一个war文件，而不是jar文件。如果您使用的是Maven并且使用了spring-boot-starter-parent(它会配置Maven的war插件给您)：

```xml
<packaging>war</packaging>
```

注：如果您是用的是Gradle，你需要修改build.gradle将war插件应用于项目：

```xml
apply plugin: 'war'
```

最后一步：确保嵌入的Servlet容器不会干扰war文件将部署的servlet容器，为此，需要对嵌入的servlet容器进行标记，并提供其依赖项。

<img src="/img/spring-13.png">

推荐阅读:

https://spring.io/guides/gs/convert-jar-to-war/

下面有 spring 说明文档直接的链接地址：

https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#build-tool-plugins-maven-packagin

##### 我们如何连接一个像 MySQL 或者 orcale 一样的外部数据库？

让我们以 MySQL 为例来思考这个问题：

第一步 - 把 mysql 连接器的依赖项添加至 pom.xml

```xml
<dependency>

  <groupId>mysql</groupId>

  <artifactId>mysql-connector-java</artifactId>

</dependency>
```

第二步 - 从 pom.xml 中移除 H2 的依赖项

或者至少把它作为测试的范围。

```xml
<!--

<dependency>

  <groupId>com.h2database</groupId>

  <artifactId>h2</artifactId>

<scope>test</scope>

</dependency>

-->  
```

第三步 - 安装你的 MySQL 数据库

更多的来看看这里 -https://github.com/in28minutes/jpa-with-hibernate#installing-and-setting-up-mysql

第四步 - 配置你的 MySQL 数据库连接

​	配置 application.properties

​	spring.jpa.hibernate.ddl-auto=none

​	spring.datasource.url=jdbc:mysql://localhost:3306/todo_example

​	spring.datasource.username=todouser

​	spring.datasource.password=YOUR_PASSWORD  

第五步 - 重新启动，你就准备好了！

就是这么简单！

#####  Spring Boot 配置的默认 H2 数据库的名字是上面？为什么默认的数据库名字是 testdb？

在 application.properties 里面，列出了所有的默认值

https://docs.spring.io/spring-boot/docs/current/reference/html/common-application-properties.html

找到下面的属性

spring.datasource.name=testdb # Name of the datasource.

如果你使用了 H2 内部存储数据库，它里面确定了 Spring Boot 用来安装你的 H2 数据库的名字。

##### 配置文件的需求是什么？

企业应用程序的开发是复杂的，你需要混合的环境：

Dev（开发环境）

QA（测试环境）

Stage（展示，演示环境）

Production（生产环境）

在每个环境中，你想要不同的应用程序配置。

配置文件有助于在不同的环境中进行不同的应用程序配置。

Spring 和 Spring Boot 提供了你可以制定的功能。

不同配置文件中，不同环境的配置是什么？

为一个制定的环境设置活动的配置文件。

Spring Boot 将会根据特定环境中设置的活动配置文件来选择应用程序的配置。

##### 如何使用配置文件通过 Spring Boot 配置特定环境的配置？

配置文件不是设别环境的关键。

在下面的例子中，我们将会用到两个配置文件

dev

prod

缺省的应用程序配置在 application.properties 中。让我们来看下面的例子：

application.properties

basic.value= true

basic.message= Dynamic Message

basic.number= 100

我们想要为 dev 文件自定义 application.properties 属性。我们需要创建一个名为 application-dev.properties 的文件，并且重写我们想要自定义的属性。

application-dev.properties

basic.message: Dynamic Message in DEV

一旦你特定配置了配置文件，你需要在环境中设定一个活动的配置文件。

有多种方法可以做到这一点：

在 VM 参数中使用 Dspring.profiles.active=prod

在 application.properties 中使用 spring.profiles.active=prod