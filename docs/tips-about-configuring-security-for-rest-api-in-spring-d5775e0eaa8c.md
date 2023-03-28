# 在 Spring 中为 REST API 配置安全性

> 原文：<https://infosecwriteups.com/tips-about-configuring-security-for-rest-api-in-spring-d5775e0eaa8c?source=collection_archive---------1----------------------->

在大多数情况下，REST APIs 应该只由授权方访问。Spring framework 提供了许多为应用程序配置身份验证和授权的方法。另一个好处是框架通常提供相对较好的默认设置。但是尽管如此，理解发生了什么可能比依赖默认值更好。

这篇文章列出了在配置或检查基于 Spring (boot)框架的 RESTful 应用程序的认证和授权设置时需要注意的一些事情。然而，这并不是一个全面的指导方针(如果存在这样的指导方针的话),它告诉我们如何为基于 Spring framework 的应用程序配置身份验证和授权。它更像是一个技巧和建议的集合。此外，我们非常欢迎任何其他建议和意见。

# 考虑使用 OAuth2 或 JWT

通常只有经过授权的用户或应用程序才能访问 RESTful 服务。RESTful web 应用程序通常不使用 HTTP 会话。相反，它对每个传入的请求执行访问检查。

限制访问 RESTful 应用程序的方法之一是开始使用 API 键。但是 OAuth2 或 JWT (JSON Web tokens)听起来可能是一个更好的选择。OAuth2 和 JWT 是 RFC 中描述的认证和授权框架。这些框架允许以比 API 密钥更灵活的方式配置和控制身份验证和授权过程。但是这样做的代价是额外的基础设施和复杂性。幸运的是，已经有了这些标准的实现，甚至还有提供所有必需基础设施的服务(例如，AWS 中的[应用负载平衡器支持 OAuth2](https://aws.amazon.com/blogs/aws/built-in-authentication-in-alb/) )。

即使应用程序使用 OAuth2 或 JWT，我们也需要确保它正确使用，例如:

*   应用程序验证每个请求的令牌和访问权限
*   应用程序不公开访问令牌(例如，不应该将任何令牌记录到日志中)
*   访问令牌具有正确的生存期
*   等等

顺便说一下， [OWASP 为认证和授权提供了很好的指导方针](https://www.owasp.org/index.php/Top_10-2017_A2-Broken_Authentication)。

# 强制 HTTPS

这个建议可以在每一个关于保护 web 应用程序的指南/文章/书籍/演示中找到。使用 TLS 非常重要，尤其是当应用程序使用 OAuth2(或 API 密钥)进行身份验证时，因为该协议明确依赖 TLS 来提供机密性和完整性。你可以考虑使用 [TLS 1.3，它提供了更好的针对降级攻击的保护](https://blog.gypsyengineer.com/en/security/how-does-tls-1-3-protect-against-downgrade-attacks.html)，更好的密码套件等等。[Java 11](https://blog.gypsyengineer.com/en/security/whats-new-security-features-in-java-11.html)增加了对 TLS 1.3 的支持。

春天可以用`requiresSecure()`的方法。不要忘记启用 [HSTS 割台](https://docs.spring.io/spring-security/site/docs/4.2.5.RELEASE/apidocs/org/springframework/security/config/annotation/web/configurers/HeadersConfigurer.html#httpStrictTransportSecurity--)。这里有一个例子:

一个应用程序可能只支持普通 HTTP，并位于提供 TLS 的反向代理之后。在这种情况下，确保反向代理配置为使用 HTTPS 和 HSTS 报头。代理应该拒绝普通的 HTTP 连接，或者总是重定向到 HTTPS。

# 配置会话管理

Spring 框架提供了一种建立 HTTP 会话的机制。如果 RESTful 服务对每个请求进行认证(大多数情况下都是这样)，那么它不需要任何会话管理机制。在这种情况下，最好指示 Spring framework 不要创建任何会话:

# 禁用登录和注销页面

Spring framework 提供了现成的登录表单和注销页面。这对于使用 GUI 的 web 应用程序可能很有用，但是 RESTful 应用程序很可能不需要这些页面。如果应用程序使用 OAuth2、JWT 或 API 令牌进行访问控制，肯定会出现这种情况。然后，禁用应用程序的这些功能可能是有意义的:

# 禁用基本 HTTP 身份验证

RESTful 应用程序很可能不需要 HTTP 基本身份验证，因此最好禁用它:

# 禁用匿名访问

如果 RESTful 服务应该只由授权的客户端访问，那么最好禁用匿名访问:

# 通过 HTTP 方法授权请求可能会导致问题

`antMatchets()` method 允许指定您想要配置访问的 HTTP 方法。它可能如下所示:

根据安全配置的其余部分，这可能会导致一个问题，因为我们在上面的配置中遗漏了 HEAD 方法。为了避免这样的问题，最好在`antMatchers()`中指定 API 端点，要求所有请求都经过认证，并在最后调用`denyAll()`。`authenticated()`和`fullyAuthenticated()`方法可用于要求对所有请求进行认证。不同的是，最后一个没有考虑到勿忘我功能。顺便说一下，RESTful 服务很可能不需要“勿忘我”。安全配置可能是这样的:

# 禁止使用随机密码创建默认用户

默认情况下，Spring framework 会创建一个带有随机密码的默认用户`user`。然后，框架将密码打印到日志中。它看起来像下面这样:

```
Using default security password: 94251362-f9cc-4c01-95be-121e5b6e1865
```

最有可能的是，一个应用程序不需要在生产中使用它，尤其是当它使用 OAuth2 或 JWT 时。所以，即使不疼也可能禁用它比较好。在安全配置中覆盖`authenticationManagerBean()`方法是有帮助的，尽管这个解决方案看起来有点奇怪(你可以在这里找到更多的细节):

# 参考

*   [保护网络应用](https://spring.io/guides/gs/securing-web/)
*   [Spring 安全架构](https://spring.io/guides/topicals/spring-security-architecture/)
*   [休息安全备忘单](https://www.owasp.org/index.php/REST_Security_Cheat_Sheet)

*原载于 2018 年 10 月 10 日*[*blog.gypsyengineer.com*](https://blog.gypsyengineer.com/en/security/tips-configuring-security-rest-api-spring.html)*。*

![](img/950d9f80f4efa37f04a7d28653ef6f1c.png)

来自[https://spring.io/projects/spring-framework](https://spring.io/projects/spring-framework)

*关注* [*Infosec 报道*](https://medium.com/bugbountywriteup) *获取更多此类精彩报道。*

[](https://medium.com/bugbountywriteup) [## 信息安全报道

### 收集了世界上最好的黑客的文章，主题从 bug 奖金和 CTF 到 vulnhub…

medium.com](https://medium.com/bugbountywriteup)