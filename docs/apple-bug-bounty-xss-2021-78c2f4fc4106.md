# 苹果虫赏金特写 XSS(2021)

> 原文：<https://infosecwriteups.com/apple-bug-bounty-xss-2021-78c2f4fc4106?source=collection_archive---------1----------------------->

# 关于我:

[](https://www.linkedin.com/in/takashi-suzuki-whitehacker/) [## 铃木隆-安全研究员- フリーランス | LinkedIn

### 在全球最大的职业社区 LinkedIn 上查看铃木隆的个人资料。Takashi 有 3 份工作列在…

www.linkedin.com](https://www.linkedin.com/in/takashi-suzuki-whitehacker/) 

[https://hackerone.com/kamikaze?type=user](https://hackerone.com/kamikaze?type=user)

# 枚举:

从 censys.io 获取 apple 的可访问主机

搜索查询:17.0.0.0/8 和 443.https.get.status_code: 200

# 截图:

Censys-CLI 和 Aquatone

ip 抓取工具:【https://github.com/censys/censys-python】T2

截图工具:【https://github.com/michenriksen/aquatone】T4

# 终端命令:

1.  从 censys CLI 抓取可访问的主机

censys search -q "17.0.0.0/8 和 443 . https . get . status _ code:\ " 200 \ " "-query _ type IP v4-fields IP protocols-max-pages 15-f JSON-o apple

2.Grep ip 地址

grep-o“[0–9]\ { 1，3\}”。[0–9]\{1,3\}\.[0–9]\{1,3\}\.[0–9]\ { 1，3\} '苹果> > IP-苹果

3.为 ip 地址添加“https ”,以便用于 Aquatone

sed 's/^/https:\/\//' IP-apple > > http-apple

4.截图

猫 http-apple |。/aquatone-ports 443-http-time out 9000-screen-time out 90000-out apple

我发现了一个易受 XSS 攻击的网站。

# 网站:

https://apple.channel.support

# 复制步骤:

1.创建票证

2.上传带有 XSS 有效负载的 SVG 图像作为回复

3.当受害者从移动设备查看攻击者的 SVG 图像时，XSS 触发

# 无线一键通

# 时间线:

报告日期:2021 年 2 月 16 日

修正并询问如何进入名人堂页面:31/03/2021