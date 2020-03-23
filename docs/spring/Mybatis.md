# **Mybatis**框架

### Mybatis执行流程

​    <img src="/img/spring-6.png">

 

1. mybatis配置

   SqlMapConfig.xml，此文件作为mybatis的全局配置文件，配置了mybatis的运行环境等信息。

   mapper.xml文件即sql映射文件，文件中配置了操作数据库的sql语句。此文件需要在SqlMapConfig.xml中加载。

2. 通过mybatis环境等配置信息构造SqlSessionFactory即会话工厂

3. 由会话工厂创建sqlSession即会话，操作数据库需要通过sqlSession进行。

4. mybatis底层自定义了Executor执行器接口操作数据库，Executor接口有两个实现，一个是基本执行器、一个是缓存执行器。

5. Mapped Statement也是mybatis一个底层封装对象，它包装了mybatis配置信息及sql映射信息等。mapper.xml文件中一个sql对应一个Mapped Statement对象，sql的id即是Mapped statement的id。

6. Mapped Statement对sql执行输入参数进行定义，包括HashMap、基本类型、pojo，Executor通过Mapped Statement在执行sql前将输入的java对象映射至sql中，输入参数映射就是jdbc编程中对preparedStatement设置参数。

7. Mapped Statement对sql执行输出结果进行定义，包括HashMap、基本类型、pojo，Executor通过Mapped Statement在执行sql后将输出结果映射至java对象中，输出结果映射过程相当于jdbc编程中对结果的解析处理过程。

 

### Mybatis缓存(了解)

`现在一般用redis做缓存`

一级缓存：Mybatis的一级缓存的作用域是session，当openSession()后，如果执行相同的SQL（相同语句和参数），Mybatis不进行执行SQL，而是从缓存中命中返回。一级缓存默认开启（思考下设计缓存的目的）

二级缓存：Mybatis的二级缓存的作用域是一个mapper的namespace，同一个namespace中查询sql可以从缓存中命中。二级缓存是可以跨session的。

### mybatis-plus

mybatisplus,是一个mybatis的第三方增强库,简单来说就是增强了mybatis的功能,让mybatis更好用,mybatisplus给的官方定义是Mybatis-Plus（简称MP）是一个 Mybatis 的增强工具，在 Mybatis 的基础上**只做增强不做改变**，为简化开发、提高效率而生。http://mp.baomidou.com/#/这是mybatisplus的网站,有兴趣的可以自己看一下.

下面就简单说下他具体有哪些好用的功能.

**1.BaseMapper**集成单表CRUD功能

假如有一个实体类User,要使用mybatis实现增删改查需要写很多的xml配置文件来实现,但是用mybatisplus之后直接继承BaseMapper接口即可实现,无需编写任何的xml文件,代码如下

```java
public interface UserMapper extends BaseMapper{ }
//继承之后即可直接使用BaseMapper中的增删改查方法,例如userMapper.selectById(id); userMapper.deleteById(id); userMapper.updateById(user); userMapper.insert(user);
```

**2.集成分页功能**

mybatisplus集成了很好用的分页功能,在执行sql查询的时候,传入一个pagination的对象即可实现自动分页.Pagination对象中主要的成员变量如下:

**3.条件构造器实现复杂的sql查询**

这个功能主要用户实现动态的sql拼接,主要用到的对象是EntityWrapper,通过生成一个EntityWrapper对象,然后通过该wrapper对象的方法实现sql的拼接.例如可以wrapper的eq方法实现sql中的 = 功能,like方法实现sql中的 like功能,gt方法实现sql的 >

功能等等等等,这块大家可以自己看下wrapper中的方法就可以了,非常简单,拿来即可用.

**4.代码自动生成**

mybatis generator不知道大家有没有用过,就是根据表结构自动生成实体类和mapper及xml配置文件,mybatisplus在这个基础上提供了更强大的代码自动生成功能,

### Mybatis常见面试问题

##### mybatis使用mapper接口开发需要遵循哪些规范？

① Mapper接口方法名和mapper.xml中定义的每个sql的id相同；

② Mapper接口方法的参数类型和mapper.xml中定义的每个sql 的parameterType的类型相同；

③ Mapper接口方法的返回值类型和mapper.xml中定义的每个sql的resultType的类型相同；

④ Mapper.xml文件中的namespace即是mapper接口的类路径。

#####  Xml映射文件中，除了常见的select|insert|updae|delete标签之外，还有哪些标签？

答：<resultMap>、<parameterMap>、<sql>、<include>、<selectKey>，加上动态sql的9个标签，其中<sql>为sql片段标签，通过<include>标签引入sql片段，<selectKey>为不支持自增的主键生成策略标签。

##### mybatis中#{}和${}的区别是什么？

\#{}是预编译处理，${}是字符串替换。

Mybatis在处理#{}时，会将sql中的#{}替换为?号，调用**PreparedStatement**的set方法来赋值（原理）；

Mybatis在处理${}时，就是把${}替换成变量的值。

使用#{}可以有效的防止SQL注入，提高系统安全性。

##### Mybatis是否支持延迟加载？如果支持，它的实现原理是什么？

Mybatis仅支持association关联对象和collection关联集合对象的延迟加载，association指的就是一对一，collection指的就是一对多查询。在Mybatis配置文件中，可以配置是否启用延迟加载lazyLoadingEnabled=true|false。

它的原理是，使用CGLIB创建目标对象的代理对象，当调用目标方法时，进入拦截器方法，比如调用a.getB().getName()，拦截器invoke()方法发现a.getB()是null值，那么就会单独发送事先保存好的查询关联B对象的sql，把B查询上来，然后调用a.setB(b)，于是a的对象b属性就有值了，接着完成a.getB().getName()方法的调用。这就是延迟加载的基本原理。

当然了，不光是Mybatis，几乎所有的包括Hibernate，支持延迟加载的原理都是一样的。

##### Mybatis是如何进行分页的？分页插件的原理是什么？

Mybatis使用RowBounds对象进行分页，它是针对ResultSet结果集执行的内存分页，而非物理分页。可以在sql内直接书写带有物理分页的参数来完成物理分页功能，也可以使用分页插件来完成物理分页。

分页插件的基本原理是使用Mybatis提供的插件接口，实现自定义插件，在插件的拦截方法内拦截待执行的sql，然后重写sql，根据dialect方言，添加对应的物理分页语句和物理分页参数。（一般使用pagehelper插件）