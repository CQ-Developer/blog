# Spring Security

spring security 是 spring 家族提供的安全框架。

它不仅提供了传统的基于账号密码的安全认证，也提供了类似于 OAuth2，OIDC 等更加高级的安全认证方式。

并且支持了 spring boot 和 spring cloud。

本文基于 spring boot **2.7.5** 总结对于 spring security 的基本认识。

spring security 为使用 spring boot 的用户提供了自动装配启动器，在 maven 项目中可以通过如下坐标引入 spring security。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

当引入该坐标后，即使在不进行任何配置的情况下，spring security 也在我们的项目中生效了。

在项目启动时，我们将在控制台中看到如下输出。

```text
Using generated security password: 6b4ca58e-4dd4-4aea-8705-3d71af18884a

This generated password is for development use only. Your security configuration must be updated before running your application in production.
```

spring security 的自动装配为我们自动装配了一个用户，用户名为 `user`，密码为 `6b4ca58e-4dd4-4aea-8705-3d71af18884a`。

此时假设我们有一个 controller 端点如下。

```java
@GetMapping("/hello")
public String hello() {
    return "Hello!";
}
```

此时再访问 */hello* 端点将被重定向到一个页面并在登录后，被再次重定向回 */hello* 端点，从而完成访问。



# Auto Configure By Spring Boot

之所有有这种能力，是因为 spring boot 为我们提供了自动装配。通过 `spring-autoconfigure-metadata.properties` 文件列出 servlet 环境下的自动装配类如下。

> 注意：这里排除了 oauth2 的自动装配信息。

> 注意：老版本可以查看 `spring.factories` 文件。

- org.springframework.boot.autoconfigure.security.servlet.SecurityAutoConfiguration

- org.springframework.boot.autoconfigure.security.servlet.SecurityFilterAutoConfiguration

- org.springframework.boot.autoconfigure.security.servlet.UserDetailsServiceAutoConfiguration

![spring security auto configure](./img/spring-security-auto-configuration-architecture.excalidraw.png)

之前登录的用户的就是通过 `UserDetailsServiceAutoConfiguration` 自动装配的。

下面列出几个需要重点关注的类，它们是我们配置的核心。

- org.springframework.boot.web.servlet.DelegatingFilterProxyRegistrationBean

- org.springframework.security.web.DefaultSecurityFilterChain

- org.springframework.security.web.FilterChainProxy

- org.springframework.security.config.annotation.web.builders.HttpSecurity



# Filter Architecture

spring security 的 `Filter` 结构是理解 spring security 的核心，也是安全机制生效的入口。