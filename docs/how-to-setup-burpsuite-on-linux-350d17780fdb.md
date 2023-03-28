# 如何在 Linux 上设置 BurpSuite

> 原文：<https://infosecwriteups.com/how-to-setup-burpsuite-on-linux-350d17780fdb?source=collection_archive---------0----------------------->

BurpSuite 是一个用于测试 Web 应用程序的集成平台，它允许我们拦截数据和 Web 应用程序，它用于教授/学习/练习/拦截在客户端和服务器之间传输的数据包，我们甚至可以通过 BurpSuite 进行暴力攻击。一旦你成为网络应用测试专家，你就可以购买高级版本。

![](img/090d3bc7c338c6a9711f1a530cb0b79e.png)

硬石膏

在这个教程中，我在 Firefox 中使用了 foxy 代理插件，如果你愿意，你可以使用任何代理，甚至可以设置手动代理。

## 步骤 1 :-打开 Firefox

![](img/20b3c49d8a86ddc2af04eae742888914.png)

火狐浏览器

## 步骤 2 :-安装福克西代理

FoxyProxy 是一个先进的代理管理工具，它完全取代了 Firefox 有限的代理功能。

```
Search for Foxy Proxy 
Click on Addon Link 
Install Foxy Proxy Standard after installing you can see it on the toolbar.
```

![](img/22c66cf8ddfc78eb6ab74e3557c8a73e.png)

福克西代理

![](img/0db19b8adb8e6c94b62a818e1cb635a5.png)

插件

![](img/8d9760f98452eb3445f5c6b20ee7cd79.png)

添加到扩展

![](img/2ab6f146229e5a9b7ffff4837ed76c33.png)

工具栏上的福克西代理

## 步骤 3 :-设置 Burpsuite

```
Open BurpSuite
Create Temporary Project by Clicking Next
Click Start Burp
Go to Proxy Tab
Go to Options subtab in Proxy Tab
Note down the IP Address and Port
```

![](img/f1833880348b837e4861d37ee905225c.png)

硬石膏

![](img/e39414462ef27fae0ca99a95bbd05fb8.png)

开始临时项目

![](img/bd8dfaa8dba8c2f01640d67a68d05af3.png)

开始打嗝

![](img/4111b6668f5e4280745e3fa8ab2f37c3.png)

代理选项卡

![](img/6ed2a125e836fa6757c62bc360f77941.png)

选项子选项卡

![](img/1c2758e6c2ca712296be7079ce121f62.png)

记下 IP 和端口

## 步骤 4 :-配置代理

```
Click on Foxy Proxy Icon on toolbar in Firefox
Click option
Click Add
Give Title
Set Proxy Type to 'HTTP'
Fill above IP Adress from Burpsuite in Ip Address section
Fill above Port from Burpsuite in Port section
Click Save
```

![](img/0c1f76a02592148ed42330318ea139c7.png)

福克西代理期权

![](img/8db6d851227259a96874026ad1c930f3.png)

添加代理

![](img/a2d0cebbf6060bc9c5642e68da434618.png)

填写必填字段

![](img/ef1bc6f3be13a9ab45cac0f385cebd6d.png)

保存代理

## 步骤 5 :-设置证书

```
In Browser Go to [http://{Above](/{Above) IP}:{Port}
Click on 'Get CA Certificate'
Save the file
Go to Settings
Go to Privacy and Security Tab
Go to Certificates Section
Click View Cerificate
Click on Import
Select the Certificate in popup
Click Ok
```

![](img/c352895db62f6be5d37c069fb5395ea0.png)

下载证书

![](img/4da49a1e39d571bb16621770ecd672ad.png)

证书

![](img/7ff068dad5d82f1d2d854a1343c3c097.png)

进口执照

![](img/0732b1f70a7fcac63667661644a6de35.png)

选择证书

![](img/50066ae9786fa4899f461dc9cb541ee6.png)

现在你可以测试它了

```
Click on Foxy Proxy Icon on toolbar
Select the profile with your given title
Go to BurpSuite
Go to Proxy tab
Go to Intercept subtab
Click on 'Intercept is off' to turn on intercept
Go to Browser
Search Target
Go back to Burpsuite and chek if there is any request
```

![](img/a2235c378e871424484a252d4dcedbc6.png)

选择代理

![](img/d5489cb302ad057b17c3074a494df977.png)

打开截取

![](img/927246e9aac859f48ff37181db874bae.png)

转到目标

![](img/3758dde48fd9d3b95466aec0402a4473.png)

参见请求

恭喜你！🎉您终于为 Web 应用程序测试设置了您的 Burpsuite。

![](img/bf84c4dcd7ce23e51f485a800758b786.png)

今日迷因

来自 Infosec 的报道:Infosec 上每天都会出现很多难以跟上的内容。 [***加入我们的每周简讯***](https://weekly.infosecwriteups.com/) *以 5 篇文章、4 个线程、3 个视频、2 个 Github Repos 和工具以及 1 个工作提醒的形式免费获取所有最新的 Infosec 趋势！*