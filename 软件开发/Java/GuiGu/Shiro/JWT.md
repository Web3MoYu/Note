# JWT

​	JWT (全称：Json Web Token)是一个开放标准(RFC 7519)，它定义了一种紧凑的、自包含的方式，用于作为 JSON 对象在各方之间安全地传输信息。该信息可以被验证和信任，因为它是数字签名的。

## **JWT的数据结构**

JWT 一般是这样一个字符串，分为三个部分，以 “.” 隔开：

```properties
xxxxx.yyyyy.zzzzz

eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJrZXkxIjoidmFsdWUxIiwic3ViIjoic3R1ZGVudCIsImF1ZCI6Inh4eHgiLCJrZXkyIjoyLCJuYmYiOjE2ODYzNjQ3NzcsImtleTMiOiJrZXkzIiwiaXNzIjoibHNoIiwiZXhwIjoxNjgxNjk5MTc3LCJpYXQiOjE2ODEwOTQzNzcyMzMsImp0aSI6IjExMSIsImtleSI6InZhbHVlIn0.4dK16jOooO9gIkfhCjjA1ZgI7OwQMVNuV2mlvfVjHng
```

### **Header**

JWT 第一部分是头部分，它是一个描述 JWT 元数据的 Json 对象，通常如下所示。

```properties
{
    "alg": "HS256",
    "typ": "JWT"
}
```

alg 属性表示签名使用的算法，默认为 HMAC SHA256（写为HS256），typ 属性表示令牌的类型，JWT 令牌统一写为JWT。

最后，使用 Base64 URL 算法将上述 JSON 对象转换为字符串保存。

### **Payload**

JWT 第二部分是 Payload，也是一个 Json 对象，除了包含需要传递的数据，还有七个默认的字段供选择。

- iss:jwt签发者
- sub:jwt所面向的用户
- aud:接收jwt的一方
- iat:jwt的签发时间
- exp:jwt的过期时间
- nbf:定义在什么时间之前，该jwt都是不可用的
- jti:jwt的唯一身份标识，主要用来作为一次性token

```properties
{
    "key1": "value1",
    "sub": "student",
    "aud": "xxxx",
    "key2": 2,
    "nbf": 1686364777,
    "key3": "key3",
    "iss": "lsh",
    "exp": 1681699177,
    "iat": 1681094377233,
    "jti": "111",
    "key": "value"
}

```

​	需要注意的是，默认情况下 JWT 是未加密的，任何人都可以解读其内容，因此一些敏感信息不要存放于此，以防信息泄露。JSON 对象也使用 Base64 URL 算法转换为字符串后保存，是可以反向反编码回原样的，这也是为什么不要在 JWT 中放敏感数据的原因。

### **Signature**

```properties
header (base64URL 加密后的)
payload (base64URL 加密后的)
secret
```

## 用法

````java
public class JWTUtils {
    private static final String SING = "!@&SSD~JSA/SJ?D.IW,U<MZ>JK:HR}U{$YWS$N^D!D1!2!WI$QE(!@@)S@NMZN(JH@WH!H-";

    /**
     * 生成token header.payload.sing
     */
    public static String getToken(Map<String, Object> map) {
        Calendar instance = Calendar.getInstance();
        instance.add(Calendar.DATE, 7);// 默认七天过期
        // 创建jwt builder
        JWTCreator.Builder builder = JWT.create();

        Calendar nbf = Calendar.getInstance();
        nbf.set(2023, Calendar.JUNE, 10);
        return builder
                .withIssuer("lsh")//签发者
                .withSubject("student")//所面向的用户
                .withAudience("xxxx")//接收jwt一方
                .withIssuedAt(Instant.ofEpochSecond(System.currentTimeMillis()))//jwt的签发时间
                .withExpiresAt(instance.getTime())//过期时间
                .withExpiresAt(instance.getTime())//指定过期时间
                .withNotBefore(nbf.getTime())//指定在什么时间之前，该jwt都是不可用的
                .withJWTId("111")//jwt的唯一标识
                .withPayload(map)//指定payload
                .withClaim("key", "value")//指定密钥
                .sign(Algorithm.HMAC256(SING));//指定签名算法和密钥


    }

    /**
     * 验证token 合法性
     */
     public static DecodedJWT verify(String token) throws JWTVerificationException {
        return JWT.require(Algorithm.HMAC256(SECRET_KEY)).build().verify(token);
    }
    
}
````

### 从payload中取值

```java

try {
    DecodedJWT verify = JWTUtils.verify(token);
    // 从payload中取值
    System.out.println(verify.getClaim("userId").asInt());
    System.out.println(verify.getClaim("username").asString());
} catch (SignatureVerificationException e) {
    log.info("签名异常");
} catch (TokenExpiredException e) {
    log.info("令牌过期");
} catch (AlgorithmMismatchException e) {
    log.info("算法不匹配");
} catch (InvalidClaimException e) {
    log.info("失效的Payload");
} catch (Exception e) {
    log.info("异常");
}
```

