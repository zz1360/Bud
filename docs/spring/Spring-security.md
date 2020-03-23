# **Spring security权限认证专题**

### spring security 简介

spring security 的核心功能主要包括：

**认证** （你是谁）

**授权** （你能干什么）

**攻击防护** （防止伪造身份）

### spring security原理

**SpringSecurity** **采用的是责任链的设计模式，它有一条很长的过滤器链。**

下面详细介绍下原理：

**认证(authentication)**

认证的过程相对简单，基本都是判断当前正在操作的用户(Principal)和密码(credentials)是否匹配。对与简单登录的方式，就是去匹配用户名和密码；对于单点登录，可能就是去验证token是否有效了。获取到这些信息后，会包装到对象Authentication中。这个Authentication就是后续Filter决定页面跳转的依据。Authentication定义如下：

<img src="/img/spring-security.png">

**授权(authorization)**

对于需要控制权限的URL，一般都要三部分信息：

​	URL的pattern，即对哪些URL进行权限控制

​	设置权限，即最低需要什么权限才能访问这些URL

​	当前用户是什么权限

获取用户权限

前两点可以通过配置`<security:intercept-url pattern="/abc/**" access="hasRole('USER')"/>`指定，并被包装成SecurityMetadataSource对象，那么当前登录用户的权限从哪里获取？

Spring Security定义了如下接口， UserDetailsService用于获取UserDetail，而UserDetail里包含权限。

<img src="/img/spring-security-2.png">

具体的获取UserDetail的过程，要根据具体的业务进行处理，比如从数据库获取，或者从缓存中得到。获取之后包装为Authentication对象供后续Filter使用。

可以参考如下代码：

```java
@Component
public class SpringSecurityUserService implements UserDetailsService {
    //使用dubbo通过网络远程调用服务提供方获取数据库中的用户信息
    @Reference
    private UserService userService;

    //根据用户名查询数据库获取用户信息
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        User user = userService.findByUsername(username);
        if(user == null){
            //用户名不存在
            return null;
        }

        List<GrantedAuthority> list = new ArrayList<>();

        //动态为当前用户授权
        Set<Role> roles = user.getRoles();
        for (Role role : roles) {
            //遍历角色集合，为用户授予角色
            list.add(new SimpleGrantedAuthority(role.getKeyword()));
            Set<Permission> permissions = role.getPermissions();
            for (Permission permission : permissions) {
                //遍历权限集合，为用户授权
                list.add(new SimpleGrantedAuthority(permission.getKeyword()));
            }
        }

        org.springframework.security.core.userdetails.User securityUser =
        new org.springframework.security.core.userdetails.User(username,user.getPassword(),list);

        return securityUser;
    }
}
```

关键的filter

**AbstractAuthenticationProcessingFilter**， 用于认证，其步骤如下：

1.  从request获取相关用户信息(密码、token等)构造Authentication；
2. AuthenticationManager其中的AuthenticationProvider进行Authentication验证；Provider里可能会获取UserDetails(包含用户具有的权限)放入Authentication中；若验证通过，则返回该Authentication中；否则抛异常；
3. 该Filter捕获到异常，则导航到相关error或login页面；若正常，则根据具体的代码设置，由后面的filter继续处理或直接访问到资源；  

<img src="/img/spring-security-3.png">

当然，这个filter并不是每个url都会去拦截的，只有满足一定条件的url才会去拦截。比如不需要权限控制的url就不会被拦截。

FilterSecurityInterceptor和ExceptionTranslationFilter

这两个filter用于授权。其在Spring Security的Filter Chain中处于很靠后的位置。

FilterSecurityInterceptor的关键代码如下：

<img src="/img/spring-security-4.png">

ExceptionTranslationFilter关键代码如下：

<img src="/img/spring-security-5.png">

ExceptionTranslationFilter会根据抛出的异常是AccessDeniedException还是AuthenticationException来判断导航页面。

在Spring Security所有的Filter中，只有两种filter是可以导航到页面的：XXXAuthenticationProcessingFilter和ExceptionTranslationFilter，因此在配置时，需要提供相关页面url给这两种filter。

**常用的几个Filter**

1. SecurityContextPersistenceFilter

该filter默认启用，作用是保存Authentication对象使后续的filter可以获得这个对象。对于同一个用户(session id相同)，会从Session中取出用户的校验结果。若是第一次访问，则会新建SecurityContext放入SecurityContextHolder(该Holder实际上是用一个ThreadLocal来保存SecurityContext)待后续代码保存校验结果。

2. CasAuthenticationFilter

用于处理CAS service的token，对于用到单点登录的系统，这个filter会经常使用。

3. AnonymousAuthenticationFilter

默认启用。由上文可知，AbstractAuthenticationProcessingFilter中可以构造Authentication。那么不需要权限控制的url怎么去构造Authentication呢？AnonymousAuthenticationFilter就发挥了作用。该filter构造了一个pesudo Authentication提供给FilterSecurityInterceptor，从而使所有的url的处理流程统一。