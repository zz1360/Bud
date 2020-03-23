# **Spring**

### Spring主要核心

(1)***控制反转(IOC)***：传统的java开发模式中，当需要一个对象时，我们会自己使用new或者getInstance等直接或者间接调用构造方法创建一个对象，而在Spring开发模式中，Spring容器使用了工厂模式为我们创建了所需要的对象，我们使用时不需要自己去创建，直接调用Spring为我们提供的对象即可，这就是控制反转的思想。实例化一个java对象有三种方式：使用类构造器，使用静态工厂方法，使用实例工厂方法，当使用spring时我们就不需要关心通过何种方式实例化一个对象，spring通过控制反转机制自动为我们实例化一个对象。（***底层原理是反射***）

(2)***依赖注入(DI)***：Spring使用JavaBean对象的Set方法或者带参数的构造方法为我们在创建所需对象时将其属性自动设置为所需要的值 的过程就是依赖注入的基本思想。

(3)***面向切面编程(AOP)***：在面向对象编程(OOP)思想中，我们将事物纵向抽象成一个个的对象。而在面向切面编程中，我们将一个个对象某些类似的方面横向抽象成一个切面，对这个切面进行一些如权限验证，事物管理，记录日志等公用操作处理的过程就是面向切面编程的思想。（***底层原理是动态代理***）

### IoC 和 DI的区别

IoC 控制反转，指将对象的创建权，反转到Spring容器 ， DI 依赖注入，指Spring创建对象的过程中，将对象依赖属性通过配置进行注入

<img src="/img/spring-1.png">

spring支持构造器注入和setter方法注入

  构造器注入，通过 <constructor-arg> 元素完成注入

setter方法注入， 通过<property> 元素完成注入【开发中常用方式】

 

在Spring中，所有管理的对象都是JavaBean对象，而BeanFactory和ApplicationContext就是spring框架的两个IOC容器，现在一般使用ApplicationContext，其不但包含了BeanFactory的作用，同时还进行更多的扩展。

### Spring Bean生命周期

1.Spring容器 从XML 文件中读取Bean的定义，并实例化Bean。

2.Spring根据Bean的定义填充所有的属性。

3.如果Bean实现了BeanNameAware 接口，Spring 传递bean 的ID 到 setBeanName方法。

4.如果Bean 实现了 BeanFactoryAware 接口， Spring传递beanfactory 给setBeanFactory 方法。

5.如果有任何与bean相关联的BeanPostProcessors，Spring会在postProcesserBeforeInitialization()方法内调用它们。

6.如果bean实现IntializingBean了，调用它的afterPropertySet方法，如果bean声明了初始化方法，调用此初始化方法。

7.如果有BeanPostProcessors 和bean 关联，这些bean的postProcessAfterInitialization() 方法将被调用。

8.如果bean实现了 DisposableBean，它将调用destroy()方法。

注意：

有两个重要的bean 生命周期方法，第一个是**setup()** ， 它是在容器加载bean的时候被调用。第二个方法是 **teardown()** 它是在容器卸载类的时候被调用。

The bean 标签有两个重要的属性init-method和destroy-method。使用它们你可以自己定制初始化和注销方法。它们也有相应的注解@PostConstruct和@PreDestroy。

### springbean的作用域

四种，singleton（默认值），prototype，request，session

两种配置方式：

1)Xml配置文件

<img src="/img/spring-2.png">

2)注解的方式配置

<img src="/img/spring-3.png">

### Spring 中的设计模式

1. 代理模式—Spring中两种代理方式，若目标对象实现了若干接口，spring使用JDK的java.lang.reflect.Proxy类代理，若目标对象没有实现任何接口，spring使用CGLIB库生成目标对象的子类。

2. 单例模式—在spring配置文件中定义的bean默认为单例模式。

3. 模板方法模式—用来解决代码重复的问题。

   比如： RestTemplate, JmsTemplate, JpaTemplate,redistemplate。

4. 前端控制器模式—Srping提供了DispatcherServlet来对请求进行分发。

5. 视图帮助(**View Helper** )—Spring提供了一系列的JSP标签，高效宏来辅助将分散的代码整合在视图里

6. 依赖注入—贯穿于BeanFactory/ApplicationContext接口的核心理念。

7. 工厂模式—在工厂模式中，我们在创建对象时不会对客户端暴露创建逻辑，并且是通过使用一个共同的接口来指向新创建的对象。Spring中使用BeanFactory用来创建对象的实例。

### Spring注解

Spring在2.5版本以后开始支持用注解的方式来配置依赖注入。可以用注解的方式来替代XML方式的bean描述，可以将bean描述转移到组件类的内部，只需要在相关类上、方法上或者字段声明上使用注解即可。**注解注入将会被容器在XML注入之前被处理，所以后者会覆盖掉前者对于同一个属性的处理结果。**

注解装配在Spring中是默认关闭的。所以需要在Spring文件中配置一下才能使用基于注解的装配模式。如果你想要在你的应用程序中使用关于注解的方法的话，请参考如下的配置。

```xml

	<context:annotation-config/>
    <!-- bean definitions go here -->

```

在 `<context:annotation-config/>`标签配置完成以后，就可以用注解的方式在Spring中向属性、方法和构造方法中自动装配变量。

几种比较重要的注解类型：

​	 @Component :标识了一个受Spring管理的组件，泛指组件，当组件不好归类的时候，我们可以使用这个注解		进行标注

​	@Respository： 标识持久层，数据访问组件，即DAO组件

​	@Service： 标识业务层组件

​	@Controller ：标识表现层,控制层组件

***@RequestMapping*** 

​	@RequestMapping 是用来处理请求地址映射的注解，可用于类或方法上。用在类上，表示类中所有的响应的		方法都是以该地址作为父路径来访问，用在方法上表示该方法的访问的路径；两者结合起来的访问的路径为		完整的访问某个方法的路径。

@RequestMapping注解的属性：

​	value：指定请求的实际的地址，指定的地址可以是URI Template模式；

​	method：指定访问的方法

​	consumes：指定处理请求的内容类型，比如aplication/json;text/html

​	produces：指定返回的内容的类型

​	params：指定request中必须包含某些参数值时，才让该方法处理请求

​	headers：指定request中必须包含指定的header值，才能让该方法处理请求



***@Resource和@Autowired***

​	@Resource和@Autowired都是做bean的注入时使用,都可以写在字段上，或者是setter方法上

​	@Autowired注解是按照类型（byType）装配依赖对象（由Spring提供）

​		如果在此基础上想通过byName来装配，可以结合@Qualifier注解来一起使用。

​	@Resource默认按照byName自动注入

​	@Resource有两个重要的属性，name和type

​	@Resource如果写属性name，则按照byName来装配，如果属性为type，则按照byType来装配，不写按照默		认的方式来装配（byName）

### Spring事务

Spring支持两种类型的事务管理：

1.编程式事务管理：这意味你通过编程的方式管理事务，给你带来极大的灵活性，但是难维护。

2.**声明式事务管理：这意味着你可以将业务代码和事务管理分离，你只需用注解和XML配置来管理事务**。

<img src="/img/spring-4.png">

 

Spring事务配置示例（使用tx标签配置的拦截器）

```xml
  <!-- 定义事务管理器（声明式的事务） --> 
   <bean id="transactionManager"
     class="org.springframework.orm.hibernate3.HibernateTransactionManager">
     <property name="sessionFactory" ref="sessionFactory" />
   </bean>
  <!-- 配置Advice通知 -->
   <tx:advice id="txAdvice" transaction-manager="transactionManager">
     <tx:attributes>
       <tx:method name="*" propagation="REQUIRED" />
     </tx:attributes>
   </tx:advice>
   <!-- 配置切点切面 -->
   <aop:config>
     <aop:pointcut id="interceptorPointCuts"
       expression="execution(* com.bluesky.spring.dao.*.*(..))" />
     <aop:advisor advice-ref="txAdvice"
       pointcut-ref="interceptorPointCuts" />    
   </aop:config> 
```

### spring的事务传播行为

spring事务的传播行为说的是，当多个事务同时存在的时候，spring如何处理这些事务的行为。

① PROPAGATION_REQUIRED：如果当前没有事务，就创建一个新事务，如果当前存在事务，就加入该事务，该设置是最常用的设置。

② PROPAGATION_SUPPORTS：支持当前事务，如果当前存在事务，就加入该事务，如果当前不存在事务，就以非事务执行。‘

③ PROPAGATION_MANDATORY：支持当前事务，如果当前存在事务，就加入该事务，如果当前不存在事务，就抛出异常。

④ PROPAGATION_REQUIRES_NEW：创建新事务，无论当前存不存在事务，都创建新事务。

⑤ PROPAGATION_NOT_SUPPORTED：以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。

⑥ PROPAGATION_NEVER：以非事务方式执行，如果当前存在事务，则抛出异常。

⑦ PROPAGATION_NESTED：如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，则按REQUIRED属性执行。

### Spring如何处理线程并发问题

在一般情况下，只有无状态的Bean才可以在多线程环境下共享，在Spring中，绝大部分Bean都可以声明为singleton作用域，因为Spring对一些Bean中非线程安全状态采用**ThreadLocal**进行处理，解决线程安全问题。

