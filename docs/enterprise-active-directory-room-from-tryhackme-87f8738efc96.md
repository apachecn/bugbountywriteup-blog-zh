# 企业:来自 TryHackMe 的活动目录室

> 原文：<https://infosecwriteups.com/enterprise-active-directory-room-from-tryhackme-87f8738efc96?source=collection_archive---------3----------------------->

你刚刚登陆了一个内部网络。你扫描网络，只有域控制器…

从标语来看，我们知道将会有一些我们必须处理的活动目录。

初始枚举:

让我们从 OG nmap 扫描开始，你可以使用任何你喜欢的东西。

命令:

```
sudo nmap -sC -sV -p- --min-rate 2000 <IP-ADRESS> | tee nmap.md
```

解释:

*   -sC:在所有打开的端口上执行默认脚本扫描。
*   -sV:在所有打开的端口上执行版本扫描。
*   -p-:扫描 1- 65535 范围内的所有端口
*   —最小速率:增加速率

![](img/1301ea82bbfc2917d308f0ad8728fbc4.png)

NMAP 扫描

哇哦。信息太多了，让我们一条一条地分解。

ladp 给了我们“企业”这个域名。THM”。让我们将它添加到/etc/hosts 文件中。

我们在端口 80 有一个网络服务器。让我们参观它。

![](img/db58d46cebd5aa216ea306c39aa0d842.png)

登录页面。

我们可以尝试找到隐藏的目录。

目录扫描:

![](img/6039a80578e06e5a31056c1ae787904f.png)

FFUF

我们去看看，也许能找到些什么。

![](img/68af37de4a99b4c6446c54c9e40e2b5e.png)

Robots.txt

嗯，我没想到。

至此，我知道 web 服务器不是正确的路径。让我们列举更多的服务。

我们在目标上打开了 Smb 端口。让我们试着列举一下，也许我们能找到一些份额。

**SMB 枚举:**

我们可以尝试许多工具，例如

*   SMB 客户端
*   Smbmap
*   Enum4linux
*   Crackmapexec

![](img/07392ec14db15fab049731741aec85b0.png)

分享

有一个有趣的共享名为用户让我们连接到它。

![](img/250a81006581b02a92a94e0e7beab278.png)

分享

任何目录中都没有什么有用的东西。

![](img/5de8ce4320b4d2559d3be7ca9e0a44c4.png)

Enum4linux

Enum4linux 也没有给我们任何其他有用的信息。

让我们试试 crackmapexec

我们得到了目标的域名、名称和操作系统版本。

![](img/375eed59ebee04813cd58fb00548d9b6.png)

再次拒绝访问

我们没有获得对可写共享的任何访问权限，让我们尝试列举其他服务

让我们试着用 kerbrute 获得有效的用户名:

![](img/85ae85f8fce1221df20a328f9ae201e4.png)

克尔布鲁特

回头看看 nmap，我们看到 LDAP 是开放的，我们可以。

**LDAP 枚举:**

我们可以针对端口 389 运行一些 nmap 脚本来获得更多信息。

![](img/816e083cb3cb9e498d95acd6d9a9b3ee.png)

Nmap 脚本扫描

将找到的域添加到/etc/hosts 文件中。

此时，我跌到了谷底。但是让我们试着看看我们错过了什么。

回头看 nmap 扫描，我看到另一个端口运行 web 服务器。

![](img/739440af98d80a13ff4227f8619e77bf.png)

端口 7990 上的 HTTP:

让我们通过端口 7990 访问网络:

![](img/cd160ecce1cc6a43cdb4fa958c81ad4e.png)

ATLASSIAN 登录页面

它需要一封电子邮件才能继续。有一个提醒 Enterprise-THM 的员工，他们正在转移到 Github。这可能是一个提示。让我们在 Github 上搜索 Enterprise-THM。

老实说，一开始我并没有注意这张纸条。但迟做总比不做好。

![](img/72a4ec7df6bddfef183afb9fbc3f6abf.png)

OSINT

有趣的是，Github 上有一个 Enterprise-THM 知识库。我们可以在这个仓库里找到一些秘密。

![](img/b5171b10707f6efd3c069a522139f2a2.png)

Github 回购

我们可以在这个回购里看到一个员工。

![](img/29e8e2f63d106b7abe5296e7ffebaced.png)

员工帐户

进一步查看后，我发现了一个 PowerShell 脚本。在之前的脚本历史中，我找到了一些凭据。

![](img/e3c09d6903b50cea84c35cc267d41d83.png)

Creds

让我们把那记下来。

我们现在有了用户 Nik 的凭据，让我们看看该用户是否有任何关联的 SPN 票证。

# Kerberoasting 认证:

Kerberoasting 认证是一种利用后攻击技术，它试图泄露 Active Directory 服务帐户(AD)的密码。在这种攻击中，攻击者伪装成具有服务主体名称(SPN)的帐户用户，请求具有加密密码或 Kerberos 的票据。

**Impacket GetUserSPN.py:**

我们可以使用 impcket 的 GetUserSPN.py 来 kerbroast 用户 Nik。

语法:

```
GetUserSPNs.py <domain_name>/<domain_user>:<domain_user_password>   -request -outputfile <output_TGSs_file>
```

![](img/b9930fa9c3bfb5934caf771983bf91f6.png)

我们可以看到 Nik 有一个名为 bitbucket 的服务主体集，我们在共享中也看到了它。所以让我们请求它。

![](img/cd86b1cab7aa3c6827329400d90cd9ec.png)

让我们使用 hashcat 或 john 来破解 hash。

语法:

```
hashcat -m 13100 --force <TGSs_file><passwords_file>john --format=krb5tgs --wordlist=<passwords_file><AS_REP_responses_file>
```

![](img/f33db990664c98841e0e684d15717a88.png)

哈希卡特

现在我们得到了密码，让我们尝试通过 Evil 登录-winrm == >失败

![](img/6d873745a53703ffecb9e088ba7eeaba.png)

邪恶-winrm

让我们试试 RDP。

我们可以使用 xfreerdp 来获得远程访问:

```
xfreerdp /v:LAB.ENTERPRISE.THM /u:bitbucket /p:{PASSWORD}
```

![](img/5cd5ab3a6537a2a82204828aa2022be9.png)

RDP

我们可以在桌面上得到 user.txt。

**权限提升:**

让我们看看盒子上的 winPEAS.bat。

![](img/72f51b6f954c342f468b2fe30aef4c3f.png)

winPEAS.bat

我们可以看到 zerotireoneservice 容易受到**未引用路径服务的攻击。**

![](img/531de0a921aadf8cce9142c775edde26.png)

Winpeas 结果

让我们生成 msfvenom 有效载荷。我们将使用的有效载荷是

```
windows/meterpreter/reverse_tcp
```

![](img/1e1b2f1ab41d040333b4203806aad4c1.png)

有效载荷

将您的目录更改为“C:\Program Files (x86)\Zero Tier”并传输您的有效负载。

确保您的 shell 名称为 Zero.exe。

另一方面，也要设置好你的听众。

![](img/de718911201f83b4b1e0712a6d224045.png)

现在只需停止并启动服务。

```
Stop-Service -Name "zerotieroneservice"To start The Service Start-Service -Name "zerotieroneservice"
```

我们会得到这个内脏的外壳，它会在几秒钟内死去。

![](img/96094f09d8a928fb9eb3733151be3512.png)

壳

为了防止这种情况，我们需要迁移流程。

试了几次后，winlogon.exe 的效果最好。

![](img/b25693d5846d3bf3fc2509017d2a7826.png)

根深蒂固的

这是一个很棒的盒子。希望你们都喜欢。

我将发布更多与活动目录相关的房间。

所以在那之前。

*来自 Infosec 的报道:Infosec 上每天都会出现很多难以跟上的内容。* [***加入我们的每周简讯***](https://weekly.infosecwriteups.com/) *以 5 篇文章、4 个线程、3 个视频、2 个 Github Repos 和工具以及 1 个工作提醒的形式免费获取所有最新的 Infosec 趋势！*