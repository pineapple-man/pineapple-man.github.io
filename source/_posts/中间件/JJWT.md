---
title: JJWT
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
categories: Java
tags: 中间件
keywords: JJWT
excerpt: 本文主要讲解如何使用 JJWT 进行 JWT
date: 2021-11-27 12:08:32
thumbnailImage:
---
<!-- toc -->


>在学习 JJWT 之前，我推荐你先了解 JWT 相关知识，当然在本站中也摘录有 JWT 相关知识，如果需要学习，请访问{% post_link 认证解决方案 认证解决方案%}这里

JWT 的声明一般被用来在身份提供者和服务提供者间传递被认证的用户身份信息，以便于从资源服务器获取资源，也可以增加一些额外的其它业务逻辑所必须的声明信息


## 概述

JJWT 旨在成为最易使用和理解的库，用于在 JVM 和 Android 上创建和验证 JSON Web 令牌

`JWT`本身是支持加密签名的，在使用签名的`JWT`时，需要注意一下两点：

- 保证 JWT 是由认识的人创建的（JWT 是真实的）
- 保证在创建 JWT 之后没有操纵或改变 JWT（保持其完整性）

`真实性`和`完整性` 保证`JWT`包含可以信任的信息。 如果`JWT`未通过**真实性**或**完整性**检查，应该始终拒绝`JWT`，因为我们无法信任它

## Hello World

仍然按照常见的套路（依赖，HelloWorld，概念）进行学习一个第三方库

### 依赖

```xml
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-api</artifactId>
    <version>0.10.5</version>
</dependency>
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-impl</artifactId>
    <version>0.10.5</version>
    <scope>runtime</scope>
</dependency>
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-jackson</artifactId>
    <version>0.10.5</version>
    <scope>runtime</scope>
</dependency>
```

### 创建 JWT

```java
public static void main(String[] args) {
		SecretKey key = Keys.secretKeyFor(SignatureAlgorithm.HS256);
		String jws = Jwts.builder()
            .setSubject("pineapple-man").signWith(key)
            .compact();
		System.out.println(jws);
    /**eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJwaW5lYXBwbGUtbWFuIn0.moDrltTQbK9mhQflt8_KfVtQY-5NrZg4uvRyz5k-DzQ*/
}
```

上述代码中，构建了一个主题为`pineapple-man`的 JWT，使用的是`HMAC-SHA-256`算法的密钥对 JWT 进行签名，并在最终将它压缩成 String 形式

### 解析 JWT

```java
	public static void main(String[] args) {
		SecretKey key = Keys.secretKeyFor(SignatureAlgorithm.HS256);
		String jws = Jwts.builder().setSubject("pineapple-man").signWith(key).compact();
		System.out.println(jws);
        //解析
	System.out.println(Jwts.parser().setSigningKey(key).parseClaimsJws(jws)
				.getBody().getSubject().equals("pineapple-man"));
	}
```

将之前的密钥用于验证`JWT`的签名，如果它无法验证`JWT`，则抛出`SignatureException`。如果在做 JWT 解析时，发生了验证失败的现象，可以通过捕获`JwtException`异常来自定义失败情况下的处理

```java
try {
    Jwts.parser().setSigningKey(key).parseClaimsJws(compactJws);
    //OK, we can trust this JWT
} catch (JwtException e) {
    //don't trust the JWT!
}
```

## JWS 

JWS 就是已经签名的 JWT，下面展示了如何手动实现 JWS

```json
//头部信息
String header = "{'alg':'HS256'}";
String claims = "{'sub':'pineapple-man'}";
//对他们分别进行`UTF_8`编码：
byte[] encodedHeader = Base64.getEncoder().encode(header.getBytes(StandardCharsets.UTF_8));
byte[] encodedClaims = Base64.getEncoder().encode(claims.getBytes(StandardCharsets.UTF_8));
//将编码后的Header和Body使用.进行分隔，并连接成一个字符串
String concatenated = Arrays.toString(encodedHeader) + "." + Arrays.toString(encodedClaims);
//使用加密私钥，选择签名算法（此处使用HMAC-SHA-256），并对连接的字符串进行签名
Key key = getMySecretKey()
byte[] signature = hmacSha256( concatenated, key )
//由于签名始终结果是字节数组，因此Base64URL对签名进行编码并使用.将它连接到字符串concatenated后面
String jws = concatenated + '.' + Base64.getEncoder().encode( signature )
```

以上就是 JWS（已经签名的 JWT）生成过程的实现方式

### 创建 JWS

可以使用这种方式创建`JWS`：

1. 使用`Jwts.builder()`方法创建`JwtBuilder`实例
2. 调用`JwtBuilder`方法根据需要添加标头参数和声明
3. 指定要用于对`JWT`进行签名的`SecretKey`或`非对称PrivateKey`
4. 调用`compact()`方法进行压缩和签名，生成最终的`jws`

```java
String jws = Jwts.builder() 	// (1)
    .setSubject("JDKONG")      	// (2) 
    .signWith(key)          	// (3)
    .compact();             	// (4)

```

### 设置 Header Parameters

`JWT Header`提供关于`JWT Claims`相关的内容，格式和加密操作的元数据

如果需要设置一个或多个`JWT`头参数，则可以根据需要简单地多次调用`JwtBuilder#setHeaderParam`

```java
String jws = Jwts.builder()
    .setHeaderParam("kid", "myKeyId")
    // ... etc ...
```

每次调用`setHeaderParam`时，它只是将键值对附加到内部`Header`实例，如果键值已经存在，则会覆盖任何现有的同名键/值对

:notes:不需要设置`alg`或`zip`标头参数，因为`JJWT`会根据使用的签名算法或压缩算法自动设置它们

除此之外，你还可以其他方式，设置`JWT Header`，这里就不阐述

#### 设置 Claims

`Claims`是`JWT`的正文部分，包含`JWT`创建者希望向`JWT`收件人提供的信息，常见的 API 如下

|       API       |                             含义                             |
| :-------------: | :----------------------------------------------------------: |
|   `setIssuer`   | sets the [`iss` (Issuer) Claim](https://tools.ietf.org/html/rfc7519#section-4.1.1) |
|  `setSubject`   | sets the [`sub` (Subject) Claim](https://tools.ietf.org/html/rfc7519#section-4.1.2) |
|  `setAudience`  | sets the [`aud` (Audience) Claim](https://tools.ietf.org/html/rfc7519#section-4.1.3) |
| `setExpiration` | sets the [`exp` (Expiration Time) Claim](https://tools.ietf.org/html/rfc7519#section-4.1.4) |
| `setNotBefore`  | sets the [`nbf` (Not Before) Claim](https://tools.ietf.org/html/rfc7519#section-4.1.5) |
|  `setIssuedAt`  | sets the [`iat` (Issued At) Claim](https://tools.ietf.org/html/rfc7519#section-4.1.6) |
|     `setId`     | sets the [`jti` (JWT ID) Claim](https://tools.ietf.org/html/rfc7519#section-4.1.7) |

```java
String jws = Jwts.builder()
    .setIssuer("me")
    .setSubject("Bob")
    .setAudience("you")
    .setExpiration(expiration) 	//a java.util.Date
    .setNotBefore(notBefore) 	//a java.util.Date 
    .setIssuedAt(new Date()) 	// for example, now
    .setId(UUID.randomUUID()) 	//just an example id
    
    /// ... etc ...
```

当然也可以自定义 Claims，如果如果需要设置一个或多个与上面显示的标准setter方法声明不匹配的自定义声明，可以根据需要多次调用`JwtBuilde.claim` 声明：

```java
String jws = Jwts.builder()
    .claim("hello", "world")
    // ... etc ...
```

每次调用`claim`时，它只是将键值对附加到内部`claims`实例，如果键值已经存在，则会覆盖任何现有的同名`key/value`对，同头部信息一样

### 签名

JWT 规范确定了 12 中标准签名算法，三种密钥算法和九种非对称密钥算法，由于也不是做密码学的这里也不过多展开。自己项目中常见的使用`HMAC-SHA-256`就足够了

建议通过调用`JwtBuilder`的`signWith`方法来指定签名密钥，以及对应的摘要算法，示例如下：

```java
String jws = Jwts.builder()
   // ... etc ...
   .signWith(key) // <---
   .compact();
```

使用`signWith`时，`JJWT`还会自动使用相关的算法标识符设置所需的`alg`头。类似地，如果使用长度为4096位的`RSA PrivateKey`调用`signWith`，`JJWT`将使用`RS512`算法并自动将`alg`头设置为`RS512`

:notes:不能用`PublicKeys`签署`JWT`，因为这总是不安全的。 `JJWT`将拒绝任何指定的`PublicKey`的方式签名，并抛出异常：`InvalidKeyException`

### 解析 JWS

:sailboat:解析 JWS 步骤如下：

1. 使用`Jwts.parser()`方法创建`JwtParser`实例
2. 指定要用于验证`JWS`签名的`SecretKey`或`非对称PublicKey`
3. 使用`jws String`调用`parseClaimsJws(String)`方法，生成原始`JWS`

:notes:如之前所述，整个调用将包装在`try/catch`块中，以防解析或签名验证失败

```java
Jws<Claims> jws;
try {
    jws = Jwts.parser()         	// (1)
    .setSigningKey(key)         	// (2)
    .parseClaimsJws(jwsString); 	// (3)
    // we can safely trust the JWT
catch (JwtException ex) {       	
    // we cannot use the JWT as intended by its creator
}

```

#### 校验 Key

阅读`JWS`时，最重要的事情是指定用于验证JWS加密签名的**密钥**。 如果签名验证失败，则无法安全地信任此`JWT`，应将其丢弃

如果`jws`是使用`SecretKey`签名的，则应在`JwtParser`上指定**相同**的`SecretKey`

```java
Jwts.parser()
  .setSigningKey(secretKey) // <----
  .parseClaimsJws(jwsString);
```

如果`jws`是使用`PrivateKey`签名的，那么应该在`JwtParser`上指定该密钥**相应**的`PublicKey`（不是`PrivateKey`）

```java
Jwts.parser()
  .setSigningKey(publicKey) // <---- publicKey, not privateKey
  .parseClaimsJws(jwsString);
```

如果你的应用程序不只使用一个SecretKey或KeyPair会怎么样？ 如果可以使用不同的SecretKeys或公钥/私钥或两者的组合创建JWS，该怎么办？

在这些情况下，无法使用单个键调用`JwtParser`的`setSigningKey`方法。相反，需要使用`SigningKeyResolver`

如果程序需要使用不同密钥签名的`JWS`，则**不会**调用`setSigningKey`方法。 相反，需要实现`SigningKeyResolver`接口并通过`setSigningKeyResolver`方法在`JwtParser`上指定实例

```java
//实现了 SigningKeyResolver 接口的自定义解析类
SigningKeyResolver signingKeyResolver = getMySigningKeyResolver();
Jwts.parser()
    .setSigningKeyResolver(signingKeyResolver) // <----
    .parseClaimsJws(jwsString);
```

事实上，可以通过从`SigningKeyResolverAdapter`扩展并实现`resolveSigningKey(JwsHeader，Claims)`方法来简化一些事情。 例如：

```java
public class MySigningKeyResolver extends SigningKeyResolverAdapter {
    @Override
    public Key resolveSigningKey(JwsHeader jwsHeader, Claims claims) {
        // implement
    }
}
```

在解析`JWS JSON`之后，`JwtParser`将在验证`jws`签名之前调用`resolveSigningKey()`方法。 这也就允许检查`Jws Header`和`Claims`参数，以帮助查找用于验证特定`jws`的密钥的信息。 **这对于复杂安全模型的应用程序非常强大，这些安全模型可能在不同时间使用不同的密钥或针对不同的用户或客**。

`JWT`规范支持的方法是在创建`JWS`时，在`JWS`头中设置`kid（Key ID）`字段，例如：

```java
Key signingKey = getSigningKey();
String keyId = getKeyId(signingKey); 		//any mechanism you have to associate a key with an ID is fine
String jws = Jwts.builder()
    .setHeaderParam(JwsHeader.KEY_ID, keyId) // 1
    .signWith(signingKey)                    // 2
    .compact();
```

然后在解析期间，`SigningKeyResolver`可以检查`JwsHeader`以获取该`kid`，然后**使用该值从某个位置查找密钥，如数据库**。 例如：

```java
public class MySigningKeyResolver extends SigningKeyResolverAdapter {
    @Override
    public Key resolveSigningKey(JwsHeader jwsHeader, Claims claims) {
        //inspect the header or claims, lookup and return the signing key
        String keyId = jwsHeader.getKeyId(); 	//or any other field that you need to inspect
        Key key = lookupVerificationKey(keyId); //implement me
        return key;
    }
}
```

注意，检查`jwsHeader.getKeyId()`只是查找密钥的最常用方法，也可以检查任意数量的标头字段或声明，以确定如何查找验证密钥。

### Claims 断言

如果正在解析的`JWS`具有特定的子`sub`值，否则可能不信任该令牌。 那么可以使用`JwtParser`上的各种`require *`方法之一来实现

```java
try {
    Jwts.parser().requireSubject("JDKONG").setSigningKey(key).parseClaimsJws(s);
} catch(InvalidClaimException ice) {
    // the sub field was missing or did not have a 'JDKONG' value
}
```

如果缺少某个值而不是不正确的值，那么就不会捕获`InvalidClaimException`，而是捕获`MissingClaimException`或`IncorrectClaimException`：

```java
try {
    Jwts.parser().requireSubject("JDKONG").setSigningKey(key).parseClaimsJws(s);
} catch(MissingClaimException mce) {
    // the parsed JWT did not have the sub field
} catch(IncorrectClaimException ice) {
    // the parsed JWT had a sub field, but its value was not equal to 'JDKONG'
}
```

当然，也可以使用`require(fieldName，requiredFieldValue)`方法来要求自定义字段。例如：

```java
try {
	Jwts.parser().require("field","requiredValue").setSigningKey(key).parseClaimsJws(s);
} catch(InvalidClaimException ice) {
    // the 'myfield' field was missing or did not have a 'myRequiredValue' value
}
```

### 压缩

如果`JWT`的`Claim`集足够大，也就是说，它包含许多`key/value`对，或者单个值非常大或冗长，那么可以通过**压缩声明主体来减小创建的`JWS`的大小**

例如，如果在`URL`中使用生成的`JWS`，压缩可能会很重要，因为由于浏览器，用户邮件代理或HTTP网关兼容性问题，`URL`最好保持在`4096`个字符以下。 较小的`JWT`还有助于降低带宽利用率

### 默认压缩

如果要压缩`JWT`，可以使用`JwtBuilde`r的`compressWith(CompressionAlgorithm)`方法。 例如：

```java
Jwts.builder()
   .compressWith(CompressionCodecs.DEFLATE) // or CompressionCodecs.GZIP
   // .. etc ...
```

使用`DEFLATE`或`GZIP`压缩编解码器，但是在解压缩时，不必执行任何操作，不需要配置`JwtParser`，`JJWT`将按预期自动解压缩主体

### 自定义压缩

如果在创建`JWT`时使用自己的自定义压缩编解码器（通过`JwtBuilder.compressWith`），则需要使用`setCompressionCodecResolver`方法将编解码器提供给`JwtParser`。 例如：

```java
CompressionCodecResolver ccr = new MyCompressionCodecResolver();
Jwts.parser()
    .setCompressionCodecResolver(ccr) // <----
    // .. etc ...
```

通常，`CompressionCodecResolver`实现将检查`zip`标头以找出使用的算法，然后返回支持该算法的编解码器实例。 例如：

```java
public class MyCompressionCodecResolver implements CompressionCodecResolver {
        
    @Override
    public CompressionCodec resolveCompressionCodec(Header header) throws CompressionException {
        
        String alg = header.getCompressionAlgorithm();
            
        CompressionCodec codec = getCompressionCodec(alg); //implement 
            
        return codec;
    }
}
```

## 附录

[Java Web Token 之 JJWT 使用](https://blog.csdn.net/weixin_41540822/article/details/88781964)

