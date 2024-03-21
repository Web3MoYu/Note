# Mybatis和SpringBoot3

### Property 'sqlSessionFactory' or 'sqlSessionTemplate' are required

出现这个问题是因为，mybatisplus使用的mybatis的版本比较低，换成springboot2.7.7就行了。如果想要使用springboot3就要使用mybatisplus3.5.3.1。

### Failed to determine a suitable driver class

最大的原因就是Springboot3不支持druid数据源，手动配置或采用下面的方法

```java
@Configuration
public class DataSourceConfig {

    @Resource
    Environment env;

    @Bean
    public DataSource getDataSource() {
        DruidDataSource druidDataSource = new DruidDataSource();
        druidDataSource.setUrl(env.getProperty("spring.datasource.druid.url"));
        druidDataSource.setUsername(env.getProperty("spring.datasource.druid.username"));
        druidDataSource.setPassword(env.getProperty("spring.datasource.druid.password"));
        return druidDataSource;
    }
}
```



在解决Property 'sqlSessionFactory' or 'sqlSessionTemplate' are required之后，如果使用druid会出现Failed to determine a suitable driver class的错误。正确写法如下

```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/frontreact
    driver-class-name: com.mysql.cj.jdbc.Driver
    username: root
    password: root
```

```yaml
# 这样写在SpringBoot3中会出现Failed to determine a suitable driver class的错误，要使用上面那种方式或者手动配置数据源
spring:
    druid:
      url: jdbc:mysql://localhost:3306/frontreact
      driver-class-name: com.mysql.cj.jdbc.Driver
      username: root
      password: root
```



# 遇到的问题

### 前期开发的跨域问题

使用MvcConfig实现WebMvcConfigurer重写addCorsMappings即可

```java
@Configuration
public class WebMvcConfig implements WebMvcConfigurer {

    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**")
                .allowedOrigins("http://127.0.0.1:7070")
                .allowedMethods("OPTIONS", "GET", "POST", "PUT", "DELETE")
                .allowCredentials(true);
    }
}
```

