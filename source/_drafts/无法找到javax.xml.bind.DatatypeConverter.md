
环境说明：

* Spring Boot 3.2.4
* JDK 17

在使用 jjwt 0.9x 版本的整合包封装自定义的 JWTUtils 工具类时，调用 `Jwts.builder()` 创建 Token 中，用了 signWith 报错：

```
Exception in thread "main" java.lang.NoClassDefFoundError: javax/xml/bind/DatatypeConverter
	at io.jsonwebtoken.impl.Base64Codec.decode(Base64Codec.java:26)
	at io.jsonwebtoken.impl.DefaultJwtBuilder.signWith(DefaultJwtBuilder.java:99)
	at org.example.utils.JwtUtils.getTokenBuilder(JwtUtils.java:102)
	at org.example.utils.JwtUtils.generateToken(JwtUtils.java:35)
	at org.example.utils.JwtUtils.generateToken(JwtUtils.java:58)
	at org.example.utils.JwtUtils.main(JwtUtils.java:174)
```

<!--more-->

## 1.1 原因分析

错误 `java.lang.NoClassDefFoundError: javax/xml/bind/DatatypeConverter` 表明 Java XML Binding（JAXB）API 中的 `DatatypeConverter` 在 Java 运行环境中不可用。

这个问题通常出现在 Java 9 及以后的版本，因为从 Java 9 开始，JAXB API 被从 Java SE 平台的默认类路径中移除了，作为引入 Java 模块系统的一部分。

## 1.2 解决方案

如果使用的是 Java 9 或更高版本，并且项目使用 Maven 或 Gradle，可以在项目依赖中显式添加 JAXB API。

**对于 Maven，在 `pom.xml` 中添加以下依赖：**

```xml
<dependencies>
    <!-- JAXB API -->  
    <dependency>  
        <groupId>javax.xml.bind</groupId>  
        <artifactId>jaxb-api</artifactId>  
        <version>2.3.1</version>  
    </dependency>  
    <!-- JAXB 运行时 -->  
    <dependency>  
        <groupId>org.glassfish.jaxb</groupId>  
        <artifactId>jaxb-runtime</artifactId>  
        <version>2.3.1</version>  
    </dependency>  
    <!-- Activation（JAXB 需要） -->  
    <dependency>  
        <groupId>javax.activation</groupId>  
        <artifactId>activation</artifactId>  
        <version>1.1.1</version>  
    </dependency>
</dependencies>
```

**对于 Gradle，在 `build.gradle` 中添加以下依赖：**

```gradle
dependencies {
    // JAXB API
    implementation 'javax.xml.bind:jaxb-api:2.3.1'
    // JAXB 运行时
    implementation 'org.glassfish.jaxb:jaxb-runtime:2.3.1'
    // Activation（JAXB 需要）
    implementation 'javax.activation:activation:1.1.1'
}
```


