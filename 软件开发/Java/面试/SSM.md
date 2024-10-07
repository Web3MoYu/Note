

一、框架知识分布
--------

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/15d06758b7d841e3e682201c3d8227b4.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/04dfb31d5667aeeb5bca0ab5c22cf4e3.png)

二、Spring
--------

### 1\.spring-单例bean

#### ①问题引入

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/b5d47ab6274696ebeb3e4abed7ed66a9.png)
Spring框架中的bean是单例的
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/5e6a13cbdc20717aeb3926c085d14a4f.png)
singleton：bean在每个SpringIOC容器中只有一个实例。
prototype：一个bean的定义可以有多个实例。

#### ②单例bean是线程安全的吗

count是**可修改**的成员变量需要考虑线程安全
userService是**无法修改**的成员变量不需要考虑线程安全
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/07b3a1e86b97434e2ed2dd74c512d30c.png)

#### ③问题总结

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/6524b4256000846e22a14a72c8ef454a.png)

#### ④实战面试

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/9fa308334936dfa77d1a9d61b1de6b50.png)

### 2\.spring-AOP

#### ①问题引入

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/8336ffc5a4e66d3bfec89051a23b47c3.png)
AOP称为[面向切面编程](https://so.csdn.net/so/search?q=%E9%9D%A2%E5%90%91%E5%88%87%E9%9D%A2%E7%BC%96%E7%A8%8B&spm=1001.2101.3001.7020)，用于将那些与业务无关，但却对多个对象产生影响的公共行为和逻辑，抽取并封装为一个可重用的模块，这个模块被命名为“切面”(Aspect)，**减少系统中的重复代码**，**降低模块间的耦合度**，同时提高了系统的可维护性。

常见的AOP使用场景:
1.记录操作日志（每一个service记录操作）
2.缓存处理（业务加缓存，写在AOP的切面拦截需要添加缓存的业务方法）
3.Spring中内置的事务处理

#### ②AOP记录操作日志

获取请求的用户名、请求方式、访问地址、模块名称、登录ip、操作时间，记录到数据库的日志表中，如下图所示：
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/5a697796b451104013be3a3bb8673854.png)
后台有四个请求接口，需要记录操作日志，使用AOP提供的环绕通知做一个切面，相当于一个通过的代码，遇到请求的时候就可以执行这段代码。
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/24bbbf95973374fd21484443b42271ee.png)
代码解释：https://zhuanlan.zhihu.com/p/596205936?utm_id=0

#### ③Spring事务如何实现的

Spring支持**编程式**事务管理和**声明式**事务管理两种方式。

1.编程式事务控制：需使用TransactionTemplate来进行实现，对业务代码有侵入性，项目中很少使用
2.声明式事务管理：声明式事务管理**建立在AOP之上**的。其本质是通过AOP功能**，对方法前后进行拦截**，将事务处理的功能编织到拦截的方法中，也就是在目标**方法开始之前加入一个事务**，在执行完目标方法之后**根据执行情况提交或者回滚事务**。（常用）
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/3af83a9bd9731377798ebb233e7bb2da.png)

#### ④问题总结

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/71f610ed8a732edbc09fced2fff6fd56.png)

### 3\.Spring-事务失效的场景

#### ①问题引入

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/95ea5b9502034675eb0c9e5dacd1b7df.png)
对spring框架的深入理解、复杂业务的编码经验

1.异常捕获处理
2.抛出检查异常
3.非public方法

#### ②情况一：异常捕获处理

转账的代码中出现一个异常，由于这个异常会被try-catch捕获却没有抛出，所以可能导致事务失效（如果异常没有被捕获，就会发生事务回滚）
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/8f96f5ff4e532f80e9a76ddb1a24b0ac.png)
**原因分析：**事务通知只有捉到了目标抛出的异常，才能进行后续的回滚处理，如果目标自己处理掉异常，事务通知无法知悉
**解决方法：**在catch块添加thrownewRuntimeException(e)抛出
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/affb65cb0652a3f9568a507b2c38399f.png)

#### ③情况二：抛出检查异常

系统找不到指定文件，抛出一个检查异常
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/0f6c0567942e685c1921d0aee5ed43d1.png)
**原因分析：**Spring默认只会回滚非检查异常
**解决方法：**配置rollbackFor属性`Transactional(rollbackFor=Exception.class)`，一旦出现异常都会被事务控制
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/484265bf6bd567767647206ead4204b0.png)

#### ④非public方法导致事务失效

方法没有用public
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/a011c95987bfd57693b6c0d7082ee441.png)
**原因分析：**Spring为方法创建代理、添加事务通知、前提条件都是该方法是public的
**解决方法：**方法改为public

#### ⑤问题总结

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/7582b8d70c32ce56be563a1b36742c55.png)

### 4\.Spring-bean的生命周期（难）

#### ①问题引入

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/1c21a2b5d3e218aa49465d7ffbe0937e.png)
Spring容器是如何管理和创建bean实例
方便调试和解决问题

#### ②BeanDefinition（Bean的定义信息）

Spring容器在进行实例化时，会将xml配置的`<bean>`的信息封装成一个BeanDefinition对象，Spring根据BeanDefinition来创建Bean对象，里面有很多的属性用来描述Bean
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/8e67f18c4678d0bc59486531de1e4bce.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/18342a80b3f6081a6d9ab64473c25f9a.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/45ae1a9c5f8519efebcdf4a3035145bd.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/827ca89c776312710ce3a377ddf84b89.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/8ad7084a4fa3ed0353b7e4718f8f786c.png)

#### ③问题总结

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/1872bf73b312cbbae13014f7f0fc9485.png)

### 5\.Spring-bean的循环依赖

#### ①问题引入

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/aeb675ce105a235754594d7faf826435.png)
在创建A对象的同时需要使用的B对象，在创建B对象的同时需要使用到A对象

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/4c5a159058764d9155f054c17f62c72d.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/93facc5eda9e9a28c0e763b284cd2159.png)

#### ②Spring的循环依赖

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/eaa60dffa22ec01f5e66e070c1ac1fe3.png)

Spring解决循环依赖是通过**三级缓存**，对应的三级缓存如下所示:
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/3a0b092c325cc71b14eaab5720ad74b7.png)
如果要想打破循环依赖,就需要一个中间人的参与,这个中间人就是二级缓存。
一级和二级缓存可以解决一般对象的循环依赖
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/452c739a507fcc7da593f6b29c56858d.png)
代理对象需要用到三级缓存
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/4ae3c3bf9af718c6c2e50dd61628435b.png)
构造方法注入产生的循环依赖问题，使用延迟加载解决问题
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/4d5d4c7dc93a3be5ed0ffd8f7e717425.png)

#### ③问题总结

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/7741993c3ca79e1fd6502ebddf5a2cd3.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/5a6211669fbf44bf1132005bf60dd9a3.png)

三、SpringMVC
-----------

### 1\.SpringMVC-执行流程

#### ①问题引入

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/94831d0501b1df090a9b4d37c79cc5b8.png)
Springmvc的执行流程是这个框架最核心的内容

1.视图阶段（老旧JSP等)
2.前后端分离阶段(接口开发，异步)

#### ②视图阶段（JSP）

重要的组件：前端控制器、处理器映射器、处理器适配器、视图解析器
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/f20fa73540802a30803013e5fadf91d5.png)

#### ③前后端分离阶段(接口开发，异步)

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/a66c271bf8565eb13c2c3a23617a0285.png)

#### ④问题总结

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/00c3edd515f6745a0c69f745a9044b28.png)

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/91bdb98fc5e67b42f0a63dfcdf628d11.png)

四、SpringBoot
------------

### 1\.springboot-自动配置

#### ①问题引入

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/6cda611f16adb6ede0396bb9cad5875b.png)
自动配置主要依赖于`@SpringBootApplication`注解，其中还包含了三个注解
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/5990b57854880176adb403df03946cc2.png)
`@SpringBootConfiguration`：该注解与@Configuration注解作用相同，用来声明当前也是
个配置类。
`@ComponentScan`：组件扫描，默认扫描当前引导类所在包及其子包。
`@EnableAutoConfiguration`：SpringBoot实现自动化配置的核心注解。

#### ②问题总结

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/079798b0309b8aecc9eab1815260d50c.png)

五、Spring-框架中常见的注解
-----------------

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/8ad81d825d7ee0e9ee9631d65dc61494.png)

### 1\.spring常见的注解

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/43db58f76890a3d30198c9d88d1e12fa.png)

### 2\.springMVC常见注解

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/13daaa45710811d6cce6f681d971e442.png)

### 3.springboot常见注解

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/f1c821da280c16d8ffde1004092e4a8d.png)

六、Mybatis
---------

### 1\.Mybatis-执行流程

#### ①问题引入

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/b3e387a7ff87672265a0b8b2088dcfdd.png)

1.理解了各个组件的关系
2.Sql的执行过程（参数映射、sql解析、执行和结果处理)

#### ②Mybatis执行流程

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/c2f2e0ce2d8ea39cd0e8b1b27b82d04b.png)

### 2\.Mybatis支持延迟加载

#### ①问题引入

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/bac8469c92ce557d3c6494e74c8e2295.png)

#### ②立即加载

查询用户信息的同时也可以查询到相关订单信息

UserMapper：
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/12d4341ae694a428cecbbf6f484c7d9d.png)

OrderMapper：
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/354c272eebe1b13cd8aab9476c17e8de.png)

UserTest.java打印输出用户信息
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/83b69240be33d05b743de02be94e560c.png)

执行结果：
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/166c4683c043357ec43c3c357c01b7c4.png)

#### ③延迟加载

`fetchType="lazy"`开启局部延迟加载

UserMapper：
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/8c01629604da62f09a782502f7c5a6c1.png)

UserTest.java打印输出用户信息
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/83b69240be33d05b743de02be94e560c.png)

执行结果：先调用查询用户信息，然后因为调用了getOrderList()，所以还执行了查找对应订单的sql
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/056958dac6f64c10cecb77365400462f.png)

在配置文件中开启**全局延迟加载**：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/551966532af0531d5a00def92ea58e9d.png)

**延迟加载的原理**

1.使用**CGLIB**创建目标对象的代理对象
2.调用目标方法user.getOrderList)时，进入拦截器invoke方法，发现user.getOrderList()是null值，执行sql查询order列表3.把order查询上来，然后调用user.setOrderList(ListorderList)，接着完成user.getOrderList()方法的调用
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/b276deb63bd1e3ed74277134ac6119b2.png)

#### ④问题总结

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/c88a1e1252d89629835f65ac43eb678f.png)

### 3\.Mybatis-一级二级缓存

#### ①问题引入

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/fa2b8ee2ae03c7cc7ca4fb920bbd8a2b.png)

●本地缓存：基于**PerpetualCache**，本质是一个HashMap
●一级缓存：作用域是**session**级别
●二级缓存：作用域是**namespace**和**mapper**的作用域，不依赖于session

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/4681fb2fde068181ec6cce582dd13503.png)

#### ②一级缓存

一级缓存：基于PerpetualCache的HashMap本地缓存，其存储作用域为Session，当Session进行flush或close之后，该Session中的所有Cache就将清空，**默认打开**一级缓存

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/85ec3d171e60a31b2b43407a72529ac2.png)
因为这两个都是查询的同一个id的用户信息，所以第二次查询的时候直接在缓存中取，而不需要再次执行SQL语句
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/84fd342c8090933c9a2144899139d1e7.png)

#### ③二级缓存

二级缓存是基于namespace和mapper的作用域起作用的，不是依赖于SQLsession，默认也是采用PerpetualCache，HashMap存储（默认关闭）
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/0ef6f6c4a01df470146263a64e448318.png)

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/31b7d3396e1eb14cd77bad8b2452bd9b.png)

**开启二级缓存的两个步骤：**

1.全局配置文件
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/9983002fc45351423710fe0930944ef5.png)

2.映射文件：使用`<cache/>`标签让当前mapper生效二级缓存
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/6946bf1252de7df56ea5661591250eaf.png)

3.执行结果：只调用了一次SQL
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/4d03634bed8abe22875a47abf969933c.png)


**注意事项：**

1.对于缓存数据更新机制，当某一个作用域(一级缓存Session/二级缓存Namespaces)的进行了新增、修改、删除操作后，默认该作用域下所有select中的缓存将被clear
2.二级缓存需要缓存的数据实现Serializable接口
3.只有会话提交或者关闭以后，一级缓存中的数据才会转移到二级缓存中、

#### ④问题总结

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/f42bdec5ba2ffbcf284c93a13eee8ae8.png)
