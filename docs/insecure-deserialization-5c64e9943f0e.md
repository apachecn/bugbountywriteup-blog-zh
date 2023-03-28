# 不安全的反序列化😈🐝

> 原文：<https://infosecwriteups.com/insecure-deserialization-5c64e9943f0e?source=collection_archive---------5----------------------->

![](img/8aace66f37c97abd28b001ebe6c4a59b.png)

斯蒂芬·拉德福德在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

序列化是将对象从内部表示转换成字符或字节流的行为。序列化对象的表示应该独立于平台和语言。数据在应用程序中被序列化和反序列化，以**存储**或**传输**它。在 web 应用中， **JSON** 或 **XML** 经常被很多 API 和协议用于数据交换。像 PNG/GIF/JPEG/MPEG 这样的文件格式使用 XML 来存储元数据。YAML 在配置文件方面变得非常流行，例如在 [Cloudformation 模板](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-formats.html)或 [GitlabCI 配置文件](https://docs.gitlab.com/ee/ci/yaml/)中。

有些文件格式允许您做的不仅仅是(反)序列化基本数据类型。例如，假设您想要建立一个 CI 管道。您可能有一个执行单元测试的步骤，一个检查类型的步骤，一个林挺的步骤。所有这些步骤可能都需要安装相同的依赖项集。与其重复自己，不如使用**引用**。你一次定义一个字典，在很多地方复制。引用允许人快速地读、写和修改文件，而机器只是在多个地方有相同的值。

另一个强大的功能是包括外部实体。在最简单的情况下，这意味着您想要包含另一个文件。例如，您可能有一个想要在多个地方使用的日志记录配置。在更极端的情况下，外部实体可能不在本地文件中，而只能通过互联网获得。老实说，我不知道你为什么要这样。如果你知道，请留下你的推荐！

大多数序列化格式不够强大，无法表示您可以拥有的任意对象。这些格式的功能有所不同。有些人希望在兼容多种语言方面走得更远。作为一个潜在的副作用，它们可能允许**任意代码执行**。

# 为什么你应该关心

*   不安全的反序列化在 **OWASP 前 10 名** ( [来源](https://owasp.org/www-project-top-ten/2017/A8_2017-Insecure_Deserialization))中排在第 8 位🐝
*   2013 年:YAML 节点包([CVE-2013–4660](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2013-4660))允许远程代码执行。远程代码执行非常糟糕:人们可以获取您的数据，安装后门，关闭您的服务，删除或加密您的数据，使用您的服务进行加密挖掘，潜在地损害您的硬件。
*   2014 年:安卓< 5.0 an insecure deserialization can result in arbitrary code execution ([CVE-2014–7911](https://nvd.nist.gov/vuln/detail/CVE-2014-7911)
*   2015 年:安卓< 5.1.1 allows arbitrary code execution ([CVE-2015–3837](https://nvd.nist.gov/vuln/detail/CVE-2015-3837)
*   2015 年:ArcGIS 允许任意代码执行([CVE-2015–2002](https://nvd.nist.gov/vuln/detail/CVE-2015-2002))
*   2015: [一个类统治一切:Android 中的 0 天反序列化漏洞](https://www.usenix.org/system/files/conference/woot15/woot15-paper-peles.pdf)作者 Or Peles，Roee Hay 引用[CVE-2015–3837](https://nvd.nist.gov/vuln/detail/CVE-2015-3837)
*   2019 年:Kubernetes 易受十亿次笑 DOS 攻击([CVE-2019–11253](https://nvd.nist.gov/vuln/detail/CVE-2019-11253))
*   2020 年:typo 3([CVE-2020–11067](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-11067))，IBM QRadar([CVE-2020–4280](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-4280))允许远程代码执行。
*   2020 年:Apache Tomcat 允许远程执行代码([CVE-2020–9484](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-9484))

# 反序列化攻击是如何工作的？

实际上有许多反序列化攻击。对它们进行分组的一种方式是按照文件格式，例如 YAML、XML、Python pickle 文件等等。另一种方式是通过攻击者想要达到的目标，例如任意代码执行或拒绝服务(DOS)。

问题是这些文件格式太强大了。它们要么直接允许代码执行，要么允许创建对文件系统的引用或对文档中元素的引用。

## 攻击 YAML 解串器

拿这个`example.yaml`文件来说:

```
!!python/object/apply:os.systemargs: ['cat /etc/passwd']
```

并执行以下 Python 代码:

```
import yaml  # pip install pyyaml is requiredwith open("example.yaml") as fp:
    data = fp.read()
yaml.unsafe_load(data)
```

这将打印出`/etc/passwd`的内容。您还可以删除系统上的任何(或所有)文件，发送 web 请求(例如带有密码文件的内容)，下载并执行软件(例如 rootkit/后门)。这可能是最坏的情况了。

如果你想了解更多关于 YAML 的特色，请阅读:

[](https://levelup.gitconnected.com/6-yaml-features-most-programmers-dont-know-164762343af3) [## 6 大多数程序员不知道的 YAML 特性

### 提升你的 YAML 知识，写更干净的 YAML 文件

levelup.gitconnected.com](https://levelup.gitconnected.com/6-yaml-features-most-programmers-dont-know-164762343af3) 

## 攻击 XML 反序列化

XML 允许引用外部实体，如文件(如`/etc/passwd`)或网站。如果你想了解更多为什么这是一个问题，请阅读我关于 XXE 袭击的文章

[](https://medium.com/faun/xxe-attacks-750e91448e8f) [## XXE 袭击😈

### PDF、Excel、SVG、电子书——都使用 XML。他们可能很脆弱。

medium.com](https://medium.com/faun/xxe-attacks-750e91448e8f) 

另一个可能的攻击媒介是在十亿次攻击中使用 XML 的引用功能:

[](https://medium.com/bugbountywriteup/dos-via-a-billion-laughs-9a79be96e139) [## 通过十亿次大笑😈

### 通过重复引用消耗任意多的 RAM

medium.com](https://medium.com/bugbountywriteup/dos-via-a-billion-laughs-9a79be96e139) 

## 攻击 Pickle 反序列化

Marco Slaviero 在他的论文“ [Sour Pickles](https://media.blackhat.com/bh-us-11/Slaviero/BH_US_11_Slaviero_Sour_Pickles_WP.pdf) ”中指出，pickle 文件的反序列化允许任意代码执行。Charles Menguy 在一个类似的例子中很好地总结了这一点:

```
import pickle
pickle.loads(b"cos\nsystem\n(S'cat /etc/passwd'\ntR.")
```

# 如何防御反序列化攻击？

你几乎总是可以做的两个措施:

*   **最小特权原则**:用尽可能少的特权运行你的代码。您确实不需要 root 权限。根据您的偏执程度，您可以创建一个专门的用户来执行反序列化。您可以取消该用户使用网络的权利。
*   **纵深防御**:确保每个组件都采取可能的安全措施。

对于某些格式，您可以告诉反序列化程序忽略它的某些功能:

*   **PyYAML** :使用`yaml.safe_load`功能。在某个时候，他们改变了界面，使`yaml.load`指向`yaml.safe_load`。还可以用`yaml.unsafe_load`。我喜欢他们在函数调用中包含“不安全”。这表明有些事情可能是危险的。
*   **XML** :对于 Python，有`[defusedxml](https://pypi.org/project/defusedxml)`将 Python 的各种 XML 解析器设置为安全默认值，防止 [XEE](https://medium.com/faun/xxe-attacks-750e91448e8f) 、十亿次大笑攻击和二次爆发。

对于 pickle 等其他格式，您只需确保您的输入不会造成伤害。

# 下一步是什么？

在这个关于应用安全(AppSec)的系列文章中，我们已经解释了攻击者的一些技术😈以及防守队员的技术😇：

*   第 1 部分: [SQL 注入](https://medium.com/faun/sql-injections-e8bc9a14c95)😈
*   第二部:[不要泄露秘密](https://levelup.gitconnected.com/leaking-secrets-240a3484cb80)😇
*   第 3 部分:[跨站脚本(XSS)](https://levelup.gitconnected.com/cross-site-scripting-xss-fd374ce71b2f) 😈
*   第 4 部分:[密码哈希](https://levelup.gitconnected.com/password-hashing-eb3b97684636)😇
*   第五部分: [ZIP 炸弹](https://medium.com/bugbountywriteup/zip-bombs-30337a1b0112)😈
*   第六部分:[验证码](https://medium.com/plain-and-simple/captcha-500991bd90a3)😇
*   第 7 部分:[电子邮件欺骗](https://medium.com/bugbountywriteup/email-spoofing-9da8d33406bf)😈
*   第 8 部分:[软件组成分析](https://medium.com/python-in-plain-english/software-composition-analysis-sca-7e573214a98e) (SCA)😇
*   第九部分: [XXE 袭击事件](https://medium.com/faun/xxe-attacks-750e91448e8f)😈
*   第十部分:[有效的访问控制](https://levelup.gitconnected.com/effective-access-control-331f883cb0ff)😇
*   第十一部分: [DOS via 十亿次大笑](https://medium.com/bugbountywriteup/dos-via-a-billion-laughs-9a79be96e139)😈
*   第十二部分:[全磁盘加密](https://medium.com/faun/full-disk-encryption-2090489f9760)😇
*   第 13 部分:不安全的反序列化😈

这即将到来:

*   CSRF😈
*   磁盘操作系统😈
*   凭据填充😈
*   密码劫持😈
*   单点登录😇
*   双因素认证😇
*   备份😇

如果您对更多关于 AppSec / InfoSec 的文章感兴趣，请告诉我！