# **定时任务专题**

### 什么是任务调度技术 

在企业级应用中，经常会制定一些“计划任务”，即在某个时间点做某件事情，核心是以时间为关注点，即在一个特定的时间点，系统执行指定的一个操作。常见的任务调度框架有Quartz 和 SpringTask【spring内置】以及jdk的Timer类（不常用）等。

### Quartz简介 

Quartz作为一个优秀的开源调度框架，完全由Java写成。Quartz 具有以下特点：

强大的调度功能，

支持立即调度、定时调度、周期调度、并发调度； 灵活的应用方式，

支持job间通过listener实现依赖调度，可以方便的进行调度组合

Quartz既可以单独使用也可以跟spring框架整合使用，在实际开发中一般会使用后者。

##### 配置文件 applicationContext_job.xml 各个属性作用

1. Job：表示一个任务（工作），要执行的具体内容。 

2. JobDetail：表示一个具体的可执行的调度程序，Job 是这个可执行程调度程序所要执行的内容，另外JobDetail 还包含了这个任务调度的方案和策略。

3. Trigger：代表一个调度参数的配置，什么时候去调。

4. Scheduler：代表一个调度容器，一个调度容器中可以注册多个 JobDetail 和 Trigger。当 Trigger 与

   JobDetail 组合，就可以被 Scheduler 容器调度了。

##### 如何监控Quartz的job执行状态：运行中，暂停中，等待中？

目前我能想到的解决办法是通过往表（新建一个操作日志表）里插入日志的形式：

运行中：通过JobListener监听器来实现运行时更改表信息

暂停中：调用scheduler.pauseTrigger()方法时，更改表中job信息

等待中：新添加的job默认给其等待中的状态，也是更改表中的job信息

但是上面这种形式的麻烦之处是得频繁的往表里插入数据。

### Springtask

Springtask是spring内置的，上手简单。SprngTask没有专门的包，其核心类位于spring-context包中。所以引入spring的核心包此功能即可使用。

使用方法：

1.基于xml配置文件

<img src="/img/springtask-1.png">

重点是引入task的schema:

```xml
xmlns:task="http://www.springframework.org/schema/task"

http://www.springframework.org/schema/task http://www.springframework.org/schema/task/spring-task-3.2.xsd
```

2.基于注解的

注解使用必须在xml中开启注解任务（springboot项目只需在启动类上加上@EnableScheduling即可）

<img src="/img/springtask-2.png">

然后使用@Scheduled在想要执行的方法上

<img src="/img/springtask-3.png">

拓展：SpringTask 默认是单线程的；在实际开发中，不希望所有的任务都运行在一个线程中，想要改成多线程，给SpringTask提供一个多线程的TaskScheduler，Spring已经有默认实现

### Cron 表达式详解

cron表达式分为七个域，之间使用空格分隔。其中最后一个域（年）可以为空。每个域

都有自己允许的值和一些特殊字符构成。使用这些特殊字符可以使我们定义的表达式更

加灵活。

下面是对这些特殊字符的介绍：

逗号（,）：指定一个值列表，例如使用在月域上1,4,5,7表示1月、4月、5月和7月

横杠（-）：指定一个范围，例如在时域上3-6表示3点到6点（即3点、4点、5点、6点）

星号（*）：表示这个域上包含所有合法的值。例如，在月份域上使用星号意味着每个月

都会触发

斜线（/）：表示递增，例如使用在秒域上0/15表示每15秒

问号（?）：只能用在日和周域上，但是不能在这两个域上同时使用。表示不指定

井号（#）：只能使用在周域上，用于指定月份中的第几周的哪一天，例如6#3，意思是

某月的第三个周五 (6=星期五，3意味着月份中的第三周)

L：某域上允许的最后一个值。只能使用在日和周域上。当用在日域上，表示的是在月域

上指定的月份的最后一天。用于周域上时，表示周的最后一天，就是星期六

W：W 字符代表着工作日 (星期一到星期五)，只能用在日域上，它用来指定离指定日的

最近的一个工作日