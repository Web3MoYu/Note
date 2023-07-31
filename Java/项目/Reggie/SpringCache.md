# SpringCache

## 介绍

Spring Cache是一个框架，实现了基于注解的缓存功能，只需要简单地加一个注解，就能实现缓存功能。
Spring Cache:提供了一层抽象，底层可以切换不同的cache实现。具体就是通过CacheManager接口来统一不同的缓
存技术。
CacheManager是Spring提供的各种缓存技术抽象接口。

针对不同的缓存技术需要实现不同的CacheManager:

| CacheManager      | 描述                               |
| ----------------- | ---------------------------------- |
| EhCacheManager    | 使用EhCache作为缓存技术            |
| GuavaCacheManager | 使用Google的GuavaCache作为缓存技术 |
| RedisCacheManager | 使用Redis作为缓存技术              |

## 常用注解

| 注解           | 说明                                                         |
| -------------- | ------------------------------------------------------------ |
| @EnableCaching | 开启缓存注解功能                                             |
| @Cacheable     | 在方法执行前spring先查看缓存中是否有数据，如果有数据，则直接返回缓存数据;<br/>若没有数据，调用方法并将方法返回值放到缓存中 |
| @CachePut      | 将方法的返回值放到缓存中                                     |
| @CacheEvict    | 将一条或多条数据从缓存中删除                                 |

在spring bootI项目中，使用缓存技术只需在项目中导入相关缓存技术的依赖包，并在启动类上使用@EnableCaching:开启缓存支持即可。
例如，使用Redis作为缓存技术，只需要导入Spring data Redis的maven坐标即可。

## @CachePut

- 将方法的返回值放入缓存

| 参数      | 说明                                                         |
| --------- | ------------------------------------------------------------ |
| value     | 缓存的名称，每个缓存名称下可以有多个key,可以简单的理解为一个分类 |
| key       | 缓存的key，支持Spring表达式                                  |
| condition | 只有满足表达式时才缓存数据，这个虽然也支持Spring表达式，但是没有result |
| unless    | 满足表达式时不缓存数据，这个有result                         |

```java
/*
key的用法很灵活
- #result 代表该方法的返回值
- #user 代表该方法的某个参数（名字要和参数名一致）
- #root 代表该方法
*/
@CachePut(value = "userCache", key = "#user.id")
public User save(User user) {
	userService.save(user);
	return user;
}
```

## @CacheEvict

- 将一条或多条数据从缓存中删除，清除的是指定value下的

| 参数             | 说明                                                         |
| ---------------- | ------------------------------------------------------------ |
| value            | 缓存的名称，每个缓存名称下可以有多个key,可以简单的理解为一个分类 |
| key              | 缓存的key，支持Spring表达式                                  |
| allallEntries    | 清空所有的缓存，默认关闭                                     |
| beforeInvocation | 是否在方法执行前就清空，默认为 false                         |

```java
/*
key的用法同上
也可以使用#root.args[1]，#p1,来获取方法的第二个参数的值
*/
@CacheEvict(value = "userCache", key = "#id")
@DeleteMapping("/{id}")
public void delete(@PathVariable Long id) {
	userService.removeById(id);
}
```

## @Cacheable

- 在方法执行前spring先查看缓存中是否有数据，如果有数据，则直接返回缓存数据;<br/>若没有数据，调用方法并将方法返回值放到缓存中

| 参数      | 说明                                                         |
| --------- | ------------------------------------------------------------ |
| value     | 缓存的名称，每个缓存名称下可以有多个key,可以简单的理解为一个分类 |
| key       | 缓存的key，支持Spring表达式                                  |
| condition | 只有满足表达式时才缓存数据，这个虽然也支持Spring表达式，但是没有result |
| unless    | 满足表达式时不缓存数据，这个有result                         |

```java
@GetMapping("/{id}")
@Cacheable(value = "userCache", key = "#id", unless = "#result == null")
public User getById(@PathVariable Long id) {
	return userService.getById(id);
}

@GetMapping("/list")
@Cacheable(value = "userCache", key = "#user.id + '_' + #user.name")
public List<User> list(User user) {
	LambdaQueryWrapper<User> wrapper = new LambdaQueryWrapper<>();
	wrapper.eq(user.getId() != null, User::getId, user.getId())
		   .eq(user.getName() != null, User::getName, user.getName());

	return userService.list(wrapper);
}
```

## 使用Redis和Springboot-start-cache

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

只需要导入依赖包即可，其他照上进行
