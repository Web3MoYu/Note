# Swagger

## 介绍

使用Swagger你只需要按照它的规范去定义接口及接口相关的信息，再通过Swagger衍生出来的一系列项目和工具，就可以做到生成各种格式的接口文档，以及在线接口调试页面等等。
官网：https://swagger.io/
knife4j是为ava MVC框架集成Swagger生成Api文档的增强解决方案。

```xml
<dependency>
   <groupId>com.github.xiaoymin</groupId>
   <artifactId>knife4j-spring-boot-starter</artifactId>
   <version>3.0.3</version>
</dependency>
```

## 使用方式

操作步骤：
1、导入knife4j的maven坐标

2、导入knife4j相关配置类

```java
@Configuration
@Slf4j(topic = "Mvc配置类")
@EnableSwagger2 // 开启
@EnableKnife4j  // 开启
public class WebMvcConfig extends WebMvcConfigurationSupport {

    /**
     * 设置静态资源映射
     * @param registry
     */
    @Override
    protected void addResourceHandlers(ResourceHandlerRegistry registry) {
        log.info("开始进入静态资源映射");
        registry.addResourceHandler("/backend/**").addResourceLocations("classpath:/backend/");
        registry.addResourceHandler("/front/**").addResourceLocations("classpath:/front/");
    }

    /**
     * 配置mvc框架的消息转换器
     * @param converters
     */
    @Override
    protected void extendMessageConverters(List<HttpMessageConverter<?>> converters) {
        log.info("扩展消息转换器..");
        // 创建消息转换器对象
        MappingJackson2HttpMessageConverter messageConverter = new MappingJackson2HttpMessageConverter();

        //设置对象转换器，底层使用Jackson将java对象转换为json
        messageConverter.setObjectMapper(new JacksonObjectMapper());

        //将上面的消息转换器对象追加到mvc框架的转换器集合中
        converters.add(0,messageConverter);
    }

    @Bean
    public Docket createRestApi(){
            return new Docket(DocumentationType.SWAGGER_2)
                    .apiInfo(apiInfo())
                    .select()
                    .apis(RequestHandlerSelectors.basePackage("com.lsh.reggie.controller"))
                    .paths(PathSelectors.any())
                    .build();

    }

    private ApiInfo apiInfo(){
        return new ApiInfoBuilder()
                .title("瑞吉外卖")
                .version("1.0")
                .description("瑞吉外卖接口文档")
                .build();
    }
}

```



3、设置静态资源，否则接口文档页面无法访问

```java
registry.addResourceHandler("doc.html").addResourceLocations("classpath:/META-INF/resources/");
registry.addResourceHandler("/webjars/**").addResourceLocations("classpath:/META-INF/resources/webjars/")
```



4、在LoginCheckFilter中设置不需要处理的请求路径

```java
 // 匹配不需要拦截的路径
String[] urls = new String[]{
    "/employee/login",
    "/employee/logout",
    "/backend/**",
    "/front/**",
    "/common/**",
    "/user/sendMsg", // 移动端发送短信
    "/user/login",   // 移动端登录
    "/doc.html",
    "/webjars/**",
    "/swagger-resources",
    "/v2/api-docs"
};
```

## 常用注解

| 注解               | 说明                                                     |
| ------------------ | -------------------------------------------------------- |
| @Api               | 用在请求的类上，例如Controller，表示对类的说明           |
| @ApiModel          | 用在类上，通常是实体类，表示一个返回响应数据的信息       |
| @ApiModelProperty  | 用在属性上，描述响应类的属性                             |
| @ApiOperation      | 用在请求的方法上，说明方法的用途，作用                   |
| @ApiImplicitParams | 用在请求的方法上，表式一组参数说明                       |
| @ApiImplicitParam  | 用在@ApiImplicitParams注解中，指定一个请求参数的各个方面 |

### @Api

```java
@Api(tags = "套餐相关API")
public class SetmealController {
    ....
}
```

### @ApiModel,@ApiModelProperty

```java
@ApiModel("套餐")
public class Setmeal implements Serializable {
    private static final long serialVersionUID = 1L;

    @ApiModelProperty("主键")
    private Long id;
    ....
}
```

### @ApiOperation,@ApiImplicitParams,@ApiImplicitParam

```java
@GetMapping("/page")
@ApiOperation("套餐分页查询API")
@ApiImplicitParams({
    @ApiImplicitParam(name = "page", value = "页码", required = true),
    @ApiImplicitParam(name = "pageSize", value = "每页记录数", required = true),
    @ApiImplicitParam(name = "name", value = "套餐名称", required = false)
})
public R<Page<SetmealDto>> page(Integer page, Integer pageSize, String name) {
    //Page<SetmealDto> pageDto = setmealService.page(page, pageSize, name);
    Page<SetmealDto> pageDto = setmealService.pageVo(page, pageSize, name);
    return R.success(pageDto);
}
```

