# 从 InfoDisc 到 RCE 再到 SOP 旁路

> 原文：<https://infosecwriteups.com/from-infodisc-to-rce-to-sop-bypass-2d956b58796e?source=collection_archive---------1----------------------->

你好渗透测试人员！

今天，
我将用 2 PBBP…
写下我的发现，从信息泄露
到远程代码执行
到同源策略绕过
——————
姑且称之为 a.com&b.com
a . com:
在运行 Dirb 的时候，
我发现了 crossdomain.xml 文件。
所以我在 b.com/crossdomain.xml 查了一下
我看到 a.com 允许从 b.com
<allow-access-from domain = " * . b . com "/>的任何子域访问

b.com:
然后我运行 sublist3r 来查找 b.com 子域..
(。/sublist 3r . py-d b.com)
几分钟后……
我拿到了 b.com 子域列表。

然后我跑目击者截图为 b.com 子域名列表
(。/witness . py—prepend-https—headless-f b-sub domains . txt)

Braaah :"D
我注意到一个子域有
(Apache Tomcat/6.0.35)
然后我向我最好的朋友(Google)询问了这个版本..

在 Google 搜索结果后:
我注意到这个版本受到了这个 CVE 的影响-2016–8735(RCE)
并且我知道 Jexboss 可以利用这个漏洞..

所以我运行 Jexboss 来利用这个漏洞
(。/jexboss.py -h)
(。/jex boss . py-u[http://developers.b.com)](http://developers.b.com))

运行了这个漏洞..过了一会儿……:D 完成了
有效载荷上传成功
反向外壳工作正常:D

我在 developers.b.com 得到了 RCE
-现在我可以在这个子域&上上传恶意的完全访问。😂
-也可以绕过 also 的 SOP😎

结果:
1。向 b.com PBBP 举报 RCE。
2。据报道，同源政策可能绕过 a.com PBBP。

-10 天内修复了 2 个问题
-我获得了…谢谢:3
——他们不会给赏金带来坏运气..但是🖐️一点问题都没有😄

经验教训:
-第一步总是收集目标的信息。
-检查每一个请求。
-检查服务器上的所有文件。
。/././././结尾。/././././

请注意:
-这是我第一次写文章，所以我在等你的反馈😊
#通用