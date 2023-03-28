# 自动化 Burp Suite -3 |创建宏来替换从响应主体到带有会话验证的请求的 CSRF 令牌

> 原文：<https://infosecwriteups.com/automating-burp-suite-3-creating-macro-to-replace-csrf-token-from-response-body-to-request-param-de37eb54ab5f?source=collection_archive---------0----------------------->

这是自动化 Burp Suite 的第 3 部分，我们将尝试替换响应主体生成的 CSRF 令牌，以请求主体**DVWA 中的 user_token 参数**。查看下一部分，我们将通过 [**burp suite 扩展**](/automating-burp-suite-4-understanding-and-customising-custom-header-from-response-via-burp-macro-214332dda012) **自动定制标题替换。**

这部分很简单。到目前为止，您已经对 Burp Suite 宏有了基本的了解。现在让我们开始在 web 应用程序中创建自动登录。

[![](img/b7ddf962b8f21b4c5f23a9d7c43ae2a5.png)](https://www.yeahhub.com/wp-content/uploads/2018/04/burp-suite-disable-detect-portal.png)

## 步骤:

1.  在[http://localhost/log in . PHP](http://localhost/login.php)上运行 DVWA
2.  选择传递用户名和密码参数的登录请求。

![](img/ae9afb66360c78f2521f3fbb2ecba45a.png)

2.切换到 Burp Suite 中的项目选项选项卡，单击会话，然后单击添加以添加会话处理规则。

3.在`Session handling rule editor`中，填写`Rule Description`:自动登录。在`Rule Actions`中的
，点击添加然后`Run a macro`。

![](img/8b257ec1b0d026f6623cf856b79c089b.png)

4.在`Macro Recorder`中，我们需要选择两个请求。第一个是 login.php 的 200 OK(打嗝历史记录中的请求 385)和 login.php 的 302 Found(打嗝历史记录中的请求 382)。

> **第一个选择的请求**是当用户退出 DVWA 时，响应中出现登录表单以及 **CSRF 值**。
> 第二个**选择请求**用于登录用户，它包含用户凭证以及请求中的 **CSRF 令牌**。
> 第一个登录请求将值连同**用户值**中的 CSRF 令牌一起发送给第二个响应。

![](img/4d0e649aabb978bf2b3db7ea25e71a11.png)

5.点击确定，然后在`configure item`。

![](img/378e7c5988975f41d58898169600c945.png)

6.在`Define Custom Parameter`中，选择带有 **302 发现**响应的 login.php，并从响应体中选择数值。

`value='97acfb61671983c229de45434931fcea'`

![](img/529d83fff2d3444ba9557b4f666b26c5.png)

7.然后点击 ok 并在`Macro Editor`中选择 **200 OK** 请求

![](img/f7fab468effc07bbe3ebf6732cb5009d.png)

8.然后选择状态为 **302 的 2 个请求，发现**。点击`Configure item`，在参数处理中选择`user_token`，然后选择`Derive from prior response`。单击确定。

> 这是获取的令牌值链接到第二个`**Macro**` **中的值的地方。**

![](img/a942cb4eda59f846bb400f22dee2edb8.png)

9.然后选择`Response 1`链接 **CSRF 值**，点击 **OK** 。

![](img/cfef9e5d97ddb7eeb447d24a608a497a.png)

10.然后更改`Macro Description`并再次检查选择的`requests`。最后，点击**确定**。

![](img/2b43a078ac78504075182707adeea817.png)

11.然后在`Session handling action editor`中选择下面给出的`**Update current request**`的两个值。

![](img/f279be37df125ab0195bbd1ca2ba6d89.png)

12.在`Session handling rule editor`中，相应地在`Tools Scope`和`URL Scope`中选择范围。

![](img/ff65e98169f2d5f67784fece930e7f80.png)

13.现在我们必须**创建一个会话验证规则**，当会话无效时将触发**。
再次点击`Add`创建新规则。**

![](img/ac4d4dec209503ed18a0fdf0d5e7fb0a.png)

14.然后`Session handling action editor`打开，点击`Add`并在`Rule Actions`中选择`Check session is valid`。

![](img/6619ea49057e443802b7f94b47e55640.png)

15.在`Location(s)`中选择`URL of redirection target`。用于`Look for expression:`进入**login.php**

> 当用户试图访问经过身份验证的 URL 时，此 login.php 基于位置标头

![](img/9c5bdc68c3a4a12b0f7a526bf54d558f.png)

15.第一个`check session is valid`是验证用户试图访问的已验证的 URL，如果用户没有登录，那么它检查并验证用户。

> 现在我们需要创建另一个`check session is valid`,以确保当用户试图访问 login.php 时能够登录。在 localhost/login.php 的情况下，页面发送登录表单，表单正文中包含用户名和密码。这主要发生在用户注销会话时。

![](img/d4bff92c49df30b365ccfac892620f98.png)

16.让我们编辑`Session handling rule editor`并在`Rule Actions`中选择`check session is valid`。

![](img/56bd297abcaa754e4ee38f58e7c322dc.png)

17.在`Session handling action editor`中选择`Run macro`，然后在`Location(s)`中选择`Response body`。并在`Look for expression:`中填入用户名(**因为响应体包含用户名和密码表单，带有 user_token 值**)。

![](img/daa12490a7777de632e86a1a24c7a230.png)

18.我们来回顾一下宏。
a) **第一个宏**用于自动登录，它从另一个宏中获取 **CSRF 值**。
b) **第二个宏**检查所有会话无效的已验证页面。第三个宏检查响应体是否包含用户名，然后通过调用**自动登录宏**来验证用户。

![](img/226280a470a9c48d5332b0e99270b9d2.png)

18.在`Session handling rule editor`中切换到范围选项卡，然后相应地选择范围和`URL Scope`。

![](img/70f17abfb5fb00c40da39aa0c1aba746.png)

19.让我们在重复的。禁用`Session Handling Rules`。

![](img/0592d068a69f7a10126f206364301f0c.png)

20.现在访问经过认证的 URL([http://localhost/vulnerabilities/sqli/](http://localhost/vulnerabilities/sqli/))，得到的响应是`302 Found`。

![](img/c0dc02c847923889a74251d9e796ffa8.png)

21.现在启用会话处理规则并检查 repeater 中的请求。

![](img/cfc6f5a92da700462d74dbbdd87bd12f.png)

22.发送请求并检查响应。在呈现页面时，我们可以看到用户已自动登录。

![](img/df36eac51dd35cd4b92313b0396b2183.png)

**这就完成了打嗝套件自动化的第三部分**。在另一个部分中，可以从包含 user_token 的响应主体直接向请求头添加自定义头。**第 1 和第 3 教程的唯一区别是无效会话的会话验证。**

> [虽然在 Burp Suite 中不需要自定义标题，但在应用程序添加自定义标题的现实世界中，使用 next blog 可以添加自定义标题。为此，我开发了自定义打嗝扩展，可以通过宏直接调用，并从之前的宏中获取响应，并直接为自定义头赋值。](/automating-burp-suite-4-understanding-and-customising-custom-header-from-response-via-burp-macro-214332dda012)

[![](img/4bc5de35955c00939383a18fb66b41d8.png)](http://buymeacoffee.com/justmorpheus)