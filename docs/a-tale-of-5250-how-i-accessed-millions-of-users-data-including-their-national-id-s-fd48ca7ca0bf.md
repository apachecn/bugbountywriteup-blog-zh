# 一个 5250 美元的故事:我如何访问数百万用户的数据，包括他们的地址和个人信息

> 原文：<https://infosecwriteups.com/a-tale-of-5250-how-i-accessed-millions-of-users-data-including-their-national-id-s-fd48ca7ca0bf?source=collection_archive---------0----------------------->

嗨，希望你们都很好，新年快乐，耶！✨，让我们开始写博客吧，不要再浪费时间了。

像往常一样，我在 Tecno src 程序中寻找应用程序源代码中的某些东西，因为范围很大，所以我收集了所有的应用程序，并使用 apktool 通过以下命令一次性反编译它们: **find。-iname "*。apk "-exec apk tool d-o { } _ out { } \；**

(是的，反编译🥲需要很长时间)

# **寻找水桶:**

现在我开始在反编译的文件中寻找一些有趣的东西，但是确实有

大约 50+个应用程序，我不能手动查看每个应用程序对吗？我刚刚有了一个关于 nuclei 的想法，然后我就知道有 android 应用程序的模板，

我只是下载了它们，并在整个目录上启动了 nuclei😅

命令:**nucleus-target/path-to-output-folder/" Android testing "/alla pks/-t/path-to-tam plates/mobile-nucleus-templates/**

运行了 18-19 分钟后，Nuclei 给出了一个输出，说**发现了 S3 存储桶，**我试图通过 AWS CLI 访问它，结果是这样的:**访问被拒绝**😔，没有运气。

然后经过几分钟的运行，我得到了一个 s3 桶的输出，我漫不经心地试图访问它，没有任何希望，该死！s3 桶里装满了果汁，我就想:

我只是简单地访问了 tecno 的内部文件、用户和他们拥有的一切数据，我可以下载一切，甚至整个桶😂。

这里只是数据的一瞥:现在我非常确定桶里装满了果汁。啊，我想看更多的文件，但是因为我们必须遵守 bug 赏金规则，所以我停止了更多的工作，直接向团队汇报。

现在，在报告之后，我又有了一个带有 nuclei 的 s3 存储桶，它还包含大约 4-5g 的数据😳

我也报告过，但是不知道为什么团队说“**两个 s3 桶都是由同一个团队管理的”**所以他们把我的报告合并到了之前的报告中😔，我没有想到会是这样😔我试图说服他们不能合并，但他们就是这么做了😔但是他们在这个项目上给了我额外的 25 个声誉，并把报告移到了关键，这对于我刚刚发现的来说还是很少的！伙计们，你能说他们在这里合并不同的报告是对还是错吗？在评论中写下来，以便团队可以看到🥲，我只为一份报告奖励了 5250 美元，为第二份报告奖励了 0 美元，尽管它包含了如此多的敏感 data🥲，我想说的是，我没有从桶中下载任何文件，我只从 tecno 的服务器下载了一个文件，以便与漏洞报告一起发送，现在他们修复了服务器，没有人可以访问数据，所有人的数据都是安全的。

**链接:**

**细胞核**:https://github.com/projectdiscovery/nuclei**感谢** [**项目发现**](https://github.com/projectdiscovery/nuclei)

**安卓细胞核模板**:[https://github.com/optiv/mobile-nuclei-templates](https://github.com/optiv/mobile-nuclei-templates)

**apk tool**:[https://github.com/iBotPeaches/Apktool](https://github.com/iBotPeaches/Apktool)

谢谢你们的阅读，我希望你们喜欢，请忽略任何错误和我的语法😅，你们可以在 Twitter 上关注我: [@__sam0_0](https://twitter.com/__Sam0_0)

我将很快发表更多的文章，敬请期待！！！这么🔥