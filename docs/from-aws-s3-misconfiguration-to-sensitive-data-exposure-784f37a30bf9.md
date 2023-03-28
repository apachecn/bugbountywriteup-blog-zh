# 从自动气象站 S3 错误配置到敏感数据暴露

> 原文：<https://infosecwriteups.com/from-aws-s3-misconfiguration-to-sensitive-data-exposure-784f37a30bf9?source=collection_archive---------2----------------------->

![](img/d2e720f11a1592584a9d1ab9d12e6821.png)

马库斯·斯皮斯克在 [Unsplash](https://unsplash.com/s/photos/information-disclosure?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

公司通常部署第三方应用程序来存储各种媒体内容。这些内容通常是各种文件格式，如图像、文档、Html、JavaScript、SQL。等等。在我参与 bug bounty 项目的过程中，经常会发现在 web 应用程序的不同地方(如网站源代码)或通过特定操作(如文件上传)公开了对亚马逊 AWS S3 桶的引用。

反应

```
200 OK“Successfully uploaded to s3://testbucket/profilepicures/user/fancy_avatar.jpg”
```

这些桶也可以使用谷歌词典找到；" site:S3 . Amazon AWS . com " " target . com "

在部署过程中发现这些云存储应用程序配置错误也并不罕见。一个恰当的例子是我在新年前几天寻找的一个特定目标。在测试 AWS s3 存储桶的访问控制权限时，要检查的典型操作包括:

`aws s3 ls s3://test-bucket to list folders and objects within the bucket`

`aws s3 ls s3://test-bucket/test-folder/ to list folders and objects within the /test-folder/`

`aws s3 cp test.txt s3://test-bucket to copy a file (test.txt) to the bucket`

`aws s3 mv test.txt s3://test-bucket to move a file (test.txt) to the bucket`

`aws s3 rm s3://test-bucket/test.txt to delete the file (test.txt) from the bucket`

***亚马逊*** *S3 访问控制列表(ACL)使您能够管理对桶和对象的访问。每个存储桶和对象都有一个****ACL****作为子资源附加到其上。它定义了哪些 AWS 帐户或组被授予访问权限以及访问类型。*

现在，对于我最近的目标，我注意到对所有典型的 OWASP 类型的 bug 的测试都没有发现。目标在很大程度上被强化了。然而，当上传一个个人资料图片到站点时，文件被保存到 s3 存储桶，并且每当文件被访问时从那里被加载。此外，该网站为最终用户提供了上传、查看和更新其简历的功能。执行这些操作还暴露了 S3 桶的 URL。

```
s3://vulnerable-bucket.s3-ap-southeast-1.amazonaws.com/
```

该网站基本上是一个平台，将求职者与招聘人员联系起来，并确保各种工作的候选人得到正确安置，并与现有的工作机会相匹配。我记下了这个 s3 URL，CVs 和个人资料图片都上传到了这个 URL，并继续我的测试。

直到很久以后，我才推出了亚马逊 AWS S3 CLI T2，这是一个命令行工具，任何人都可以使用它在 s3 bucket 上执行各种操作。这些包括创建、删除、上传和同步存储桶、文件夹和存储桶中的对象。我通过尝试将一个文件复制并移动到 bucket 中来开始我的测试。

我用我的标识符别名创建了一个简单的文本文件，并尝试了；

```
aws s3 cp test.txt s3://vulnerable-bucket 
```

返回拒绝访问错误，以下尝试也是如此；

```
aws s3 mv test.txt s3://vulnerable-bucket
```

访问控制设置正确地限制了存储桶上的复制和移动操作。然后，我试图执行一个 list 操作来检查这是否也受到了限制。下面的文件夹结构被返回，令我惊讶的是，它包括敏感的文件夹，包含公司和用户数据。

```
 PRE JD_2.0/
 PRE candidatebulkupload/
 PRE candidatefiles/
 PRE companylogo/
 PRE feed/
 PRE marketmapresumes/
 PRE medias/
 PRE parsed_resumes/
 PRE printableprofiles/
 PRE production-logs/
 PRE profilepic/
 PRE metrics/
 PRE resume/
 PRE resumes/
 PRE uploads/
 PRE whatsapp/
```

我立即浏览了我认为最敏感的文件夹，以确定我是否可以实际访问文件本身，而不仅仅是目录/文件结构。resumes 文件夹尤其令人感兴趣，因为它包含了已上传到 web 应用程序的用户的每一份简历。简历显示了这类文件中典型的个人信息，如全名、出生日期、地理位置、爱好等。

确认这一点后，我停止了测试，并向受影响的程序提交了一份报告，该程序很快做出了响应并实施了修复。这种影响可能是毁灭性的——考虑到 s3 存储桶的所有内容都可以追溯到公司成立之初。不清楚这个 s3 存储桶向公众开放了多长时间，所以在我自己发现之前，它可能已经被未经授权的访问了。

**提示** : *您可以使用* `*aws s3 cp s3://test-bucket/file.txt ./*` *命令将 bucket 中包含的文件下载到您当前的工作目录中。在需要对文件进行进一步分析的情况下非常有用。*

希望你喜欢阅读这篇文章。下次再见，祝你黑客愉快！