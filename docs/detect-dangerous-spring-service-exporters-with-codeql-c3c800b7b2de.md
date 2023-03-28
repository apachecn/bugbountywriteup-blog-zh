# 使用 CodeQL 检测危险的 Spring 服务导出程序

> 原文：<https://infosecwriteups.com/detect-dangerous-spring-service-exporters-with-codeql-c3c800b7b2de?source=collection_archive---------1----------------------->

## 如何确定 CVE-2016-1000027 不影响你的申请

在这篇博文中，我将讨论用 CodeQL 查询检测不安全的 Spring Exporters。首先，我将描述收到 CVE-2016-1000027 的问题。接下来，我将展示易受攻击的代码是什么样的，以及如何在应用程序中缓解这个问题。然后，我将描述 CodeQL 查询是如何工作的。此外，我将展示查询中发现的几个漏洞。

![](img/8f5d192311b1895644c1c4f7f64781af.png)

照片由[迈克·肯尼利](https://unsplash.com/@asthetik?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 什么是春季出口商？

Spring 框架提供了将服务 bean 导出为端点的类。服务导出器从传入的请求中读取数据，然后将数据传递给底层 bean。数据可以包含序列化的对象。

例如，`HttpInvokerServiceExporter`和`SimpleHttpInvokerServiceExporter`类将指定的服务 bean 导出为 HTTP 端点。这些导出器扩展了使用默认 Java 反序列化机制来解析传入请求中的数据的`RemoteInvocationSerializingExporter`。

# 什么是 CVE-2016-1000027？

默认的 Java 反序列化机制可以通过`ObjectInputStream`类获得。这种机制很容易受到攻击。如果攻击者可以让应用程序反序列化恶意数据，在最坏的情况下，可能会导致任意代码执行。

Spring 的`RemoteInvocationSerializingExporter`使用默认的 Java 反序列化机制来解析数据。因此，扩展它的所有类都容易受到反序列化攻击。弹簧框架至少包含延伸出`RemoteInvocationSerializingExporter`的`HttpInvokerServiceExporter`和`SimpleHttpInvokerServiceExporter`。这些导出器使用不安全的 Java 反序列化机制解析来自 HTTP 主体的数据。

[该问题被 Tenable](https://www.tenable.com/security/research/tra-2016-20) 发现并报告。问题收到 [CVE-2016-1000027](https://nvd.nist.gov/vuln/detail/CVE-2016-1000027) 。Spring 团队对此做出了回应，摒弃了易受攻击的类，并在文档中添加了一条注释，警告用户关于[问题](https://github.com/spring-projects/spring-framework/issues/24434)。他们建议避免使用危险的服务出口商。

老实说，如果不破坏使用易受攻击的服务导出器的应用程序，似乎不可能解决这个问题。对此类问题的通常解决方法是在反序列化之前检查序列化的对象是否是允许的类的实例。这可以通过覆盖`ObjectInputStream.resolveClass()`方法或使用 Apache Commons IO 中的`ValidatingObjectInputStream`来完成。这里的关键是默认的允许类列表。如果默认列表是限制性的，该修复可能会破坏应用程序。如果列表是许可的，那么应用程序在默认情况下仍然容易受到攻击，用户需要明确指定允许的安全类来消除漏洞。Spring 团队决定不提供在易受攻击的导出器中配置允许反序列化的类的方法。

CVE-2016-1000027 在 NVD 发布后，安全扫描器开始针对使用 Spring 框架的应用程序报告此问题。由于 Spring 框架最近非常流行，许多用户都收到了这样的警告。许多用户的问题是他们不知道如何处理这些提醒。这个问题不能仅仅通过更新应用程序中的 Spring Framework 版本来解决，因为 Spring Framework 没有解决这个问题的方法。

# 易受攻击的代码示例

使用一个不安全的服务导出器很容易使应用程序变得易受攻击。以下示例显示了基于`HttpInvokerServiceExporter`的易受攻击的 HTTP 端点:

下一个例子展示了如何在 Spring 框架的 XML 配置中定义相同的端点:

出于演示目的，我还为 CVE-2016-1000027 编写了一个简单的 [PoC。这是一个简单的 Spring 应用程序，它使用了一个易受攻击的服务导出器。该存储库包含一个针对易受攻击端点的演示漏洞利用。](https://github.com/artem-smotrakov/cve-2016-1000027-poc)

# 如何减轻 CVE-2016-1000027

最好的方法是停止使用`HttpInvokerServiceExporter`和`SimpleHttpInvokerServiceExporter`或者任何其他基于`RemoteInvocationSerializingExporter`的出口商。相反，可以使用 API 端点的其他消息格式之一。比如 JSON。确保底层反序列化机制配置正确，这样反序列化攻击就不可能发生。

如果易受攻击的出口商不能被替换，考虑使用在 [JEP 290](https://openjdk.java.net/jeps/290) 中引入的全局反序列化过滤器。

# 用于检测不安全 Spring 导出器的 CodeQL 查询

[CodeQL](https://securitylab.github.com/tools/codeql) 是一个代码分析引擎。它允许您为代码编写查询来检测各种问题，包括安全问题。让我们看看它如何帮助我们检测不安全的 Spring 服务出口商。

如上面的代码片段所示，不安全的服务导出器可以用两种方式定义:

*   Spring 配置类中创建 bean 的方法
*   XML 配置中的 bean

CodeQL 可以涵盖这两种方式。这正是我们所需要的。

在 Spring 配置类中，我们需要寻找以下方法:

*   该类应该有一个使其成为配置的注释。例如，`@Configuration`标注。
*   该方法应该返回一个扩展`RemoteInvocationSerializingExporter`的类的实例。
*   该方法应该有`@Bean`注释。

下面的 CodeQL 查询实现了这些想法:

在 XML 配置中，我们只需要寻找使用扩展了`class`属性中的`RemoteInvocationSerializingExporter`的类的`<bean>`元素。以下简单的 CodeQL 查询实现了这一点:

如果安全扫描器针对您的应用程序报告 CVE-2016-1000027，您可以使用上面的 CodeQL 查询来检查应用程序是否真的易受攻击。[这些查询也被添加到 CodeQL 实验查询包](https://github.com/github/codeql/pull/5260)。

# 参考

*   [使用 Spring 的远程和 web 服务](https://docs.spring.io/spring-framework/docs/2.0.x/reference/remoting.html)
*   [Spring 文档—remote invocationserializingexporter](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/remoting/rmi/RemoteInvocationSerializingExporter.html)
*   [Spring 文档— HttpInvokerServiceExporter](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/remoting/httpinvoker/HttpInvokerServiceExporter.html)
*   [CodeQL 文档](https://codeql.github.com/docs/)