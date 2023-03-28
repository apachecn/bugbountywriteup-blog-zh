# 颤振编程和安全漏洞

> 原文：<https://infosecwriteups.com/flutter-programming-and-security-vulnerabilities-b2b01d9f5eb1?source=collection_archive---------2----------------------->

![](img/5e3b7c51dbcdfb82aa23f3f861e1f5ce.png)

摆动

在这篇文章中，我将告诉你关于 Flutter 编程语言和安全漏洞。Flutter 是 Google 创建的开源移动应用开发框架。它用于从单个代码库为移动、web 和桌面构建本机编译的应用程序。

与任何软件一样，保持 Flutter 最新是很重要的，以便解决任何可能被发现的安全漏洞。Flutter 团队定期发布包含安全修复的更新，因此建议定期更新到 Flutter 的最新版本。

开发人员可以采取几个步骤来确保他们的 Flutter 应用程序的安全性:

1.  **使用安全的网络连接:**使用 HTTPS 进行网络连接，防止中间人攻击。
2.  **安全地存储敏感数据:**使用安全的存储机制，比如 Flutter 安全存储插件，来存储敏感数据，比如密码和访问令牌。
3.  **使用安全认证:**实现安全认证机制，比如 OAuth，以防止对应用程序的未授权访问。
4.  **使用安全数据传输:**使用加密来保护应用程序和服务器之间传输的数据。
5.  **定期更新依赖关系:**保持依赖关系(如包和插件)最新，以便利用可能已经发布的任何安全补丁。

> **使用安全网络连接:**

要在 Flutter 应用程序中使用 HTTPS 进行网络连接，您可以使用`http`包并在 URL 中指定`https`方案:

```
import 'package:http/http.dart' as http;

String url = 'https://example.com/api/endpoint';

http.Response response = await http.get(url);
if (response.statusCode == 200) {
  // Handle successful response
} else {
  // Handle error
}
```

> **安全存储敏感数据:**

要在 Flutter 应用程序中安全地存储密码和访问令牌等敏感数据，可以使用`flutter_secure_storage`包:

```
import 'package:flutter_secure_storage/flutter_secure_storage.dart';

final storage = new FlutterSecureStorage();

// Store a value
await storage.write(key: 'password', value: 'my_password');

// Read a value
String password = await storage.read(key: 'password');

// Delete a value
await storage.delete(key: 'password');
```

> **使用安全认证:**

要在 Flutter 应用程序中实现 OAuth，您可以使用`flutter_oauth`包:

```
import 'package:flutter_oauth/flutter_oauth.dart';

final OAuth oauth = new OAuth();

// Get a request token
String requestToken = await oauth.getRequestToken();

// Get an access token
String accessToken = await oauth.getAccessToken(requestToken);

// Use the access token to authenticate API requests
```

> **使用安全数据传输:**

要加密应用程序和服务器之间传输的数据，您可以使用像`pointycastle`这样的包:

```
import 'package:pointycastle/pointycastle.dart';

// Generate a key pair
final keyPair = new KeyPair.generateKeyPair(new RSAKeyGenerator().parameters);

// Encrypt the data
final plainText = 'Hello, world!';
final cipher = new RSAEngine()
  ..init(true, PublicKeyParameter(keyPair.public));
final encrypted = cipher.process(plainText.codeUnits);

// Decrypt the data
final decipher = new RSAEngine()
  ..init(false, PrivateKeyParameter(keyPair.private));
final decrypted = decipher.process(encrypted);

print(String.fromCharCodes(decrypted)); // prints "Hello, world!"
```

此外，我们建议您查看 [**OWASP Top**](https://owasp.org/www-project-mobile-top-10/) 和 SANS Top 25 列表中的安全问题。

![](img/c1298b3f710b108802d7c52cbc72804d.png)

杰克·斯派洛船长——加勒比海盗

在这篇文章中，我告诉了你关于 flutter 编程语言和安全漏洞，在我的下一篇文章中再见，保重。

## 来自 Infosec 的报道:Infosec 每天都有很多内容，很难跟上。[加入我们的每周简讯](https://weekly.infosecwriteups.com/)以 5 篇文章、4 条线索、3 个视频、2 个 GitHub Repos 和工具以及 1 个工作提醒的形式免费获取所有最新的 Infosec 趋势！