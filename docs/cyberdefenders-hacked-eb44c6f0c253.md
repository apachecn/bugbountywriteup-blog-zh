# 网络卫士|被黑

> 原文：<https://infosecwriteups.com/cyberdefenders-hacked-eb44c6f0c253?source=collection_archive---------0----------------------->

## 法医报告

![](img/6992c1b2b448507a32d8e4080cb7f0dc.png)

被黑——CyberDefenders.org

今天，我们被叫去分析一个受损的 Linux 网络服务器。弄清楚威胁参与者是如何获得访问权限的，对系统进行了哪些修改，以及使用了哪些持久的技术。(如后门、用户、会话等)。

**那么，我们来分析一下服务器..**

## Windows 工具

*   FTK 成像仪->[https://access data . com/products-services/forensic-toolkit-ftk/ftki manager](https://accessdata.com/products-services/forensic-toolkit-ftk/ftkimager)
*   r-studio->[https://www.r-studio.com/](https://www.r-studio.com/)
*   Linux 神器->[https://tho-le . medium . com/Linux-forensics-some-useful-Artifacts-74497 dca1ab 2](https://tho-le.medium.com/linux-forensics-some-useful-artifacts-74497dca1ab2)

第一件事是导入 web 服务器。E01 到 FTK 成像仪，所以我们能够从服务器上看到文件和文件夹。

## 回答问题

1.什么是系统时区？

> 欧洲/布鲁塞尔

![](img/df6bc6e877f830919f83f549842820bf.png)

2.谁是最后一个登录系统的用户？

> 邮件

![](img/0d9424acaec92f46ea83e991cc8f9e4f.png)

3.用户“邮件”连接的源端口是什么？

> 57708

![](img/042fe497a8ae83b5c1af4f783129ab1c.png)

4.用户“邮件”的最后一次会话持续了多长时间？(仅分钟)

> 1

![](img/d2ee5f54f1da22a79e3475bf2b6cacf5.png)

5.最后一个用户使用哪个服务器服务登录系统？

> sshd

![](img/fe5198fa2db42e2bc1029b6146423eb5.png)

6.对目标机器执行了什么类型的身份验证攻击？

> 暴力

![](img/426cb9ea18d16566a2368051cd0d9591.png)

7.“/var/log/lastlog”文件中列出了多少个 IP 地址？

> 2

![](img/dc47ed68a9e43805f9bd12ff040706f1.png)

8.有多少用户有一个登录 shell？

> 5

用`/bin/bash`表示的登录外壳

![](img/d0aa8f2b47dc133c18efacab4e970f28.png)

9.邮件用户的密码是什么？

> 辩论术

我有影子和密码，所以我用开膛手约翰破解了它。

![](img/9ba11270f2bb0c61421905d243ae471b.png)

10.攻击者创建了哪个用户帐户？

> 服务器端编程语言（Professional Hypertext Preprocessor 的缩写）

![](img/7467fd2d50866578d290ef1013cadb1d.png)

11.机器上有多少用户组？

> 58

![](img/7ca73de13e664e5549afff0ed5858134.png)

12.有多少用户可以访问 sudo？

> 2

sudo 访问可以在 sudo 组中指示。

![](img/44f4c02b37b099affb036203a938e5f2.png)

13.PHP 用户的主目录是什么？

> /usr/php

![](img/f2441933b4dfac3ea359f0e554c53135.png)

14.攻击者使用了什么命令来获得 root 权限？(答案包含两个空格)。

> sudo su -

![](img/24591e9913818fc14e0939070f3e32f8.png)

15.用户“root”删除了哪个文件？

> 37292.c

![](img/af01ecc732042c9813d22d5f0f1dd61e.png)

16.恢复删除的文件，打开它，并提取漏洞作者的名字。

> 反叛者

首先，将映像挂载到某个分区，并打开 r-studio 来恢复文件。

![](img/8ba6d634aa67099dc04ddab048e659f2.png)

然后打开该文件:

![](img/8f57fb5be76fedbb9a7acebb8d1b59f7.png)

17.机器上安装了什么内容管理系统(CMS)？

> drupal

![](img/d2d9f4bbfde8cf1cdd44ad0bd06c2be9.png)

18.机器上安装的 CMS 版本是什么？

> 7.26

![](img/5e69796c46c053796bdb53be4d2e9ab2.png)

因为 apache.conf 是旧文件，而我的 hypotesis 是 Drupal 安装时创建的这个文件。然后我在他们网站上搜索 Drupal 版本。

![](img/3ed2cf01c6a3b5b7b91b46d59def0939.png)

19.哪个端口在监听接收攻击者的反向 shell？

> 4444

![](img/5ef403c1204aeca1676f1d5649349f5e.png)

你看到 base64 代码了吗？

![](img/3a234156872ceefe8195893513ed0b4f.png)

## 结论

取证的关键是了解藏物并像攻击者一样思考。
所以，多学习多练习，会更好的提升自己。感谢阅读。

# 🔈 🔈Infosec Writeups 正在组织其首次虚拟会议和网络活动。如果你对信息安全感兴趣，这是最酷的地方，有 16 个令人难以置信的演讲者和 10 多个小时充满力量的讨论会议。[查看更多详情并在此注册。](https://iwcon.live/)

[](https://iwcon.live/) [## IWCon2022 - Infosec 书面报告虚拟会议

### 与世界上最优秀的信息安全专家建立联系。了解网络安全专家如何取得成功。将新技能添加到您的…

iwcon.live](https://iwcon.live/)