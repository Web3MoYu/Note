# Shiro

## 第一章 入门概述

### 1.1 Shiro是什么

​	Apache Shiro是一个功能强大且易于使用的Java安全(权限)框架。Shiro可以完成：认证、授权、加密、会话管理、与Web集成、缓存等。借助Shiro您可以快速轻松地保护任何应用程序——从最小的移动应用程序到最大的Web和企业应用程序。

![](img/1.png)

官网：https://shiro.apache.org/

### 1.2 为什么要用Shiro

​	自2003年以来，框架格局发生了相当大的变化，因此今天仍然有很多系统在使用Shiro。这与Shiro的特性密不可分。

​	易于使用：使用Shiro构建系统安全框架非常简单。就算第一次接触也可以快速掌握。

​	全面：Shiro包含系统安全框架需要的功能，满足安全需求的“一站式服务”。

​	灵活：Shiro可以在任何应用程序环境中工作。虽然它可以在Web、EJB和IoC环境中工作，但不需要依赖它们。Shiro也没有强制要求任何规范，甚至没有很多依赖项。

​	强力支持Web：Shiro具有出色的Web应用程序支持，可以基于应用程序URL和Web协议（例如REST）创建灵活的安全策略，同时还提供一组JSP库来控制页面输出。

​	兼容性强：Shiro的设计模式使其易于与其他框架和应用程序集成。Shiro与Spring、Grails、Wicket、Tapestry、Mule、Apache Camel、Vaadin等框架无缝集成。

​	社区支持：Shiro是Apache软件基金会的一个开源项目，有完备的社区支持，文档支持。如果需要，像Katasoft这样的商业公司也会提供专业的支持和服务。

### 1.3 Shiro与SpringSecurity的对比

​	1、Spring Security基于Spring开发，项目若使用Spring作为基础，配合Spring Security做权限更加方便，而Shiro需要和Spring进行整合开发； 

​	2、Spring Security功能比Shiro更加丰富些，例如安全维护方面； 

​	3、Spring Security社区资源相对比Shiro更加丰富； 

​	4、Shiro的配置和使用比较简单，Spring Security上手复杂些；

​	5、Shiro依赖性低，不需要任何框架和容器，可以独立运行.Spring Security依赖Spring容器；

​	6、shiro不仅仅可以使用在web中，它可以工作在任何应用环境中。在集群会话时Shiro最重要的一个好处或许就是它的会话是独立于容器的。

### 1.4 基本功能 

**1、基本功能点如下图所示** 

![](img/2.png)

**2、功能简介**

（1）Authentication：身份认证/登录，验证用户是不是拥有相应的身份；

（2）Authorization：授权，即权限验证，验证某个已认证的用户是否拥有某个权限；即判断用户是否能进行什么操作，如：验证某个用户是否拥有某个角色。或者细粒度的验证某个用户对某个资源是否具有某个权限；

（3）Session Manager：会话管理，即用户登录后就是一次会话，在没有退出之前，它的所有信息都在会话中；会话可以是普通JavaSE环境，也可以是Web环境的；

（4）Cryptography：加密，保护数据的安全性，如密码加密存储到数据库，而不是明文存储；

（5）Web Support：Web支持，可以非常容易的集成到Web环境；

（6）Caching：缓存，比如用户登录后，其用户信息、拥有的角色/权限不必每次去查，这样可以提高效率；

（7）Concurrency：Shiro支持多线程应用的并发验证，即如在一个线程中开启另一个线程，能把权限自动传播过去；

（8）Testing：提供测试支持；

（9）Run As：允许一个用户假装为另一个用户（如果他们允许）的身份进行访问；

（10）Remember Me：记住我，这个是非常常见的功能，即一次登录后，下次再来的话不用登录了

### 1.5 原理

**1、Shiro 架构(Shiro外部来看)**

从外部来看Shiro，即从应用程序角度的来观察如何使用Shiro完成工作

![](img/3.png)

Shiro 架构

（1）Subject：应用代码直接交互的对象是Subject，也就是说Shiro的对外API核心就是Subject。Subject代表了当前“用户”，这个用户不一定是一个具体的人，与当前应用交互的任何东西都是Subject，如网络爬虫，机器人等；与Subject的所有交互都会委托给SecurityManager；Subject其实是一个门面，SecurityManager才是实际的执行者；

（2）SecurityManager：安全管理器；即所有与安全有关的操作都会与SecurityManager交互；且其管理着所有Subject；可以看出它是Shiro的核心，它负责与Shiro的其他组件进行交互，它相当于SpringMVC中DispatcherServlet的角色

（3）Realm：Shiro从Realm获取安全数据（如用户、角色、权限），就是说SecurityManager要验证用户身份，那么它需要从Realm获取相应的用户进行比较以确定用户身份是否合法；也需要从Realm得到用户相应的角色/权限进行验证用户是否能进行操作；可以把Realm看成DataSource

**2、Shiro 架构(Shiro内部来看)**

![](img/4.png)

Shiro 架构

（1）Subject：任何可以与应用交互的“用户”

（2）SecurityManager：相当于SpringMVC中的DispatcherServlet；是Shiro的心脏；所有具体的交互都通过SecurityManager进行控制；它管理着所有Subject、且负责进行认证、授权、会话及缓存的管理。

（3）Authenticator：负责Subject认证，是一个扩展点，可以自定义实现；可以使用认证策略（Authentication Strategy），即什么情况下算用户认证通过了； 

（4）Authorizer：授权器、即访问控制器，用来决定主体是否有权限进行相应的操作；即控制着用户能访问应用中的哪些功能；

（5）Realm：可以有1个或多个Realm，可以认为是安全实体数据源，即用于获取安全实体的；可以是JDBC实现，也可以是内存实现等等；由用户提供；所以一般在应用中都需要实现自己的Realm；

（6）SessionManager：管理Session生命周期的组件；而Shiro并不仅仅可以用在Web环境，也可以用在如普通的JavaSE环境

（7）CacheManager：缓存控制器，来管理如用户、角色、权限等的缓存的；因为这些数据基本上很少改变，放到缓存中后可以提高访问的性能

（8）Cryptography：密码模块，Shiro提高了一些常见的加密组件用于如密码加密/解密。

## 第二章 基本使用

### 2.1 环境准备

1、Shiro不依赖容器，直接创建maven工程即可

2、添加依赖

```xml
<dependencies>
 	<dependency>
 		<groupId>org.apache.shiro</groupId>
 		<artifactId>shiro-core</artifactId>
 		<version>1.9.0</version>
 	</dependency>
 	<dependency>
 		<groupId>commons-logging</groupId>
 		<artifactId>commons-logging</artifactId>
 		<version>1.2</version>
 	</dependency>
</dependencies>

```

### 2.2 INI 文件

Shiro 获取权限相关信息可以通过数据库获取，也可以通过ini配置文件获取

**1、创建ini文件**

![](img/5.png)

```
[users]
zhangsan=z3
lisi=l4
```

### 2.3 登录认证 

**1、登录认证概念**

​	1> 身份验证：一般需要提供如身份ID等一些标识信息来表明登录者的身份，如提供 email，用户名/密码来证明。 

​	2> 在shiro中，用户需要提供principals（身份）和credentials（证明）给shiro，从 而应用能验证用户身份： 

​	3> principals：身份，即主体的标识属性，可以是任何属性，如用户名、邮箱等，唯一 即可。一个主体可以有多个principals，但只有一个Primary 	principals，一般是用户名/ 邮箱/手机号。 

​	4> credentials：证明/凭证，即只有主体知道的安全值，如密码/数字证书等。 （5）最常见的principals和credentials组合就是用户名/密码

**2、登录认证基本流程** 

（1）收集用户身份/凭证，即如用户名/密码

（2）调用Subject.login进行登录，如果失败将得到相应的AuthenticationException异常，根据异常提示用户错误信息；否则登录成功

（3）创建自定义的Realm类，继承org.apache.shiro.realm.AuthenticatingRealm类，实现doGetAuthenticationInfo()方法

![](img/6.png)

**3、登录认证实例** 

创建测试类，获取认证对象，进行登录认证，如下：

```java
public class ShiroRun {
	public static void main(String[] args) {
 		//1 初始化获取 SecurityManager
		IniSecurityManagerFactory factory = new IniSecurityManagerFactory("classpath:shiro.ini");
		SecurityManager securityManager = factory.getInstance();
 		SecurityUtils.setSecurityManager(securityManager);
 		//2 获取 Subject 对象
 		Subject subject = SecurityUtils.getSubject();
 		//3 创建 token 对象，web 应用用户名密码从页面传递
 		AuthenticationToken token = new UsernamePasswordToken("zhangsan","z3");
 		//4 完成登录
 		try {
 			subject.login(token);
 			System.out.println("登录成功");
 		}catch (UnknownAccountException e) {
 			e.printStackTrace();
 			System.out.println("用户不存在");
 		}catch (IncorrectCredentialsException e) {
 			e.printStackTrace();
 			System.out.println("密码错误");
 		}catch (AuthenticationException ae) {
 			//unexpected condition? error?
		}
	}
}
```

**4、身份认证流程** 

（1）首先调用Subject.login(token)进行登录，其会自动委托给SecurityManager

（2）SecurityManager负责真正的身份验证逻辑；它会委托给Authenticator进行身份验证；

（3）Authenticator才是真正的身份验证者，Shiro API中核心的身份认证入口点，此处可以自定义插入自己的实现；

（4）Authenticator可能会委托给相应的AuthenticationStrategy进行多Realm身份验证，默认ModularRealmAuthenticator会调用AuthenticationStrategy进行多Realm身份验证；

（5）Authenticator会把相应的token传入Realm，从Realm获取身份验证信息，如果没有返回/抛出异常表示身份验证失败了。此处可以配置多个Realm，将按照相应的顺序及策略进行访问。

### 2.4 角色、授权

**1、授权概念**

（1）**授权**，也叫访问控制，即在应用中控制谁访问哪些资源（如访问页面/编辑数据/页面操作等）。在授权中需了解的几个关键对象：主体（Subject）、资源（Resource）、权限（Permission）、角色（Role）。

（2）**主体(Subject)**：访问应用的用户，在Shiro中使用Subject代表该用户。用户只有授权后才允许访问相应的资源。

（3）**资源(Resource)**：在应用中用户可以访问的URL，比如访问JSP页面、查看/编辑某些数据、访问某个业务方法、打印文本等等都是资源。用户只要授权后才能访问。

（4）**权限(Permission)**：安全策略中的原子授权单位，通过权限我们可以表示在应用中用户有没有操作某个资源的权力。即权限表示在应用中用户能不能访问某个资源，如：访问用户列表页面查看/新增/修改/删除用户数据（即很多时候都是CRUD（增查改删）式权限控制）等。权限代表了用户有没有操作某个资源的权利，即反映在某个资源上的操作允不允许。

（5）Shiro支持粗粒度权限（如用户模块的所有权限）和细粒度权限（操作某个用户的权限，即实例级别的）

（6）**角色(Role)**：权限的集合，一般情况下会赋予用户角色而不是权限，即这样用户可以拥有一组权限，赋予权限时比较方便。典型的如：项目经理、技术总监、CTO、开发工程师等都是角色，不同的角色拥有一组不同的权限

**2、授权方式**

（1）编程式：通过写if/else授权代码块完成

```java
if (subject.hasRole("admin")){
    //有权限
}else{
    //无权限
}
```

（2）注解式：通过在执行的Java方法上放置相应的注解完成，没有权限将抛出相应的异常

```java
@RequireRoles("admin")
public void hello(){
    //有权限
}
```

（3）JSP/GSP标签：在JSP/GSP 页面通过相应的标签完成

```xml
<shiro:hasRole name="adin">
<!-- 有权限-->
</shiro:hasRole>
```

**3、授权流程**

（1）首先调用Subject.isPermitted*/hasRole*接口，其会委托给SecurityManager，而SecurityManager接着会委托给Authorizer；

（2）Authorizer是真正的授权者，如果调用如isPermitted(“user:view”)，其首先会通过PermissionResolver把字符串转换成相应的Permission实例；

（3）在进行授权之前，其会调用相应的Realm获取Subject相应的角色/权限用于匹配传入的角色/权限；

（4）Authorizer会判断Realm的角色/权限是否和传入的匹配，如果有多个Realm，会委托给ModularRealmAuthorizer进行循环判断，如果匹配如isPermitted*/hasRole*会返回true，否则返回false表示授权失败

![](img/7.png)

**4、授权实例**

（1）获取角色信息

- 1、给shiro.ini增加角色配置

```
[users]
zhangsan=z3,role1,role2
lisi=l4
```

- 2、给例子添加代码，沟通过hasRole()判断用户是否有指定角色

```java
try{
    subject.login(token);
    System.out.println("登录成功");
    // 判断角色
    boolean result = subject.hasRole("role1");
    System.out.printlen("是否拥有此角色:" + result);
}
```

（2）判断权限信息信息

- 1、给shiro.ini增加权限配置

```
[roles]
role1=user:insert,user:select
```

- 2、给例子添加代码，判断用户是否有指定权限

```java
//判断权限
boolean isPermitted = subject.isPermitted("user:insert");
System.out.println("是否拥有此权限：" + isPermitted);
//也可以用 checkPermission 方法，但没有返回值，没权限抛 AuthenticationException
subject.checkPermission("user:select");
```

### 2.5 Shiro加密 

实际系统开发中，一些敏感信息需要进行加密，比如说用户的密码。Shiro内嵌很多 常用的加密算法，比如MD5加密。Shiro可以很简单的使用信息加密。

1、使用Shiro进行密码加密

```java
public class ShiroMD5 {
	public static void main(String[] args) {
 		//密码明文
		String password = "z3";
 		//使用 md5 加密
 		Md5Hash md5Hash = new Md5Hash(password);
 		System.out.println("md5 加密："+md5Hash.toHex());
 		//带盐的 md5 加密，盐就是在密码明文后拼接新字符串，然后再进行加密
 		Md5Hash md5Hash2 = new Md5Hash(password,"salt");
 		System.out.println("md5 带盐加密："+md5Hash2.toHex());
 		//为了保证安全，避免被破解还可以多次迭代加密，保证数据安全
 		Md5Hash md5Hash3 = new Md5Hash(password,"salt",3);
 		System.out.println("md5 带盐三次加密："+md5Hash3.toHex());
 		//使用父类实现加密
 		SimpleHash simpleHash = new SimpleHash("MD5",password,"salt",3);
		 System.out.println("父类带盐三次加密："+simpleHash.toHex());
 	}
}
```

### 2.6 Shiro 自定义登录认证

Shiro默认的登录认证是不带加密的，如果想要实现加密认证需要自定义登录认证，自定义Realm。 

1、自定义登录认证

```java
public class MyRealm extends AuthenticatingRealm {
 	//自定义的登录认证方法，Shiro 的 login 方法底层会调用该类的认证方法完成登录认证
 	//需要配置自定义的 realm 生效，在 ini 文件中配置，或 Springboot 中配置
 	//该方法只是获取进行对比的信息，认证逻辑还是按照 Shiro 的底层认证逻辑完成认证
 	protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
 		//1 获取身份信息
 		String principal = authenticationToken.getPrincipal().toString();
 		//2 获取凭证信息
 		String password = new String((char[]) authenticationToken.getCredentials());
 		System.out.println("认证用户信息："+principal+"---"+password);
 		//3 获取数据库中存储的用户信息
 		if(principal.equals("zhangsan")){
 			//3.1 数据库存储的加盐迭代 3 次密码
 			String pwdInfo = "7174f64b13022acd3c56e2781e098a5f";
 			//3.2 创建封装了校验逻辑的对象，将要比较的数据给该对象
			AuthenticationInfo info = new SimpleAuthenticationInfo(
 				authenticationToken.getPrincipal(),
 				pwdInfo,
				ByteSource.Util.bytes("salt"),
 				authenticationToken.getPrincipal().toString());
 			return info;
	 	}
 		return null;
    }
}
```

**2、在shiro.ini中添加配置信息**

```
[main]
md5CredentialsMatcher=org.apache.shiro.authc.credential.Md5CredentialsMatcher
md5CredentialsMatcher.hashIterations=3
myrealm=com.atguigu.shirotest.MyRealm
myrealm.credentialsMatcher=$md5CredentialsMatcher
securityManager.realms=$myrealm

[users]
zhangsan=7174f64b13022acd3c56e2781e098a5f,role1,role2
lisi=l4
[roles]
role1=user:insert,user:select
```

#### 【1】认证相关

| 过滤器 | 过滤器类                 | 说明                                                         | 默认 |
| ------ | ------------------------ | ------------------------------------------------------------ | ---- |
| authc  | FormAuthenticationFilter | 基于表单的过滤器；如“/**=authc”，如果没有登录会跳到相应的登录页面登录 | 无   |
| logout | LogoutFilter             | 退出过滤器，主要属性：redirectUrl：退出成功后重定向的地址，如“/logout=logout” | /    |
| anon   | AnonymousFilter          | 匿名过滤器，即不需要登录即可访问；一般用于静态资源过滤；示例“/static/**=anon” | 无   |
| user   | UserFilter               | 用户过滤器，用户已经登录或者记住我都可以；示例"/**=user"     |      |

#### 【2】授权相关

| 过滤器 | 过滤器类                       | 说明                                                         | 默认 |
| ------ | ------------------------------ | ------------------------------------------------------------ | ---- |
| roles  | RolesAuthorizationFilter       | 角色授权拦截器，验证用户是否拥有所有角色；主要属性： loginUrl：登录页面地址（/login.jsp）；unauthorizedUrl：未授权后重定向的地址；示例“/admin/**=roles[admin]” | 无   |
| perms  | PermissionsAuthorizationFilter | 权限授权拦截器，验证用户是否拥有所有权限；属性和roles一样；示例“/user/**=perms["user:create"]” | 无   |
| port   | PortFilter                     | 端口拦截器，主要属性：port（80）：可以通过的端口；示例“/test= port[80]”，如果用户访问该页面是非80，将自动将请求端口改为80并重定向到该80端口，其他路径/参数等都一样 | 无   |
| rest   | HttpMethodPermissionFilter     | rest风格拦截器，自动根据请求方法构建权限字符串（GET=read, POST=create,PUT=update,DELETE=delete,HEAD=read,TRACE=read,OPTIONS=read, MKCOL=create）构建权限字符串；示例“/users=rest[user]”，会自动拼出“user:read,user:create,user:update,user:delete”权限字符串进行权限匹配（所有都得匹配，isPermittedAll） | 无   |
| ssl    | SslFilter                      | SSL拦截器，只有请求协议是https才能通过；否则自动跳转会https端口（443）；其他和port拦截器一样； | 无   |

## 第三章 与Spring Boot整合 

### 3.1 框架整合

**1、创建模块**

![](img/8.png)

**2、添加依赖**

```xml
<dependencies>
 	<dependency>
 		<groupId>org.apache.shiro</groupId>
 		<artifactId>shiro-spring-boot-web-starter</artifactId>
 		<version>1.9.0</version>
 	</dependency>
 
 	<!--mybatis-plus-->
 	<dependency>
 		<groupId>com.baomidou</groupId>
 		<artifactId>mybatis-plus-boot-starter</artifactId>
 		<version>3.0.5</version>
 	</dependency>
 	<!--mysql-->
 	<dependency>
		<groupId>mysql</groupId>
 		<artifactId>mysql-connector-java</artifactId>
 		<version>5.1.46</version>
 	</dependency>
 	<dependency>
 		<groupId>org.projectlombok</groupId>
 		<artifactId>lombok</artifactId>
 	</dependency>
 	<dependency>
 		<groupId>org.springframework.boot</groupId>
 		<artifactId>spring-boot-starter-thymeleaf</artifactId>
 	</dependency>
</dependencies>
```

**3、添加配置文件**

添加配置文件 application.yml，添加基础配置

```yaml
mybatis-plus:
 configuration:
 log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
 mapper-locations: classpath:mapper/*.xml
spring:
 datasource:
 type: com.zaxxer.hikari.HikariDataSource
 driver-class-name: com.mysql.jdbc.Driver
 url: jdbc:mysql://localhost:3306/shirodb?characterEncoding=utf-8&useSSL=false
 username: root
 password: 123123
 jackson:
 date-format: yyyy-MM-dd HH:mm:ss
 time-zone: GMT+8
shiro:
 loginUrl: /myController/login
```

**4、添加启动类**

![](img/9.png)

```java
@SpringBootApplication
@MapperScan("com.atguigu.shiro.mapper")
public class ShiroApplication {
 	public static void main(String[] args) {
 		SpringApplication.run(ShiroApplication.class, args);
 	}
}
```

### 3.2 登录认证实现

访问数据库获取用户信息，实现登录认证

**1、后端接口服务实现**

（1）创建库表

```mysql
CREATE DATABASE IF NOT EXISTS `shirodb` CHARACTER SET utf8mb4;
USE `shirodb`;
CREATE TABLE `user` (
 `id` BIGINT(20) NOT NULL AUTO_INCREMENT COMMENT '编号',
 `name` VARCHAR(30) DEFAULT NULL COMMENT '用户名',
 `pwd` VARCHAR(50) DEFAULT NULL COMMENT '密码',
 `rid` BIGINT(20) DEFAULT NULL COMMENT '角色编号',
 PRIMARY KEY (`id`)
) ENGINE=INNODB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8 COMMENT='用户表';
```

（2）创建实体

![](img/10.png)

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class User {
 	private Integer id;
 	private String name;
 	private String pwd;
 	private Integer rid;
}
```

（3）创建mapper

![](img/11.png)

```java
@Mapper
public interface UserMapper extends BaseMapper<User>{
    
}
```

（4）创建service

​	1、创建接口

![](img/12.png)

```java
public interface UserService {
 	//用户登录
 	User getUserInfoByName(String name);
}
```

​	2 创建实现类

![](img/13.png)

```java
@Service
public class UserServiceImpl implements UserService {
 	@Autowired
 	private UserMapper userMapper;
 	@Override
 	public User getUserInfoByName(String name) {
 		QueryWrapper<User> wrapper = new QueryWrapper<>();
 		wrapper.eq("name",name);
 		User user = userMapper.selectOne(wrapper);
 		return user;
 	}
}
```

（5）自定义realm

![](img/14.png)

```java
@Component
public class MyRealm extends AuthorizingRealm {
 	@Autowired
 	private UserService userService;
 	//自定义授权方法
 	@Override
 	protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
 		return null;
 	}
 	//自定义登录认证方法
 	@Override
 	protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
 		//1 获取用户身份信息
 		String name = token.getPrincipal().toString();
 		//2 调用业务层获取用户信息（数据库中）
 		User user = userService.getUserInfoByName(name);
 		//3 判断并将数据完成封装
 		if(user!=null){
 			AuthenticationInfo info = new  SimpleAuthenticationInfo(
            	token.getPrincipal(),
            	user.getPwd(),
            	ByteSource.Util.bytes("salt"),
            	token.getPrincipal().toString());
 			return info;
 		}
 		return null;
 	}
}
```

（6）编写配置类

![](img/15.png)

```java
@Configuration
public class ShiroConfig {
 	@Autowired
 	private MyRealm myRealm;
 	//配置 SecurityManager
 	@Bean
 	public DefaultWebSecurityManager defaultWebSecurityManager(){
 		//1 创建 defaultWebSecurityManager 对象
 		DefaultWebSecurityManager defaultWebSecurityManager = new DefaultWebSecurityManager();
 		//2 创建加密对象，并设置相关属性
 		HashedCredentialsMatcher matcher = new HashedCredentialsMatcher();
 		//2.1 采用 md5 加密
 		matcher.setHashAlgorithmName("md5");
 		//2.2 迭代加密次数
 		matcher.setHashIterations(3);
 		//3 将加密对象存储到 myRealm 中
 		myRealm.setCredentialsMatcher(matcher);
 		//4 将 myRealm 存入 defaultWebSecurityManager 对象
 		defaultWebSecurityManager.setRealm(myRealm);
 		//5 返回
 		return defaultWebSecurityManager;
 	}
 	//配置 Shiro 内置过滤器拦截范围
 	@Bean
 	public DefaultShiroFilterChainDefinition shiroFilterChainDefinition(){
 		DefaultShiroFilterChainDefinition definition = new DefaultShiroFilterChainDefinition();
 		//设置不认证可以访问的资源
 		definition.addPathDefinition("/myController/userLogin","anon");
 		definition.addPathDefinition("/login","anon");
 		//设置需要进行登录认证的拦截范围
 		definition.addPathDefinition("/**","authc");
 		return definition;
 	}
}
```

（7）实现controller

![](img/16.png)

```java
@Controller
@RequestMapping("myController")
public class MyController {
 	@GetMapping("userLogin")
 	@ResponseBody
 	public String userLogin(String name,String pwd){
 		//1 获取 Subject 对象
 		Subject subject = SecurityUtils.getSubject();
 		//2 封装请求数据到 token 对象中
 		AuthenticationToken token = new UsernamePasswordToken(name,pwd);
 		//3 调用 login 方法进行登录认证
 		try {
 			subject.login(token);
 			return "登录成功";
 		} catch (AuthenticationException e) {
 			e.printStackTrace();
 			System.out.println("登录失败");
 		return "登录失败";
 		}
 	}
}
```

（8）测试

 确认数据库密码是加盐3次加密密码

![](img/17.png)

启动服务

通过浏览器访问

http://localhost:8080/myController/userLogin?name=张三&pwd=z3

![](img/18.png)

**2、实现前端页面**

Shiro整合Thymeleaf

（1）确认依赖

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-start-thymeleaf</artifactId>
</dependency>
```

（2）添加login页面

![](img/19.png)

```html
<!DOCTYPE html>
<html lang="en">
	<head>
 		<meta charset="UTF-8">
 		<title>Title</title>
	</head>
	<body>
 		<h1>Shiro 登录认证</h1>
 		<br>
 		<form action="/myController/userLogin">
 		<div>用户名：<input type="text" name="name" value=""></div>
 		<div>密码：<input type="password" name="pwd" value=""></div>
 		<div><input type="submit" value="登录"></div>
 		</form>
	</body>
</html>
```

（3）添加main页面

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
	<head>
 	<meta charset="UTF-8">
 	<title>Title</title>
	</head>
	<body>
 		<h1>Shiro 登录认证后主页面</h1>
 		<br>
 		登录用户为：<span th:text="${session.user}"></span>
    </body>
</html>
```

（4）添加controller 方法，改造认证方法

```java
//跳转登录页面
@GetMapping("login")
public String login(){ 
 	return "login";
}
//登录认证
@GetMapping("userLogin")
public String userLogin(String name, String pwd, HttpSession session){
 	//1 获取 Subject 对象
 	Subject subject = SecurityUtils.getSubject();
 	//2 封装请求数据到 token 对象中
 	AuthenticationToken token = new UsernamePasswordToken(name,pwd);
 	//3 调用 login 方法进行登录认证
 	try {
 		subject.login(token);
 		session.setAttribute("user",token.getPrincipal().toString());
 		return "main";
 	} catch (AuthenticationException e) {
 		e.printStackTrace();
 		System.out.println("登录失败");
 		return "登录失败";
 	}
}
```

（5）修改配置文件

![](img/20.png)

（6）修改配置类

![](img/21.png)

（7）启动，访问测试

![](img/22.png)

### 3.3 多个realm的认证策略设置

**1、多个realm实现原理**

​	当应用程序配置多个Realm时，例如：用户名密码校验、手机号验证码校验等等。Shiro的ModularRealmAuthenticator会使用内部的AuthenticationStrategy组件判断认证是成功还是失败。

​	AuthenticationStrategy是一个无状态的组件，它在身份验证尝试中被询问4次（这4次交互所需的任何必要的状态将被作为方法参数）：

​		（1）在所有Realm被调用之前

​		（2）在调用Realm的getAuthenticationInfo方法之前

​		（3）在调用Realm的getAuthenticationInfo方法之后

​		（4）在所有Realm被调用之后

​		认证策略的另外一项工作就是聚合所有Realm的结果信息封装至一个AuthenticationInfo实例中，并将此信息返回，以此作为Subject的身份信息。Shiro中定义了3种认证策略的实现：

| AuthenticationStrategy class | 描述                                                         |
| ---------------------------- | ------------------------------------------------------------ |
| AtLeastOneSuccessfulStrategy | 只要有一个（或更多）的Realm验证成功，那么认证将视为成功      |
| FirstSuccessfulStrategy      | 第一个Realm验证成功，整体认证将视为成功，且后续Realm将被忽略 |
| AllSuccessfulStrategy        | 所有Realm成功，认证才视为成功                                |

ModularRealmAuthenticator内置的认证策略默认实现是AtLeastOneSuccessfulStrategy方式。可以通过配置修改策略

**2、多个realm代码实现**

```java
//配置 SecurityManager
@Bean
public DefaultWebSecurityManager defaultWebSecurityManager(){
 	//1 创建 defaultWebSecurityManager 对象
 	DefaultWebSecurityManager defaultWebSecurityManager = new DefaultWebSecurityManager();
 	//2 创建认证对象，并设置认证策略
 	ModularRealmAuthenticator modularRealmAuthenticator = new ModularRealmAuthenticator();
 	modularRealmAuthenticator.setAuthenticationStrategy(new AllSuccessfulStrategy());
 
	defaultWebSecurityManager.setAuthenticator(modularRealmAuthenticator);
 	//3 封装 myRealm 集合
 	List<Realm> list = new ArrayList<>();
 	list.add(myRealm);
 	list.add(myRealm2);
 
 	//4 将 myRealm 存入 defaultWebSecurityManager 对象
 	defaultWebSecurityManager.setRealms(list);
 	//5 返回
 	return defaultWebSecurityManager;
}
```

### 3.4 remember me功能 

​	Shiro提供了记住我（RememberMe）的功能，比如访问一些网站时，关闭了浏览器，下次再打开时还是能记住你是谁，下次访问时无需再登录即可访问。

​	**1、基本流程**

​	（1）首先在登录页面选中RememberMe然后登录成功；如果是浏览器登录，一般会把RememberMe的Cookie写到客户端并保存下来；

​	（2）关闭浏览器再重新打开；会发现浏览器还是记住你的；

​	（3）访问一般的网页服务器端，仍然知道你是谁，且能正常访问；

​	（4）但是，如果我们访问电商平台时，如果要查看我的订单或进行支付时，此时还是需要再进行身份认证的，以确保当前用户还是你。

​	**2、代码实现** 

​	（1）修改配置类

```java
@Configuration
public class ShiroConfig {
 	@Autowired
 	private MyRealm myRealm;
 	//配置 SecurityManager
 	@Bean
 	public DefaultWebSecurityManager defaultWebSecurityManager(){
 		//1 创建 defaultWebSecurityManager 对象
 		DefaultWebSecurityManager defaultWebSecurityManager = new DefaultWebSecurityManager();
 		//2 创建加密对象，并设置相关属性
 		HashedCredentialsMatcher matcher = new HashedCredentialsMatcher();
 		//2.1 采用 md5 加密
 		matcher.setHashAlgorithmName("md5");
 		//2.2 迭代加密次数
 		matcher.setHashIterations(3);
 		//3 将加密对象存储到 myRealm 中
 		myRealm.setCredentialsMatcher(matcher);
 		//4 将 myRealm 存入 defaultWebSecurityManager 对象
 		defaultWebSecurityManager.setRealm(myRealm);
 		//4.5 设置 rememberMe
 		defaultWebSecurityManager.setRememberMeManager(rememberMeManager());
 		//5 返回
 		return defaultWebSecurityManager;
 	}
 	//cookie 属性设置
 	public SimpleCookie rememberMeCookie(){
        SimpleCookie cookie = new SimpleCookie("rememberMe");
 		//设置跨域
 		//cookie.setDomain(domain);
 		cookie.setPath("/");
 		cookie.setHttpOnly(true);
 		cookie.setMaxAge(30*24*60*60);
 		return cookie;
 	}
 	//创建 Shiro 的 cookie 管理对象
 	public CookieRememberMeManager rememberMeManager(){
 		CookieRememberMeManager cookieRememberMeManager = new CookieRememberMeManager();
 		cookieRememberMeManager.setCookie(rememberMeCookie());
 
		// 注意：cookieRememberMeManager.setCipherKey传入参数为长度16位的byte[]，
        // 否则会报Unable to init cipher instance:无法初始化密码实例的错误
        // cookieRememberMeManager.setCipherKey用来设置加密的Key,参数类型byte[],字节数组长度要求16
        cookieRememberMeManager.setCipherKey("1234567890987654".getBytes());
 		return cookieRememberMeManager;
 	}
 	//配置 Shiro 内置过滤器拦截范围
 	@Bean
 	public DefaultShiroFilterChainDefinition shiroFilterChainDefinition(){
 		DefaultShiroFilterChainDefinition definition = new DefaultShiroFilterChainDefinition();
 		//设置不认证可以访问的资源
 
		definition.addPathDefinition("/myController/userLogin","anon");
 		definition.addPathDefinition("/myController/login","anon");
 		//设置需要进行登录认证的拦截范围
 		definition.addPathDefinition("/**","authc");
 		//添加存在用户的过滤器（rememberMe）
 		definition.addPathDefinition("/**","user");
 		return definition;
 	}
}

```

​	（2）修改 controller

```java
//登录认证
@GetMapping("userLogin")
public String userLogin(String name, String pwd,@RequestParam(defaultValue = "false")boolean rememberMe, HttpSession session){
 	//1 获取 Subject 对象
 	Subject subject = SecurityUtils.getSubject();
 	//2 封装请求数据到 token 对象中
 	AuthenticationToken token = new UsernamePasswordToken(name,pwd,rememberMe);
 	//3 调用 login 方法进行登录认证
 	try {
 		subject.login(token);
 		session.setAttribute("user",token.getPrincipal().toString());
 		return "main";
 	} catch (AuthenticationException e) {
 		e.printStackTrace();
 		System.out.println("登录失败");
 		return "登录失败";
 	}
}

//登录认证验证 rememberMe
@GetMapping("userLoginRm")
public String userLogin(HttpSession session) {
 	Subject subject = SecurityUtils.getSubject();
    session.setAttribute("user", subject.getPrincipal().toString());
 	return "main";
}
```

（3）改造页面login.html

```html
<body>
 	<h1>Shiro 登录认证</h1>
 	<br>
 	<form action="/myController/userLogin">
 	<div>用户名：<input type="text" name="name" value=""></div>
 	<div>密码：<input type="password" name="pwd" value=""></div>
 	<div>记住用户：<input type="checkbox" name="rememberMe" value="true"></div>
 	<div><input type="submit" value="登录"></div>
 	</form>
</body>
```

**3、测试**

​	（1）通过地址访问 userLoginRm

​		http://localhost:8080/myController/userLoginRm

​	过滤器拦截跳回登录页面

![](img/23.png)

​	（2）登录勾选记住用户

![](img/24.png)

​	（3）重新访问userLoginRm

​		http://localhost:8080/myController/userLoginRm

![](img/25.png)

### 3.5 用户登录认证后登出

用户登录后，配套的有登出操作。直接通过Shiro过滤器即可实现登出

**1、代码实现**

（1）修改登录后的 main.html

```html
<body>
 	<h1>Shiro 登录认证后主页面</h1>
 	<br>
 	登录用户为：<span th:text="${session.user}"></span>
 	<br>
 	<a href="/logout">登出</a>
</body>
```

（2）修改配置类，添加logout过滤器

```java
//配置 Shiro 内置过滤器拦截范围
@Bean
public DefaultShiroFilterChainDefinition shiroFilterChainDefinition(){
 	DefaultShiroFilterChainDefinition definition = new DefaultShiroFilterChainDefinition();
 	//设置不认证可以访问的资源
 	definition.addPathDefinition("/myController/userLogin","anon");
 	definition.addPathDefinition("/myController/login","anon");
 	//配置登出过滤器
 	definition.addPathDefinition("/logout","logout");
 	//设置需要进行登录认证的拦截范围
 	definition.addPathDefinition("/**","authc");
 	//添加存在用户的过滤器（rememberMe）
 	definition.addPathDefinition("/**","user");
 	return definition;
}
```

**2、测试**

（1）通过登录验证

![](img/26.png)

（2）点击“登出”登出系统

![](img/27.png)

### 3.6 授权、角色认证

**1、授权**

​	用户登录后，需要验证是否具有指定角色指定权限。Shiro也提供了方便的工具进行判断。

​	这个工具就是Realm的doGetAuthorizationInfo方法进行判断。触发权限判断的有两种方式 

​	（1）在页面中通过shiro:属性判断

​	（2）在接口服务中通过注解@Requires进行判断

**2、后端接口服务注解**

​	通过给接口服务方法添加注解可以实现权限校验，可以加在控制器方法上，也可以加在业务方法上，一般加在控制器方法上。常用注解如下：

```java
（1）@RequiresAuthentication 
	验证用户是否登录，等同于方法subject.isAuthenticated()
（2）@RequiresUser 
	验证用户是否被记忆：
	登录认证成功subject.isAuthenticated()为true
	登录后被记忆subject.isRemembered()为true
（3）@RequiresGuest 
	验证是否是一个guest的请求，是否是游客的请求
	此时subject.getPrincipal()为null
（4）@RequiresRoles 参数里有logical用于控制逻辑或和逻辑与
	验证subject是否有相应角色，有角色访问方法，没有则会抛出异常AuthorizationException。
	例如：@RequiresRoles(“aRoleName”)
	void someMethod();
	只有subject有aRoleName角色才能访问方法someMethod()
（5）@RequiresPermissions 
	验证subject是否有相应权限，有权限访问方法，没有则会抛出异常AuthorizationException。
	例如：@RequiresPermissions (“file:read”,”wite:aFile.txt”)
	void someMethod();
	subject必须同时含有file:read和wite:aFile.txt权限才能访问方法someMethod()
```

**3、授权验证-没有角色无法访问**

（1）添加controller方法，并添加验证角色注解

```java
//登录认证验证角色
@RequiresRoles("admin")
@GetMapping("userLoginRoles")
@ResponseBody
public String userLoginRoles() {
 	System.out.println("登录认证验证角色");
 	return "验证角色成功";
}
```

（2）修改main.html

```html
<body>
 	<h1>Shiro 登录认证后主页面</h1>
 	<br>
 	登录用户为：<span th:text="${session.user}"></span>
 	<br>
 	<a href="/logout">登出</a>
	<br>
 	<a href="/myController/userLoginRoles">测试授权</a>
</body>
```

（3）修改MyRealm方法

```java
//自定义授权方法：获取当前登录用户权限信息，返回给 Shiro 用来进行授权对比
@Override
protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
 	System.out.println("进入自定义授权方法");
 	return null;
}
```

（4）运行测试

![](img/28.png)

![](img/29.png)

**3、授权验证-获取角色进行验证**

（1）修改MyRealm方法

```java
//自定义授权方法：获取当前登录用户权限信息，返回给 Shiro 用来进行授权对比
@Override
protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
 	System.out.println("进入自定义授权方法");
 	//1 创建对象，存储当前登录的用户的权限和角色
 	SimpleAuthorizationInfo info = new SimpleAuthorizationInfo();
 	//2 存储角色
 	info.addRole("admin");
 	//返回
 	return info;
}
```

（2）运行测试

![](img/30.png)

（3）确认库表

```mysql
CREATE TABLE `role` (
 `id` BIGINT(20) NOT NULL AUTO_INCREMENT COMMENT '编号',
 `name` VARCHAR(30) DEFAULT NULL COMMENT '角色名',
 `desc` VARCHAR(50) DEFAULT NULL COMMENT '描述',
 `realname` VARCHAR(20) DEFAULT NULL COMMENT '角色显示名',
 PRIMARY KEY (`id`)
) ENGINE=INNODB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8 COMMENT='角色表';
```

![](img/31.png)

```mysql
CREATE TABLE `role_user` (
 `id` BIGINT(20) NOT NULL AUTO_INCREMENT COMMENT '编号',
 `uid` BIGINT(20) DEFAULT NULL COMMENT '用户 id',
 `rid` BIGINT(20) DEFAULT NULL COMMENT '角色 id',
 PRIMARY KEY (`id`)
) ENGINE=INNODB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8 COMMENT='角色用户映射
表';
```

![](img/32.png)

（4）查询sql

根据用户名查询对应角色信息

```sql
SELECT NAME FROM role WHERE id IN (SELECT rid FROM role_user WHERE uid=(SELECT id FROM USER WHERE NAME='张三'));
```

（5）mapper方法

```java
@Repository
public interface UserMapper extends BaseMapper<User> {
 	@Select("SELECT NAME FROM role WHERE id IN (SELECT rid FROM role_user WHERE uid=(SELECT id FROM USER WHERE NAME=#{principal}))")
 	List<String> getUserRoleInfoMapper(@Param("principal") String principal);
}
```

（6）service实现

```java
//获取用户的角色信息
@Override
public List<String> getUserRoleInfo(String principal) {
 	return userMapper.getUserRoleInfoMapper(principal);
}
```

（7）MyRealm方法改造

```java
//自定义授权方法：获取当前登录用户权限信息，返回给 Shiro 用来进行授权对比
@Override
protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
 	System.out.println("进入自定义授权方法");
 	//获取当前用户身份信息
 	String principal = principalCollection.getPrimaryPrincipal().toString();
 	//调用接口方法获取用户的角色信息
 	List<String> roles = userService.getUserRoleInfo(principal);
 	System.out.println("当前用户角色信息："+roles);
 	//创建对象，存储当前登录的用户的权限和角色
 	SimpleAuthorizationInfo info = new SimpleAuthorizationInfo();
 	//存储角色
 	info.addRoles(roles);
 	//返回
 	return info;
}

```

（8）启动登录测试

![](img/33.png)

**4、授权验证-获取权限进行验证**

获取权限验证和获取角色相类似

（1）确认库表

```mysql
CREATE TABLE `permissions` (
 `id` BIGINT(20) NOT NULL AUTO_INCREMENT COMMENT '编号',
 `name` VARCHAR(30) DEFAULT NULL COMMENT '权限名',
 `info` VARCHAR(30) DEFAULT NULL COMMENT '权限信息',
 `desc` VARCHAR(50) DEFAULT NULL COMMENT '描述',
 PRIMARY KEY (`id`)
) ENGINE=INNODB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8 COMMENT='权限表';
```

![](img/34.png)

```mysql
CREATE TABLE `role_ps` (
 `id` BIGINT(20) NOT NULL AUTO_INCREMENT COMMENT '编号',
 `rid` BIGINT(20) DEFAULT NULL COMMENT '角色 id',
 `pid` BIGINT(20) DEFAULT NULL COMMENT '权限 id',
 PRIMARY KEY (`id`)
) ENGINE=INNODB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8 COMMENT='角色权限映射
表';
```

![](img/35.png)

（2）查询sql

根据角色名查询对应权限信息

```sql
SELECT info FROM permissions WHERE id IN (SELECT pid FROM role_ps WHERE rid IN (SELECT id FROM role WHERE NAME IN('admin','userMag')));
```

（3）mapper方法

```java
@Select({
 "<script>",
 "select info FROM permissions WHERE id IN ",
 "(SELECT pid FROM role_ps WHERE rid IN (",
 "SELECT id FROM role WHERE NAME IN ",
 "<foreach collection='roles' item='name' open='(' 
separator=',' close=')'>",
 "#{name}",
 "</foreach>",
 "))",
 "</script>"
})
List<String> getUserPermissionInfoMapper(@Param("roles")List<String> roles);
```

（4）service实现

```java
//获取用户角色的权限信息
@Override
public List<String> getUserPermissionInfo(List<String> roles) {
 	return userMapper.getUserPermissionInfoMapper(roles);
}
```

（5）MyRealm方法改造

```java
//自定义授权方法：获取当前登录用户权限信息，返回给 Shiro 用来进行授权对比
@Override
protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
 	System.out.println("进入自定义授权方法");
 	//获取当前用户身份信息
 	String principal = 
	principalCollection.getPrimaryPrincipal().toString();
 	//调用接口方法获取用户的角色信息
 	List<String> roles = userService.getUserRoleInfo(principal);
 	System.out.println("当前用户角色信息："+roles);
 	//调用接口方法获取用户角色的权限信息
 	List<String> permissions = userService.getUserPermissionInfo(roles);
 	System.out.println("当前用户权限信息："+permissions);
 	//创建对象，存储当前登录的用户的权限和角色
 	SimpleAuthorizationInfo info = new SimpleAuthorizationInfo();
 	//存储角色
 	info.addRoles(roles);
 	//存储权限信息
 	info.addStringPermissions(permissions);
 	//返回
 	return info;
}
```

（6）添加controller方法

```java
//登录认证验证权限
@RequiresPermissions("user:delete")
@GetMapping("userPermissions")
@ResponseBody
public String userLoginPermissions() {
 	System.out.println("登录认证验证权限");
 	return "验证权限成功";
}
```

（7）改造main.html

```html
<body>
 	<h1>Shiro 登录认证后主页面</h1>
 	<br>
 	登录用户为：<span th:text="${session.user}"></span>
 	<br>
 	<a href="/logout">登出</a>
 	<br>
 	<a href="/myController/userLoginRoles">测试授权-角色验证</a>
 	<br>
 	<a href="/myController/userPermissions">测试授权-权限验证</a>
</body>
```

（8）启动登录测试

![](img/36.png)

**5、授权验证-异常处理**

（1）创建认证异常处理类，使用@ControllerAdvice加@ExceptionHandler实现特殊异常处理。

![](img/37.png)

```java
@ControllerAdvice
public class PermissionsException {
 	@ResponseBody
 	@ExceptionHandler(UnauthorizedException.class)
 	public String unauthorizedException(Exception ex){
 		return "无权限";
 	}
 	@ResponseBody
 	@ExceptionHandler(AuthorizationException.class)
 	public String authorizationException(Exception ex){
 		return "权限认证失败";
 	}
}
```

（2）启动运行，用李四登录测试。

![](img/38.png)

![](img/39.png)

**6、前端页面授权验证**

（1）添加依赖

```xml
<!--配置 Thymeleaf 与 Shrio 的整合依赖-->
<dependency>
 	<groupId>com.github.theborakompanioni</groupId>
 	<artifactId>thymeleaf-extras-shiro</artifactId>
 	<version>2.0.0</version>
</dependency>
```

（2）配置类添加新配置

- 用于解析thymeleaf中的shiro:相关属性

```java
@Bean
public ShiroDialect shiroDialect(){
 	return new ShiroDialect();
}
```

（3）Thymeleaf 中常用的 shiro:属性

- guest标签

```xml
<shiro:guest></shiro:guest>
```

用户没有身份验证时显示相应信息，即游客访问信息。

- user标签

```xml
<shiro:user></shiro:user>
```

用户已经身份验证/记住我登录后显示相应的信息。

- authenticated标签

```xml
<shiro:authenticated></shiro:authenticated>
```

用户已经身份验证通过，即Subject.login登录成功，不是记住我登录的。

- notAuthenticated标签

```xml
<shiro:notAuthenticated></shiro:notAuthenticated>
```

用户已经身份验证通过，即没有调用Subject.login进行登录，包括记住我自动登录的也属于未进行身份验证。

- principal标签

```xml
<shiro: principal/>
<shiro:principal property="username"/>
```

相当于((User)Subject.getPrincipals()).getUsername()。

- lacksPermission 标签

```xml
<shiro:lacksPermission name="org:create"></shiro:lacksPermission>
```

如果当前Subject没有权限将显示body体内容。

- hasRole标签

```xml
<shiro:hasRole name="admin"></shiro:hasRole>
```

如果当前Subject有任意一个角色（或的关系）将显示body体内容。

- hasAnyRoles标签

```xml
<shiro:hasAnyRoles name="admin,user"></shiro:hasAnyRoles>
```

如果当前Subject有任意一个角色（或的关系）将显示body体内容。

- lacksRole 标签

```xml
<shiro:lacksRole name="abc"></shiro:lacksRole>
```

如果当前Subject没有角色将显示body体内容。

- hasPermission 标签

```xml
<shiro:hasPermission name="user:create"></shiro:hasPermission>
```

如果当前Subject有权限将显示body体内容

（4）改造main.html

```html
<body>
 	<h1>Shiro 登录认证后主页面</h1>
 	<br>
 	登录用户为：<span th:text="${session.user}"></span>
 	<br>
 	<a href="/logout">登出</a>
 	<br>
 	<a shiro:hasRole="admin" href="/myController/userLoginRoles">测试授权-角色验证</a>
 	<br>
 	<a shiro:hasPermission="user:delete" href="/myController/userPermissions">测试授权-权限验证</a>
</body>
```

（5）测试

![](img/40.png)

### 3.7 实现缓存

**1、缓存工具EhCache**

​	EhCache是一种广泛使用的开源Java分布式缓存。主要面向通用缓存,JavaEE和轻量级容器。可以和大部分Java项目无缝整合，例如：Hibernate中的缓存就是基于EhCache实现 的。

​	EhCache支持内存和磁盘存储，默认存储在内存中，如内存不够时把缓存数据同步到磁盘中。EhCache支持基于Filter的Cache实现，也支持Gzip压缩算法。

​	EhCache直接在JVM虚拟机中缓存，速度快，效率高；

​	EhCache缺点是缓存共享麻烦，集群分布式应用使用不方便

**2、EhCache搭建使用**

（1）创建模块

![](img/41.png)

（2）添加依赖

```xml
<dependencies>
 	<dependency>
 		<groupId>net.sf.ehcache</groupId>
 		<artifactId>ehcache</artifactId>
 		<version>2.6.11</version>
 		<type>pom</type>
 	</dependency>
</dependencies>

```

（3）添加配置文件ehcache.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ehcache>
 	<!--磁盘的缓存位置-->
 	<diskStore path="java.io.tmpdir/ehcache"/>
 	<!--默认缓存-->
 	<defaultCache
 		maxEntriesLocalHeap="10000"
 		eternal="false"
 		timeToIdleSeconds="120"
 		timeToLiveSeconds="120"
 		maxEntriesLocalDisk="10000000"
 		diskExpiryThreadIntervalSeconds="120"
 		memoryStoreEvictionPolicy="LRU">
 	<persistence strategy="localTempSwap"/>
 </defaultCache>
 	<!--helloworld 缓存-->
 	<cache name="HelloWorldCache"
 		maxElementsInMemory="1000"
 		eternal="false"
 		timeToIdleSeconds="5"
 		timeToLiveSeconds="5"
 		overflowToDisk="false"
 		memoryStoreEvictionPolicy="LRU"/>
 <!--
 defaultCache：默认缓存策略，当 ehcache 找不到定义的缓存时，则使用这个
缓存策略。只能定义一个。
 -->
    
 <!--
 	name:缓存名称。
 	maxElementsInMemory:缓存最大数目
 	maxElementsOnDisk：硬盘最大缓存个数。
 	eternal:对象是否永久有效，一但设置了，timeout将不起作用。
 	overflowToDisk:是否保存到磁盘，当系统宕机时
 	timeToIdleSeconds:设置对象在失效前的允许闲置时间（单位：秒）。仅当eternal=false 对象不是永久有效时使用，可选属性，默认值是 0，也就是可闲置时间无穷大。
 	timeToLiveSeconds:设置对象在失效前允许存活时间（单位：秒）。最大时间介于创建时间和失效时间之间。仅当 eternal=false 对象不是永久有效时使用，默认是0,也就是对象存活时间无穷大。
 	diskPersistent：是否缓存虚拟机重启期数据 Whether the disk store persists between restarts of the Virtual Machine. Thedefault value is false.
 	diskSpoolBufferSizeMB：这个参数设置 DiskStore（磁盘缓存）的缓存区大小。默认是 30MB。每个 Cache 都应该有自己的一个缓冲区。diskExpiryThreadIntervalSeconds：磁盘失效线程运行时间间隔，默认是120 秒。
 	memoryStoreEvictionPolicy：当达到 maxElementsInMemory 限制时，Ehcache 将会根据指定的策略去清理内存。默认策略是 LRU（最近最少使用）。你可以设置为 FIFO（先进先出）或是 LFU（较少使用）。
 	clearOnFlush：内存数量最大时是否清除。
 	memoryStoreEvictionPolicy:可选策略有：LRU（最近最少使用，默认策略）、FIFO（先进先出）、LFU（最少访问次数）。
 	FIFO，first in first out，这个是大家最熟的，先进先出。
 	LFU， Less Frequently Used，就是上面例子中使用的策略，直白一点就是讲一直以来最少被使用的。如上面所讲，缓存的元素有一个 hit 属性，hit 值最小的将会被清出缓存。
 	LRU，Least Recently Used，最近最少使用的，缓存的元素有一个时间戳，当缓存容量满了，而又需要腾出地方来缓存新的元素的时候，那么现有缓存元素中时间戳离当前时间最远的元素将被清出缓存。
-->
</ehcache>
```

（4）创建测试类，操作缓存

![](img/43.png)

```java
public class TestEH {
 	public static void main(String[] args) {
 		//获取编译目录下的资源的流对象
 		InputStream input = TestEH.class.getClassLoader().getResourceAsStream("ehcache.xml");
 		//获取 EhCache 的缓存管理对象
 		CacheManager cacheManager = new CacheManager(input);
 		//获取缓存对象
 		Cache cache = cacheManager.getCache("HelloWorldCache");
 		//创建缓存数据
 		Element element = new Element("name","zhang3");
 		//存入缓存
 		cache.put(element);
 		//从缓存中取出
 		Element element1 = cache.get("name");
 		System.out.println(element1.getObjectValue());
 	}
}
```

**3、Shiro整合EhCache**

Shiro官方提供了shiro-ehcache，实现了整合EhCache作为Shiro的缓存工具。可以缓存认证执行的Realm方法，减少对数据库的访问，提高认证效率

（1）添加依赖

```xml
<!--Shiro整合EhCache-->
<dependency>
 	<groupId>org.apache.shiro</groupId>
 	<artifactId>shiro-ehcache</artifactId>
 	<version>1.4.2</version>
</dependency>
<dependency>
 	<groupId>commons-io</groupId>
 	<artifactId>commons-io</artifactId>
 	<version>2.6</version>
</dependency>
```

（2）在resources下添加配置文件ehcache/ehcache-shiro.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ehcache name="ehcache" updateCheck="false">
 	<!--磁盘的缓存位置-->
 	<diskStore path="java.io.tmpdir"/>
 	<!--默认缓存-->
 	<defaultCache
 		maxEntriesLocalHeap="1000"
		eternal="false"
 		timeToIdleSeconds="3600"
 		timeToLiveSeconds="3600"
 		overflowToDisk="false">
 	</defaultCache>
 	<!--登录认证信息缓存：缓存用户角色权限-->
	<cache name="loginRolePsCache"
 		maxEntriesLocalHeap="2000"
 		eternal="false"
 		timeToIdleSeconds="600"
 		timeToLiveSeconds="0"
 		overflowToDisk="false"
 		statistics="true"/>
</ehcache>
```

（3）修改配置类ShiroConfig

```java
//配置 SecurityManager
@Bean
public DefaultWebSecurityManager defaultWebSecurityManager(){
 	//1 创建 defaultWebSecurityManager 对象
 	DefaultWebSecurityManager defaultWebSecurityManager = new DefaultWebSecurityManager();
 	//2 创建加密对象，并设置相关属性
 	HashedCredentialsMatcher matcher = new HashedCredentialsMatcher();
 	//2.1 采用 md5 加密
 	matcher.setHashAlgorithmName("md5");
 	//2.2 迭代加密次数
 	matcher.setHashIterations(3);
 	//3 将加密对象存储到 myRealm 中
 	myRealm.setCredentialsMatcher(matcher);
 	//4 将 myRealm 存入 defaultWebSecurityManager 对象
 	defaultWebSecurityManager.setRealm(myRealm);
 	//4.5 设置 rememberMe
 
	defaultWebSecurityManager.setRememberMeManager(rememberMeManager());
 	//4.6 设置缓存管理器
 	defaultWebSecurityManager.setCacheManager(getEhCacheManager());
 	//5 返回
 	return defaultWebSecurityManager;
}

//缓存管理器
public EhCacheManager getEhCacheManager(){
 	EhCacheManager ehCacheManager = new EhCacheManager();
 	InputStream is = null;
 	try {
 		is = ResourceUtils.getInputStreamForPath("classpath:ehcache/ehcache-shiro.xml");
 	} catch (IOException e) {
 		e.printStackTrace();
 	}
 	CacheManager cacheManager = new CacheManager(is);
 	ehCacheManager.setCacheManager(cacheManager);
 	return ehCacheManager;
}
```

（4）启动测试

第一次登录可以看到查询角色、权限信息

![](img/44.png)

![](img/45.png)

先清除日志，再点击角色认证、权限认证，查看日志，没有查询数据库

![](img/46.png)

#### SpringCache实现简易缓存

添加依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
```



只需要在Real调用的service方法中缓存相应的数据即可

```java
@Override
// 缓存名称为role:,key为用户名，在返回值不为空的情况下进行缓存
@Cacheable(value = "role:", key = "#username", unless = "#result == null ")
public List<String> getUserRoleByName(String username) {
    return mapper.getUserRoleInfoByName(username);
}

@Override
// 缓存名称为permission:,key为相应的角色数组，在返回值不为空的情况下进行缓存
@Cacheable(value = "permission:", key = "#roles.toArray()", unless = "#result == null")
public List<String> getUserPermissionByRoles(List<String> roles) {
    return mapper.getPermissionByRoles(roles);
}

@GetMapping("/login")
// 在登出操作后，清除名称为role:和permission:下的所有缓存
@CacheEvict(value = {"role:", "permission:"}, allEntries = true)
public String login() {
    return "login";
}
```



### 3.8 会话管理 

**1、SessionManager**

​	会话管理器，负责创建和管理用户的会话（Session）生命周期，它能够在任何环境中 在本地管理用户会话，即使没有Web/Servlet/EJB容器，也一样可以保存会话。默认情况下，Shiro会检测当前环境中现有的会话机制（比如Servlet容器）进行适配，如果没有（比如独立应用程序或者非Web环境）,它将会使用内置的企业会话管理器来提供相应的会话管理服务，其中还涉及一个名为SessionDAO的对象。SessionDAO负责Session的持久化操作（CRUD），允许Session数据写入到后端持久化数据库。

**2、会话管理实现**

SessionManager由SecurityManager管理。Shiro提供了三种实现

（1）DefaultSessionManager：用于JavaSE环境 

（2）ServletContainerSessionManager：用于web环境，直接使用Servlet容器的会话 

（3）DefaultWebSessionManager：用于web环境，自己维护会话（不使用Servlet容器的会话管理）

**3、获得session方式**

（1）实现

​	Session session = SecurityUtils.getSubject().getSession(); session.setAttribute(“key”,”value”)

（2）说明

​	Controller中的request，在shiro过滤器中的doFilerInternal方法，被包装成ShiroHttpServletRequest。

​	SecurityManager和SessionManager会话管理器决定session来源于ServletRequest还是由Shiro管理的会话。

​	无论是通过request.getSession或subject.getSession获取到session，操作 session，两者都是等价的。