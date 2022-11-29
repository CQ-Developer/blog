# RoleHierarchy

spring security 支持角色之间的层级关系。本文同样基于 spring boot **2.7.5** 讨论 spring security 的权限继承实现。

假设存在这样3个角色：

1. ROLE_ADMIN

2. ROLE_DEV

3. ROLE_USER

这三个角色的权限关系应该是 *ROLE_ADMIN > ROLE_DEV > ROLE_USER*，也就是 *ROLE_ADMIN* 拥有最高的权限，而 *ROLE_USER* 则拥有最低的权限。

假设存在一个 HTTP 端点 `/a` 需要 *ROLE_DEV* 角色才能访问，那么当一个拥有 *ROLE_ADMIN* 的用户访问时，应该给予授权。而当拥有 *ROLE_USER* 角色的用户访问时，应该拒绝授权。

在 [授权架构](./spring-security-1.md#authorization-architecture) 中说明了 spring security 的授权职责是由两个 `Filter` 完成。

- FilterSecurityInterceptor

- AuthorizationFilter

# RoleHierarchy in FilterSecurityInterceptor

其中 `FilterSecurityInterceptor` 通过 `AccessDcisionManager` 天然的支持了权限继承，所以我们从它开始入手说明。

这里需要说明一个 [官方文档](https://docs.spring.io/spring-security/reference/5.7.4/servlet/authorization/architecture.html#authz-hierarchical-roles) 的错误。

```txt
Here we have four roles in a hierarchy ROLE_ADMIN ⇒ ROLE_STAFF ⇒ ROLE_USER ⇒ ROLE_GUEST. A user who is authenticated with ROLE_ADMIN, will behave as if they have all four roles when security constraints are evaluated against an AuthorizationManager adapted to call the above RoleHierarchyVoter. The > symbol can be thought of as meaning "includes".
```

这里说明了权限继承适用于 `AuthorizationManager` 场景，并且给出的示例代码是通过在 `ApplicationContext` 中配置一个 `RoleHierarchyVoter` 的 Bean 实现的。

在前文讨论 [AccessDecisionManager](./spring-security-1.md#accessdecisionmanager) 中说明过，`AccessDecisionManager` 是基于投票实现的，而 `AuthorizationManager` 并不是。

并且就算在 `ApplicationContext` 中配置一个 `RoleHierarchyVoter` 也无法实现权限继承，因为 spring security 默认的 `AccessDecisionVoter` 的实现是 `WebExpressionVoter`。

如果按照官网的配置方式，需要重新配置配置一个 `AccessDecisionManager`，这样的配置就过于沉重了，违背的 spring security 提供的简单方便的配置模式。

通过对源代码的追踪可以知道，在为 `WebExpressionVoter` 配置 `SecurityExpressionHandler` 时，会从 `ApplicationContext` 中查找类型为 `RoleHierarchy` 的 Bean，这部分代码可以在 `ExpressionUrlAuthorizationConfigurer#getExpressionHandler` 方法中找到。

> 注意：低版本中通过 `WebSecurityConfigurerAdapter` 

下面的示例通过在 `ApplicationContext` 中注册一个 `RoleHierarchy` 类型的实例，启用了权限继承。

```java
@Bean
public RoleHierarchy roleHierarchy() {
    RoleHierarchyImpl roleHierarchy = new RoleHierarchyImpl();
    String hierarchy = """
            ROLE_ADMIN > ROLE_DEV
            ROLE_DEV > RULE_USER
            """;
    roleHierarchy.setHierarchy(hierarchy);
    return roleHierarchy;
}
```

> 注意：这里使用 **JDK17** 的文本块语法，低版本的 JDK 可以通过 `\n` 表示换行符。

通过该配置后，就可以实现权限继承的功能，如果感兴趣具体的执行流程，可以从 `WebExpressionVoter#vote`方法入手，跟踪源码的执行流程，这里就不详述了。

# RoleHierarchy in AuthorizationFilter

`AuthorizationFilter` 并没有天然的支持权限继承。但是它的优势就是 `AuthorizationManager` 的灵活性极高并且十分简单。

可以通过自定义 `AuthorizationManager` 的方式，自定义权限继承。当然复用 `RoleHierarchy` 也是非常不错的选择。由于 `AuthorizationManager` 是一个函数时接口，所以这里使用方法应用的方式予以实现。

```java
@Bean
public SecurityFilterChain securityFilterChain(HttpSecurity httpSecurity) throws Exception {
    return httpSecurity
            .httpBasic(Customizer.withDefaults())
            .authorizeHttpRequests(c -> c
                    .mvcMatchers(GET, "/a").access(this::roleHierarchyAuthorizationManager)
                    .anyRequest().authenticated())
            .build();
}

// 通过 AuthorizationManager 配置访问 /a 端点需要 ROLE_DEV 权限
private AuthorizationDecision roleHierarchyAuthorizationManager(
        Supplier<Authentication> authentication,
        RequestAuthorizationContext requestAuthorizationContext) {
    // 定义权限继承规则
    RoleHierarchyImpl roleHierarchy = new RoleHierarchyImpl();
    String hierarchy = """
            ROLE_ADMIN > ROLE_DEV
            ROLE_DEV > RULE_USER
            """;
    roleHierarchy.setHierarchy(hierarchy);

    // 获取用户权限
    Collection<? extends GrantedAuthority> authorities = authentication.get().getAuthorities();
    // 获取用户所有可获得的权限
    Collection<GrantedAuthority> reachableGrantedAuthorities = roleHierarchy.getReachableGrantedAuthorities(authorities);

    // 判断用户是否存在 DEV 权限
    boolean matched = reachableGrantedAuthorities.stream()
            .map(GrantedAuthority::getAuthority)
            .anyMatch("ROLE_DEV"::equals);

    // 返回授权结果
    return new AuthorizationDecision(matched);
}
```

这段代码为 */a* 端点配置了实现了权限继承，可以自行验证。

需要注意，这里为了演示并没有将 `RoleHierarchyImpl` 的创建提出来，导致每次鉴权都需要创建一个 `RoleHierarchyImpl` 对象，后期可以自行优化。
