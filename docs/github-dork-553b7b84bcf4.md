# Github 呆子

> 原文：<https://infosecwriteups.com/github-dork-553b7b84bcf4?source=collection_archive---------0----------------------->

## 使用 Github Dork 查找敏感信息

# 愿你平安

## 你好黑客们。我希望你一切都好。我是塔米姆·哈桑，来自孟加拉国🇧🇩的安全研究员和臭虫赏金猎人..

## 今天我要谈谈 GitHub 呆子。

![](img/ae5dde725d1cbcd3ea8e3c128ff5fa2a.png)

# 那么 Github 是什么呢？

**GitHub** 是超过 5600 万开发者共同塑造软件未来的地方。为开源社区做贡献，管理他们的 Git 库，做很多事情。

有时，存储库包含许多敏感信息，如 api、db 凭证、ftp 凭证等等。

您可以通过两种方式在 github 上找到敏感信息

1.  自动化
2.  指南

但是我们要用手动部分。

那么让我们开始吧……

![](img/4a66ba7852575f38ec87ebe4579042af.png)

***1#简单搜索***

首先，你应该只是简单地搜索你的目标，如 xyz.com，以了解他们的回购架构有多少回购，提交，以及找到什么样的语言之类的东西。

***2 #排序***

使用 ***排序:最近索引*** 查看最新的代码结果。不是 ***最佳匹配选项*** 因为旧证书现在可能不适用，尤其是 4-5 岁的，另一方面，公司也更喜欢最新的证书。

***3#呆子***

这是 github recon 的主要事情。在我的建议中，你可以从一些基本的呆子开始。

**下面是**[**@ El 3c tr 0 by3s**](http://@El3ctr0Byt3s)分享的一些基本呆瓜

API _ key
" API keys "
authorization _ bearer:
oauth
auth
authentic ation
client _ secret
API _ token:
" API token "
client _ id
password
user _ password
user _ password
passcode
client _ secret
secret
password hash
OTP
user auth

## #我通常使用的一些地雷

移除密码
root
admin
log
trash
token
FTP _ PORT
FTP _ PASSWORD
DB _ DATABASE =
DB _ HOST =
DB _ PORT =
DB _ PASSWORD =
DB _ PW =
DB _ USER =
number

***#3 语言***

**使用 github 多克语言获得更有效的结果。**

*喜欢:*语言:shell 用户名
语言:sql 用户名
语言:python ftp
语言:bash ftp

***4#whildcard***

使用*(通配符)获得更多结果，因为有时目标网站有**。com** 或**。网**等。在这种情况下，如果你指定你的 github 搜索像 xyz.com，那么你可能会错过一些 ***。net***

也可以像*.xyz.com 一样使用*(通配符)。

***5 #网址***

你也应该检查网址(这对你的眼睛很重要)，因为有些网址包含一些重要的文件，如 pdf，ppt，xls 文件，其中可能包含敏感信息。

(你可以用类似于 ***的谷歌傻瓜网站来简化这个问题:xxyz . com ext:doc | ext:docx | ext:ODT | ext:pdf | ext:rtf | ext:sxw | ext:PSW | ext:PPT | ext:pptx | ext:PPS | ext:CSV | ext:txt | ext:html | ext:PHP | ext:xls****)*

我这样说是因为我在一些网站上通过这样做找到了包含用户详细信息的 xls 文件。

**你可以在我的**[**github**](https://github.com/tamimhasan404/Google-Dorks)**回购里找到一些有用的 google 呆子。**

***6 #不是***

使用 ***而不是*** 来过滤你的 github 搜索，从 github ocean 中获取确切的信息。喜欢:***xyz.com 文件名:prod.exs 不是 prod.secret.exs*** 。

***#7 社交媒体***

在社交媒体上关注你目标的开发者和员工。他们可以做的事情，如泄漏团队链接是开放的，泄漏功能发布，泄漏收购等。

***#8 一些有用的 github 呆瓜:***

dotfiles
文件名:sftp-config.json 密码
文件名:. s3cfg
文件名:config.php dbpasswd
文件名:。bashrc 密码
文件名:。esmtprc 密码
文件名:。netrc 密码
文件名:_netrc 密码
文件名:。env MAIL _ HOST = SMTP . Gmail . com
文件名:prod.exs 而不是 prod.secret.exs
文件名:。npmrc _auth
文件名:WebServers.xml
文件名:sftp-config.json
文件名:。esmtprc 密码
文件名:passwd 路径:etc
文件名:prod.secret.exs
文件名:sftp-config.json
文件名:proftpdpasswd
文件名:travis.yml
文件名:vim_settings.xml
文件名:sftp.json 路径:。vscode
文件名:secrets.yml 密码
扩展名:sql mysql 转储
扩展名:sql mysql 转储
扩展名:sql mysql 转储密码
扩展名:pem private
扩展名:ppk private

***#自动化:***

手动方式是从 Github 查找敏感信息的最佳方式。但是如果你想自动化这个过程，那么我建议你使用 [**GitDorker**](https://github.com/obheda12/GitDorker) 。在 GitHub 狩猎的时候，我有时也会使用这个工具。虽然它有点慢，因为为了防止速率限制，Gitdocker 每分钟发送 30 个请求。但是它给你的假阳性结果比其他工具少得多。

你可以在上面找到更多的 github 呆子

[https://github . com/random-Robbie/keywords/blob/master/keywords . txt](https://github.com/random-robbie/keywords/blob/master/keywords.txt)

**一些关于 github 呆子/侦察的精彩报道**

[https://orwaatyat . medium . com/your-full-map-to-github-recon-and-leaks](https://orwaatyat.medium.com/your-full-map-to-github-recon-and-leaks-exposure-860c37ca2c82)

HTT[PS://gist . github . com/EdOverflow/922549 f 610 b 258 f 459 b 219 a 32 f 92d 10b](https://gist.github.com/EdOverflow/922549f610b258f459b219a32f92d10b)[https://medium . com/hacker noon/developers-is-unknowely-posting-they-credentials-online-CAA 7626 a6 f 84](https://medium.com/hackernoon/developers-are-unknowingly-posting-their-credentials-online-caa7626a6f84)
[https://shahjerry 33 . medium . com/github-recon-its-really-deep-6553d](https://shahjerry33.medium.com/github-recon-its-really-deep-6553d6dfbb1f)

你也可以在 [***推特上搜索***](https://twitter.com/) 喜欢

***github dork # bugbounty***

了解更多关于 github 呆子的信息。在这里，人们分享他们如何使用 github recon 找到敏感信息，以及他们使用的 github 呆子。

要阅读关于 github 呆子的报道，你可以使用一些简单的谷歌呆子，如***github******呆子网站:hacker one . com***
***github 呆子网站:medium.com***

**伙计们，今天就到这里。希望对你有帮助。如果我写的文章有什么错误，或者你有什么建议，请告诉我。**

**可以关注我的**[**Youtube**](https://www.youtube.com/c/HackoMedia404)**|**[**Github**](https://github.com/tamimhasan404)**|**[**Twitter**](https://twitter.com/tamimhasan404)**|**[**Linkedin**](https://www.linkedin.com/in/tamimhasan404/)**|**[**脸书**](https://www.facebook.com/tamimhasan404)

# 谢谢你😀😀