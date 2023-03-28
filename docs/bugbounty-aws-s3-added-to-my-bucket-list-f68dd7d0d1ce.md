# # BugBounty——AWS S3 加入了我的“遗愿”清单！

> 原文：<https://infosecwriteups.com/bugbounty-aws-s3-added-to-my-bucket-list-f68dd7d0d1ce?source=collection_archive---------2----------------------->

嗨伙计们，

你可能还记得价值[百万美元的 Instagram 漏洞](https://www.forbes.com/sites/thomasbrewster/2015/12/17/facebook-instagram-security-research-threats/#1ed5a3492fb5)，该漏洞允许安全研究员韦斯·温伯格访问 Instagram 上的每一张图片和账户。这一切之所以成为可能，是因为他获得了进入 *Instagram 的 S3 存储区*的权限，该公司在那里存储了从源代码到图像的所有东西。在这篇特别的博客中，我将向你解释“错误配置的 AWS 存储桶是如何成为巨大的安全风险的”。

最近在我搜索 bug 的时候，我遇到了一个错误配置的印度电子商务公司的 AWS S3 桶，它给了我完全访问他们的 S3 桶的权限，允许他们下载、上传和覆盖文件。让我们对此进行更深入的研究，看看我是如何做到这一点的——因此，在搜索网站的一些安全漏洞时，我看到了一个职业页面，用户可以在那里申请相关的工作并上传简历。我开始测试它，发现了一个端点(让我们把公司命名为 xyz)

【https://xyz.s3.amazonaws.com/career%2Ftest.p】[](https://zoomcar.s3.amazonaws.com/career%2Ftest.docx)**df "*没有 ACL 限制设置，任何未经认证的用户都可以访问任何文件，下面是 curl 请求—*

**curl-XGET’*[*https://xyz.s3.amazonaws.com/career/test.pdf'*](https://zoomcar.s3.amazonaws.com/a.txt')*

*回应是 test.pdf 的内容。类似地，我发现在 S3 存储桶上启用了“PUT”方法，我可以简单地将任何文件写入 S3 存储桶，它是公开可写的。*

**curl-XPUT-d ' HACKED ' '*[【https://xyz.s3.amazonaws.com/career/test.pdf'】T21](https://zoomcar.s3.amazonaws.com/a.txt')*

*我对 filename 运行了一些暴力操作，并且能够读取其他用户的简历内容。:)，现在，我必须把这一个水平提高，我的下一个目标是列出并阅读 S3 桶上所有可用的文件。我连接到 s3 命令行并运行以下命令*

**“root @ logic bomb ~ # AWS S3 ls S3://*[*xyz.s3.amazonaws.com*](https://zoomcar.s3.amazonaws.com/a.txt')*”**

*毫不奇怪，我能够访问完整的 s3 存储桶，所有用户的简历(也有更多敏感数据和目录)都是公开可访问和可读的:)我尝试了更多的命令*

*root @ logic bomb:~ # AWS S3 RM*S3://*[*xyz.s3.amazonaws.com*](https://zoomcar.s3.amazonaws.com/a.txt')*/career/test . pdf**

*删除:*S3://*[*xyz.s3.amazonaws.com*](https://zoomcar.s3.amazonaws.com/a.txt')*/career/test . pdf**

*我也可以删除这些文件。*

***总之，错误配置的 S3 存储桶可能会使您的组织暴露敏感数据。***

****缓解—*** *建议您及时检查您的 S3 存储桶及其内容，以确保您不会无意中向用户提供您不想要的对象。作为参考，你可以阅读下面的链接—**

*[](https://cloudacademy.com/blog/amazon-s3-security-master-bucket-polices-acls/) [## 亚马逊 S3 安全:主 S3 桶策略和 ACL

### 欢迎阅读我的 AWS 安全系列的第 8 部分。本周，我将介绍一些围绕…的安全特性

cloudacademy.com](https://cloudacademy.com/blog/amazon-s3-security-master-bucket-polices-acls/) 

*报告详情-*

2017 年 12 月 8 日—向相关公司报告了错误。

2017 年 12 月 29 日—错误被标记为已修复。

2018 年 1 月 1 日—重新测试并确认了修复。

2018 年 1 月 7 日—由公司授予。

感谢阅读！

~逻辑炸弹([https://twitter.com/logicbomb_1](https://twitter.com/logicbomb_1))*