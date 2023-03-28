# 利用 Gradle 插件通配符版本进行远程代码执行

> 原文：<https://infosecwriteups.com/leveraging-gradle-plugin-wildcard-versions-for-remote-code-execution-24e15112c432?source=collection_archive---------0----------------------->

![](img/204a777e91fbcc0bbb8ba1c94bcf27d9.png)

## 漏洞允许 Gradle 插件门户上的任何 Gradle 插件的工件坐标被恶意行为者劫持。

**责任披露:**2018 年 9 月 6 日
**漏洞修补:**2018 年 10 月 18 日

该漏洞源于发布带有组名的工件的能力，而您的 Gradle 帐户不应该能够发布这些工件。

# TL；速度三角形定位法(dead reckoning)

用户只需使用通配符依赖版本并使用 Gradle 插件门户库，就容易受到攻击。

这个插件的组 id 是`org.jlleitschuh.testing.security`(开头的`gradle.plugin`是 Gradle 加的)。
由于 Gradle 插件门户中的一个错误，恶意行为者可以将其插件的组 id 设置为与门户上已有的任何插件相同的组 id，并在他们不应该拥有的组下发布恶意插件。恶意参与者不能覆盖工件的现有版本，但是他们可以发布更新的版本。当用户使用通配符版本作为他们的依赖项时，Gradle 会在用户下次运行他们的构建时下载最新版本的插件(现在是恶意的)。然后插件用户将被 pwn。

# 发现

我发现这个问题是因为 spotbugs 团队还没有为他们的插件发布支持 Gradle 4.10 的补丁。我正在更新我的内部版本以使用 4.10，这阻碍了进度。因为我已经有了一个 Gradle Plugin Portal 账户，用于[其他插件](https://plugins.gradle.org/u/Jlleitschuh)我决定发布一个不同工件坐标下的 spotbugs 版本。我分叉了他们的存储库并修改了他们的`build.gradle`，这样我就可以成功地运行`publishPlugins`任务。
在这里可以找到[发布的结果](https://plugins.gradle.org/plugin/com.github.spotbugs.temporary)(这个工件已经被 Gradle 团队的一名成员移走，以防止它与 spotbugs 的未来版本发生冲突)。

Gradle Plugin Portal 提供的关于如何应用这个新发布的插件的示例代码片段如下所示:

引起我注意的是真正的 spotbugs 插件与我自己的插件有着完全相同的坐标。

*   真实:`gradle.plugin.com.github.spotbugs:spotbugs-gradle-plugin:1.6.2`
*   我的:`gradle.plugin.com.github.spotbugs:spotbugs-gradle-plugin:1.6.4`

意识到我所看到的，我开始在 [Gradle 社区 Slack 频道](https://join.slack.com/t/gradle-community/shared_invite/enQtNDE3MzAwNjkxMzY0LTYwMTk0MWUwN2FiMzIzOWM3MzBjYjMxNWYzMDE1NGIwOTJkMTQ2NDEzOGM2OWIzNmU1ZTk5MjVhYjFhMTI3MmE)上向 Gradle 插件门户领导 Eric Wendelin 报告这个问题。然而，当时我还没有完全明白其中的全部含义。

# 概念证明

这时，已经是凌晨 1 点了，但我为什么要现在停下来呢？我发现了一些东西！

以下测试是使用两个不同的 Gradle Plugin Portal 帐户完成的，这两个帐户都是我注册的。

我创建了以下良性插件的概念证明。把这个插件想象成互联网上任何作者发布的任何插件。这个插件给 Gradle 版本增加了一些方便的安全特性。

一个用户来了，认为这个插件会很有用，但是他们不想每次有新的更新时都要更新版本。所以他们决定像这样使用通配符版本。

他们将从 Gradle 插件门户下载的版本是 0.4.0。如果他们运行`./gradlew`，他们会在控制台上看到以下内容。

```
./gradlew> Configure project :
A security plugin...
```

现在，一个恶意的参与者出现了，他看到你在你的构建中使用了通配符版本，或者他们只是想劫持一些插件，看看会发生什么。利用这个漏洞，他们可以将自己的代码添加到插件中并发布。这就是恶意参与者将代码更改为的内容。

恶意参与者将他们的插件版本发布到 Gradle 插件门户。现在他们要做的就是等待。

下次我们的用户来运行`./gradlew`时，他们会收到这样的问候。

```
./gradlew
Download [https://plugins.gradle.org/m2/gradle/plugin/org/jlleitschuh/testing/security/gradle-testing/0.4.1/gradle-testing-0.4.1.pom](https://plugins.gradle.org/m2/gradle/plugin/org/jlleitschuh/testing/security/gradle-testing/0.4.0/gradle-testing-0.4.0.pom)> Configure project :
A security plugin. I'm malicious!...
```

用户刚刚被 pwn。

# 格拉德团队的回应

格雷尔团队对我的报告反应非常迅速。他们还告诉我，在我发现这个漏洞的一周前，他们已经被谷歌团队告知了这个漏洞。然而，谷歌的报告没有包括用户可以通过使用通配符版本被 pwn 的部分。Google 提供的漏洞版本要求用户更改应用于以下内容的插件。

```
apply(plugin = "org.jlleitschuh.testing.security-plugin.tmp")
```

# 修复

作为 [Gradle Plugin Portal 批准政策更新](https://blog.gradle.org/new-plugin-portal-acceptance-criteria)的一部分，针对此漏洞的修补程序已被发送到 Gradle Plugin Portal，尽管此漏洞未被提及。当被问及是否进行了审计以确保该安全漏洞不会被利用时，Eric Wendelin 回答说“是的，但是[我们]没有发现恶意行为的证据”。

# 外卖

像 Gradle Plugin Portal、Maven Central Repository 和 JFrog Artifactory 这样的工件服务器都是恶意参与者的完美目标。如果一个恶意的参与者可以劫持工件坐标，他们就可以在全球数百甚至数千台机器上执行任意代码。

## 如何保护自己

虽然这些提示不会保护您免受这种攻击，但是不做这些事情会使您或您的用户容易受到恶意依赖的攻击。

*   [总是通过 HTTPS 下载你的作品，而不是通过 HTTP](https://max.computer/blog/how-to-take-over-the-computer-of-any-java-or-clojure-or-scala-developer/) 。
*   考虑不要对构建依赖项使用通配符版本。
*   始终使用可信的工件服务器，如 Maven Central 和 Gradle 插件门户。
*   考虑使用带有[安全审计](https://www.sonatype.com/nexus-auditor)的公司神器镜像。

## 有待改进的领域

这些只是建议，可能有助于在未来减少此类攻击。

*   工件上的 GPG 签名，并迫使用户确认新版本不是由同一权威机构签名的。

# **在 Gradle 发现安全漏洞？**

安全漏洞应该报告给[security@gradle.com](mailto:security@gradle.com)。