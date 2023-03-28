# HTB 大学 CTF 2022-云-魔法

> 原文：<https://infosecwriteups.com/htb-university-ctf-2022-cloud-enchanted-2966780f13f5?source=collection_archive---------2----------------------->

黑盒子大学 CTF 是一个伟大的 CTF 大学和世界各地的大学生。这些挑战代表了帮助您提高网络安全知识的真实世界场景。这篇文章主要关注 Azure 云枚举和开发。

# 扫描打开的端口

使用 nmap 扫描挑战中提供的 IP 地址。我们发现总共有 5 个港口开放。让我们列举运行在端口 80 上的 web 服务。

![](img/ea625bf06ebb0b49d76c67b26b8f1749.png)

# 枚举 web 服务

分析客户端资源，我们可以说应用程序正在从 Azure Blob 获取某些资源。

![](img/1e423f4b34e0b8c8bdf4bf19793bfd6f.png)

Azure Blob 可以在[http://enchanted.blob.core.windows.net:10000](http://enchanted.blob.core.windows.net:10000)访问。让我们打开/etc/hosts 文件并在其中添加 enchanted.blob.core.windows.net 的**。**

**![](img/8e429b0f3069d007709833de9edf9e04.png)**

# **蓝色斑点开发**

**通过 web 枚举 Azure blob 是可能的。从上一节给出的截图中可以看到，我们可以看看一些参数，比如 **se** 、 **sp** 、 **sv** 、 **sr** 和 **sig** 。这些参数组合在一起形成一个 **sas_token** ，这是一种安全的方法，可以在不泄露帐户密钥的情况下授予对资源的有限访问权限。**

**![](img/c403bfff1c05efae193f95fe8ffaea48.png)**

**这样，我们可以通过 Web 或 SDK 使用这个 **sas_token** 来尝试访问或枚举 Azure Blob 中的一些其他资源。**

## **枚举资源**

**这里，容器名是**魔法**，我们可以访问 **sas_token** 。要列出容器条目，我们可以访问下面给出的 URL，**

```
http://enchanted.blob.core.windows.net:10000/enchanted?restype=container&comp=list&se=2420-01-01&sp=rl&sv=2021-06-08&sr=c&sig=pkZBtmnBrAd5UtKzsLNKq1XOUTOwpUcB2A%2B2jUa9UzI%3D
```

**![](img/56fe8baba1706a4f907fc1a5c7e3430f.png)**

****app.py** 文件看起来很有意思。我们可以通过访问下面给出的 URL 来访问它的内容，**

```
http://enchanted.blob.core.windows.net:10000/enchanted/dev/app.py?se=2420-01-01&sp=rl&sv=2021-06-08&sr=c&sig=pkZBtmnBrAd5UtKzsLNKq1XOUTOwpUcB2A%2B2jUa9UzI%3D
```

**访问 URL 后， **app.py** 文件应该会下载到您的本地机器中。**

```
from flask import *

dev = Flask(__name__)

@dev.route('/')
def index():
        return render_template('index.html')

@dev.route('/fetch')
def fetch():
        table_service = TableService(sas_token='se=2420-01-01&sp=raud&sv=2019-02-02&tn=users&sig=m9BmFvpbJBTug8psvW6RJo/FNLmReeLl8%2B4kH5bimCw%3D', protocol='http', endpoint_suffix='core.windows.net')
        i=0
        next_pk = None
        next_rk = None
        while True:
            entities=table_service.query_entities('users',"PartitionKey eq 'Username'", next_partition_key = next_pk, next_row_key = next_rk, top=1000)
            i+=1
            for entity in entities:
                return render_template('home.html',entities=entity.AddressLine1)
            if hasattr(entities, 'x_ms_continuation'):
                x_ms_continuation = getattr(entities, 'x_ms_continuation')
                next_pk = x_ms_continuation['nextpartitionkey']
                next_rk = x_ms_continuation['nextrowkey']
            else:
                break
```

**该文件包含一些关于**表服务**和与之关联的 **sas_token** 的信息。Azure Table storage 是一种在云中存储非关系型结构化数据(也称为结构化 NoSQL 数据)的服务。**

# **蓝色桌面开发**

**Azure Table storage 是一种在云中存储非关系型结构化数据(也称为结构化 NoSQL 数据)的服务，提供了一种无模式设计的键/属性存储。**

**表服务运行在端口 ***10002*** 上，我们已经从源代码(之前下载的)中提取了 **sas_token** 。使用目前收集的信息，我们可以通过 Web 或 SDK 枚举 Azure 表存储。**

**参考:[https://learn . Microsoft . com/en-us/rest/API/storage services/query-entities](https://learn.microsoft.com/en-us/rest/api/storageservices/query-entities)**

**还有一件事，我们需要在/etc/hosts 文件中添加 enchanted.table.core.windows.net 的**来访问表服务。****

**![](img/7c775d6f1b5e0585b981f9ea4c978204.png)**

## **枚举用户表**

**要列出 users 表中的所有条目，我们可以访问下面给出的 URL，**

```
http://enchanted.blob.core.windows.net:10002/users?se=2420-01-01&sp=raud&sv=2019-02-02&tn=users&sig=m9BmFvpbJBTug8psvW6RJo/FNLmReeLl8%2B4kH5bimCw%3D
```

**我们从之前下载的源代码中获得了 **sas_token** 。在访问 URL 时，我们应该得到类似于**atomformattonsupported**的错误。**

**![](img/d212f2f4e0b9e749caa36535b47ce231.png)**

**这个错误可以通过将 **Accept** 头替换为值**application/JSON 来修复；odata=nometadata"****

**参考:[https://stack overflow . com/questions/39463917/azure-table-service-rest-API-JSON-format-is-not-supported](https://stackoverflow.com/questions/39463917/azure-table-service-rest-api-json-format-is-not-supported)**

**![](img/9e9bc02ca33606dc6eba9e8eb2482353.png)**

**感谢您花时间阅读我的文章:)**

**[](https://voker2311.github.io/) [## 主页

### 网络安全分析师@Digisec360 CEH(实用)| CRTP |约格沙| Pentabug

voker2311.github.io](https://voker2311.github.io/) [](https://twitter.com/v0k3r2311) [## JavaScript 不可用。

### 编辑描述

twitter.com](https://twitter.com/v0k3r2311) 

## 来自 Infosec 的报道:Infosec 每天都有很多内容，很难跟上。[加入我们的每周简讯](https://weekly.infosecwriteups.com/)以 5 篇文章、4 个线程、3 个视频、2 个 GitHub Repos 和工具以及 1 个工作提醒的形式免费获取所有最新的 Infosec 趋势！**