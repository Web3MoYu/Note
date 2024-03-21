# JWT和Shiro整合

## 创建JwtToken

​	首先Shiro是通过UsernamePasswordToken进行的验证，而JWT是是通过token进行验证，所以需要单独写一个认证继承AuthenticationToken

```java
public class JwtToken implements AuthenticationToken {

    // 由用户名和密码换取到的token
    private String jwtToken;

    public JwtToken(String jwtToken) {
        this.jwtToken = jwtToken;
    }

    @Override
    public Object getPrincipal() {
        return jwtToken;
    }

    @Override
    public Object getCredentials() {
        return Boolean.TRUE;
    }
}
```

## 创建JwtRealm

​	原先的ShiroRealm是通过UsernamePasswordToken进行登录验证的，所以需要重新写一个JwtRealm

### Realm

```java
@Component
@Slf4j(topic = "JwtRealm")
public class JwtRealm extends AuthorizingRealm {

    @Resource
    UserService service;

    /**
     * @Description 配置该Realm只支持JWTToken
     * @return
     * @Date 12:38 2023/4/10
     */
    @Override
    public Class<?> getAuthenticationTokenClass() {
        return JwtToken.class;
    }

    // 自定义授权方法：获取当前登录用户的角色，权限信息，返回给shiro用来进行授权认证的
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principals) {
        // 获取主信息这里拿到的就是登录认证里的user,哪里存什么这里就拿什么
        String username = (String) principals.getPrimaryPrincipal();
        // 1.创建对象，封装当前登录用户的角色，权限信息
        SimpleAuthorizationInfo info = new SimpleAuthorizationInfo();
        // 2.从数据库中查询角色信息
        List<String> roles = service.getUserRoleByName(username);
        log.info("{}用户的角色{}", username, roles.toString());
        // 2.1.封装角色信息
        info.addRoles(roles);

        // 3.从数据库中和获取权限信息
        List<String> permissions = service.getUserPermissionByRoles(roles);
        log.info("{}用户的权限{}", username, permissions);
        // 3.1封装权限信息
        info.addStringPermissions(permissions);
        return info;
    }

    // 自定义登录认证方法
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
        // 1.获取JwtToken
        String jwtToken = (String) token.getPrincipal();

        // 2.token解析
        DecodedJWT verify = JWTUtil.verify(jwtToken);
        String username = verify.getSubject();

        log.info("认证完成");
        return new SimpleAuthenticationInfo(username, Boolean.TRUE, getName());
    }
}
```

### JWTUtil

```java
public class JWTUtil {
    // 私钥
    private static final String SECRET_KEY = "SJD(O!I@#()SKD<?X<?Z<D)P!@I#)_ISK>MD<Z>:K@_)#IO)_SI[KDL;AO)PQ@I#FKDJNFKL";

    /**
     * @Description 创建token
     * @param username 用户名
     * @param expire 过期时间
     * @return
     * @Date 11:04 2023/4/10
     */
    public static String creatToken(String username, Date expire) {

        JWTCreator.Builder builder = JWT.create();
        builder.withJWTId(UUID.randomUUID().toString())// 设置token唯一标识
                .withSubject(username) // 设置token的主体
                .withIssuer("lsh")// 签发者
                .withIssuedAt(new Date()); //签发时间
        // 设置过期时间
        builder.withExpiresAt(expire);
        //签发
        return builder.sign(Algorithm.HMAC256(SECRET_KEY));
    }

    /**
     * @Description 解析token
     * @return
     * @Date 11:14 2023/4/10
     */
    public static DecodedJWT verify(String token) throws JWTVerificationException {
        return JWT.require(Algorithm.HMAC256(SECRET_KEY)).build().verify(token);
    }
}

```

## 注入自定义的realm(ShiroConfig)

```java
@Configuration
public class AuthConfig {

    @Resource
    private JwtRealm jwtRealm;

    // 配置SecurityManager
    @Bean
    public DefaultWebSecurityManager defaultWebSecurityManager() {
        DefaultWebSecurityManager manager = new DefaultWebSecurityManager();
        manager.setRealm(jwtRealm);

        // 关闭shiro的session会话功能
        DefaultSubjectDAO subjectDAO = new DefaultSubjectDAO();
        DefaultSessionStorageEvaluator defaultSessionStorageEvaluator = new DefaultSessionStorageEvaluator();
        // 关闭
        defaultSessionStorageEvaluator.setSessionStorageEnabled(false);
        subjectDAO.setSessionStorageEvaluator(defaultSessionStorageEvaluator);
        manager.setSubjectDAO(subjectDAO);
        return manager;
    }


    // 配置权限过滤器
    @Bean
    public ShiroFilterFactoryBean shiroFilterFactoryBean() {
        ShiroFilterFactoryBean filter = new ShiroFilterFactoryBean();
        // 注入安全管理器
        filter.setSecurityManager(defaultWebSecurityManager());

        // 如果用户未登录，跳转到认证的接口
        filter.setLoginUrl("/un-auth");

        // 设置自定义的过滤器
        Map<String, Filter> filters = new LinkedHashMap<>();
        // jwtFilter是过滤器的名称
        filters.put("jwtFilter", new JwtFilter());
        filter.setFilters(filters);

        // 设置拦截规则链
        Map<String, String> chain = new LinkedHashMap<>();
        // 不保存绘画
        chain.put("/api/auth/**", "jwtFilter");

        filter.setFilterChainDefinitionMap(chain);
        return filter;
    }

    /**
     * 添加注解支持，如果不加的话很有可能注解失效
     */
    @Bean
    public DefaultAdvisorAutoProxyCreator defaultAdvisorAutoProxyCreator() {
        DefaultAdvisorAutoProxyCreator defaultAdvisorAutoProxyCreator = new DefaultAdvisorAutoProxyCreator();
        defaultAdvisorAutoProxyCreator.setProxyTargetClass(true);
        return defaultAdvisorAutoProxyCreator;
    }
}
```

## 配置JWT过滤器

```java
@Slf4j(topic = "jwtFilter")
public class JwtFilter extends AccessControlFilter {
    /**
     * @Description 请求到来以后响应的方法
     * @return
     * @Date 14:45 2023/4/10
     */
    @Override
    protected boolean isAccessAllowed(ServletRequest request, ServletResponse response, Object mappedValue) throws Exception {
        // 因为关闭了shiro的状态会话，所以每次都要认证，所以直接返回false，执行下面的onAccessDenied即可
        return false;
    }

    /**
     * @Description token认证未通过，执行下面的方法
     * @return
     * @Date 14:47 2023/4/10
     */
    @Override
    protected boolean onAccessDenied(ServletRequest servletRequest, ServletResponse servletResponse) throws Exception {
        HttpServletRequest request = (HttpServletRequest) servletRequest;
        HttpServletResponse response = (HttpServletResponse) servletResponse;
        // 统一异常处理
        try {
            String token = request.getHeader("jwt");

            // 对该token进行认证
            JwtToken jwtToken = new JwtToken(token);
            // 在login中进行token的认证也就是在jwtRealm进行认证
            Subject subject = SecurityUtils.getSubject();
            subject.login(jwtToken);
        } catch (Exception e) {
            log.info("令牌错误" + e.getMessage());
            String json = JSON.toJSONString(R.error(401, "token有问题", e.getMessage()));
            response.setContentType("text/html;charset=utf-8");
            response.getWriter().write(json);
            return false;
        }
        return true;
    }
}

```

## 将JwtFilter加入到过滤器链中

编辑ShiroConfig中的shiroFilterFactoryBean方法

```java
@Bean
public ShiroFilterFactoryBean shiroFilterFactoryBean() {
    ShiroFilterFactoryBean filter = new ShiroFilterFactoryBean();
    // 注入安全管理器
    filter.setSecurityManager(defaultWebSecurityManager());

    // 如果用户未登录，跳转到认证的接口
    filter.setLoginUrl("/un-auth");

    // 设置自定义的过滤器
    Map<String, Filter> filters = new LinkedHashMap<>();
    // jwtFilter是过滤器的名称
    filters.put("jwtFilter", new JwtFilter());
    filter.setFilters(filters);

    // 设置拦截规则链
    Map<String, String> chain = new LinkedHashMap<>();
    chain.put("/api/auth/**", "jwtFilter");

    filter.setFilterChainDefinitionMap(chain);
    return filter;
}
```

## 不保存Session

- 法1-修改shiroFilterFactoryBean注册filter时加入noSessionCreation

````java
@Bean
public ShiroFilterFactoryBean shiroFilterFactoryBean() {
    ShiroFilterFactoryBean filter = new ShiroFilterFactoryBean();
    // 注入安全管理器
    filter.setSecurityManager(defaultWebSecurityManager());

    // 如果用户未登录，跳转到认证的接口
    filter.setLoginUrl("/un-auth");

    // 设置自定义的过滤器
    Map<String, Filter> filters = new LinkedHashMap<>();
    // jwtFilter是过滤器的名称
    filters.put("jwtFilter", new JwtFilter());
    filter.setFilters(filters);

    // 设置拦截规则链
    Map<String, String> chain = new LinkedHashMap<>();
    // 不保存绘画
    chain.put("/api/auth/**", "noSessionCreation, jwtFilter");

    filter.setFilterChainDefinitionMap(chain);
    return filter;
}
````

- 法2-修改defaultWebSecurityManager(推荐)

```java
// 配置SecurityManager
@Bean
public DefaultWebSecurityManager defaultWebSecurityManager() {
    DefaultWebSecurityManager manager = new DefaultWebSecurityManager();
    manager.setRealm(jwtRealm);

    // 关闭shiro的session会话功能
    DefaultSubjectDAO subjectDAO = new DefaultSubjectDAO();
    DefaultSessionStorageEvaluator defaultSessionStorageEvaluator = new DefaultSessionStorageEvaluator();
    // 关闭
    defaultSessionStorageEvaluator.setSessionStorageEnabled(false);
    subjectDAO.setSessionStorageEvaluator(defaultSessionStorageEvaluator);
    manager.setSubjectDAO(subjectDAO);
    return manager;

}
```

