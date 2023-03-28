# PortSwigger Web Security Academy 实验室:SQL 注入联盟攻击，发现包含文本的列

> 原文：<https://infosecwriteups.com/portswigger-web-security-academy-lab-sql-injection-union-attack-finding-a-column-containing-text-f67728a4240a?source=collection_archive---------1----------------------->

在本实验中，我们将首先确定使用 SQL 注入漏洞的列数。然后，我们将确定从哪一列提取的数据写入哪个字段。如果需要，我们可以安排用 UNION 进行的 SQL 查询来收集关于数据库的信息。

![](img/68d7b2175cf7b28a810f272c95125acd.png)

+UNION+SELECT+NULL —

+UNION+SELECT+NULL，NULL —

通过不断增加上面的有效负载语句，我们添加空语句，直到屏幕上没有任何语句，或者直到我们没有收到错误消息。

![](img/f11b7531a8b171eee015a4dbef1c2e92.png)![](img/5461ba65c0e0bc4e00aaf85f58f02393.png)![](img/f5a6640e1aea1e57eb1ae6ad6c5bb5ca.png)

现在，让我们写一个字符串来确定哪一列的数据被写入哪个字段，而不是 NULL。已经确定第二列中的表达式打印在屏幕上。

![](img/067d560c9c926e7be396ce983f40758b.png)![](img/3085dfc5ede6d4f0fcc06da8087f5183.png)![](img/6a667d6fefcd642ca1121ff906defe98.png)

我们确保打印出的是表达式“HPkriz”，而不是我写的表达式“anilyelken”。这样，实验就完成了。

![](img/2e1962307d533411176c9883aefb62bf.png)

来自 Infosec 的报道:Infosec 上每天都会出现很多难以跟上的内容。 [***加入我们的每周简讯***](https://weekly.infosecwriteups.com/) *以 5 篇文章、4 个线程、3 个视频、2 个 Github Repos 和工具以及 1 个工作提醒的形式免费获取所有最新的 Infosec 趋势！*