# 只是 OSCP 的另一个准备技巧

> 原文：<https://infosecwriteups.com/just-another-oscp-preparation-tips-f22410006674?source=collection_archive---------0----------------------->

![](img/d61a82808966186d8d9cf913f2162dd1.png)

我上个月通过了 OSCP 考试。

下面是一些作为初学者接近 OSCP 的回顾和提示，因为我发现有很多人在谈论他们在通过考试之前失败了多少次。事实上，我也失败过一次。

希望你能一次通过。

# 技巧

## 全面发展

如果你像我一样从网络黑客开始你的黑客之旅(似乎很多人都是这样)，你肯定需要了解更多关于权限提升和 ssh、ftp、smtp、smb 等技巧的知识。

在 OSCP，你只需要非常基本的网络黑客技巧，但为了做一些有意义的事情，你必须能够枚举 smtp 用户，从 smb 获取 shell，滥用 suid 二进制文件获取 root 等。因为这个，我第一次尝试失败了。

一个练习的好地方是 HackTheBox。 [NetSecFocus Trophy Room](https://docs.google.com/spreadsheets/d/1dwSMIAPIam0PuRBkCiDI88pU3yzrqqHkDtBngUHNCw8/htmlview#) 是一个盒子列表，它确实帮助我准备了 OSCP 考试。从简单的盒子开始。

## 小心兔子洞

OSCP 不需要像 payloadAllTheThings 上那样非常聪明的把戏。对于单个漏洞，如果方法正确，您应该能够在几分钟内完成。例如，如果你找到一个目录遍历的参数，你将得到你想要的经典../../../../../../../ <secret files="">。如果你用%252e%252e%252f 之类的东西去试探它，你已经在兔子洞里了。</secret>

有时候你会看到一个软件有可利用的版本。当然，如果你在 exploit-db 上使用漏洞，你需要做一点修改，但是如果它在调试了很长时间后仍然不工作，那么这个软件可能会让你分心。

为了避免掉进兔子洞，您可以尝试对每台机器/每个开放端口/每种攻击类型设定时间表。如果我被困在一个小时，我会去找另一个目标机器，不管我觉得自己多么接近解决方案，我都必须继续前进。当你稍后回来时，你有更大的机会注意到你在兔子洞时错过了什么。

## 尽早安排考试

是的，这不是你的黑客技术，但非常重要。OSCP 考试预订已经满了，你必须在考试前 1-2 个月预订，以便有一个好的开始时间。如果你不得不在下午 3 点开始考试，你的大脑可能不会很好地工作。

# 资源

准备 OSCP 的好黑客 TheBox 机器列表:[https://docs . Google . com/spreadsheets/d/1 dwsmiapiam 0 purbkcidi 88 pu 3 yzrqhkdtbnguhncw 8/html view #](https://docs.google.com/spreadsheets/d/1dwSMIAPIam0PuRBkCiDI88pU3yzrqqHkDtBngUHNCw8/htmlview#)

特权升级诡计:[https://gtfobins.github.io/](https://gtfobins.github.io/)

许多聪明的小技巧——你可能在考试中不需要它们，但却有助于参考:[https://github.com/swisskyrepo/PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings)

一个自动扫描 nmap 端口并根据结果运行其他侦察工具的工具:【https://github.com/21y4d/nmapAutomator】T2