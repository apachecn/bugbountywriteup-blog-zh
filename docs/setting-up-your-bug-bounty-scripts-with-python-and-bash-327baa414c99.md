# 子域监控机器人——用 Python 和 Bash 设置 bug bounty 脚本

> 原文：<https://infosecwriteups.com/setting-up-your-bug-bounty-scripts-with-python-and-bash-327baa414c99?source=collection_archive---------1----------------------->

你好，

自动化是非常有趣的事情，如果以正确的方式完成，它会更加有趣。但是编写自动化脚本是最重要的事情。因此，在本文中，我们将讨论用 Python 和 Bash 为您的 VPS 服务器(或者可能是 raspberry pi)编写自动化脚本。如果你还没有读过我的前一篇文章[监控你的 bug 奖励目标](/monitoring-your-targets-for-bug-bounties-36f6be3e69c9)，这是我们系列的主要文章，我强烈建议你先读一下。

![](img/5923a5c8e1aa4d0a34690254f39482f1.png)

[Sai Kiran Anagani](https://unsplash.com/@anagani_saikiran?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片；自动化脚本

首先，让我们决定什么将是我们的自动化系统可以用于。以下是可能性:-

*   新子域名监控
*   原木中毒
*   核心
*   带有 gf 的 Waybackurls
*   等等。

因此，在本文中，我们将从非常基本的机器人开始，为新的子域监控

# 该算法

该算法可以用各种方法设计。在 bash 中，在 python 中，混合使用，所以我们将借助 bash 脚本运行 python 和 shell 命令

> 打开包含用户提供的目标的现有文件“domains.txt”
> 
> 对一些目标进行排序，因为文件可能包含数千个域，我们不希望我们的网络/机器上有那么多负载
> 
> 在它们上面运行一个工具，可能是 amass，然后输出到一个文件“new-subdomains.txt”
> 
> 与旧文件“subdomains.txt”进行比较，过滤掉新文件。
> 
> 将它们发送到用户定义的渠道，可能是 slack 或电子邮件(强烈推荐 slack，因为您将拥有一个有组织的数据)。此外，将新的子域写入“subdomains.txt”文件，这样当您想要搜索该目标时，就可以很容易地获得一个列表

# 设置事物

创建目录来组织所有数据是很重要的。所以我们需要建立目录，这样事情就不会变得一团糟。目录的结构可以是:-

```
.
├── domains.txt
├── init.py
├── last.txt
├── main.sh
├── max.txt
├── runner.py
├── sorter.py
└── targets
    ├── abc.com
    │   ├── new-subdomains.txt
    │   └── subdomains.txt
    └── xyz.com
        ├── new-subdomains.txt
        └── subdomains.txt
```

所以在这里，`domains.txt`是众所周知的，`init.py`是将初始化新目标的脚本，`runner.py`是主脚本，`sorter.py`将从`domains.txt`中取出所需数量的域，并将其输出到`max.txt.`，最后，`last.txt`是包含最后扫描的域的文件，以便脚本可以从那里继续。

# sorter.py

首先，让我们看看`sorter.py`的代码

```
import os

# Edit the line below to specify the number of domains to be scanned in a run
max_scans = 11

last_scanned_url = open("last.txt", 'r').read().replace("\n", '')

length = 0

with open("domains.txt", "r") as urls_file:
    for url in urls_file:
        url = url.replace('\n', '')
        if url == last_scanned_url:
            for i in range(max_scans):
                next_url = urls_file.readline().replace('\n', '')
                print(next_url)
                length += 1
                open('last.txt', 'w').write(next_url)

if length < max_scans:
    with open("domains.txt", 'r') as urls_file:
        for url in urls_file:
            url = url.replace('\n', '')
            if length < max_scans:
                print(url)
                length += 1
                open('last.txt', 'w').write(url)
```

现在，让我们看看这是怎么回事

视频到 sorter.py

我希望你已经明白了，如果没有，让我们明白。

这段代码首先打开一个名为`last.txt`的文件，并获取最后扫描的域。之后，它打开文件`domains.txt`并检查最后扫描的域在哪里。一旦找到它，它打印域并写入`last.txt`,以便下次运行时可以从那里继续。此外，如果它到达文件的最后一行，它会跳到第一行，然后再次开始读取。它使用命令`python3 sorter.py > max.txt`运行，以便将输出写入文件。现在，一个简单的问题

> 这可以直接从主脚本中完成，为什么要放在这里呢？
> 
> Ans。是的，你能做那件事。但是当你有多个机器人时，事情就变得混乱了。为了避免把事情弄糟，要以整洁的方式去做

# 创建主脚本— runner.py

`runner.py` —这是对我们来说最重要的脚本，因为它将负责完成所有的任务。但是在设置它之前，我们需要设置我们的 slack 通道，所以让我们先这样做。

## 设置松弛通道

假设您已经有了一个 slack 帐户和一个工作空间，请按照以下步骤操作

*   ***打开松弛工作区(https://app.slack.com/client/<某物> / <其他物>)点击添加通道***

![](img/5e31af0b9262851cae8a33eaff1ee804.png)

点击添加频道按钮

*   ***给它起一个好名字，点击创建，跳过添加一些人(如果你想添加一些人，请随意)***

![](img/a8d24e7689f14be2dbb49e216b0c1716.png)

为通道命名

*   ***现在，头转向***[**【https://api.slack.com/】**](https://api.slack.com/)***，点击【创建 app】***

![](img/9d34e4fe3c47aabfca7752f7700d982a.png)

创建新应用程序

*   ***之后会弹出一个新框，点击*** `***From Scratch***` ***>给它命名>给它分配一个通道，点击*** 创建 app

![](img/a961403554c087064c5f13e2a55a2fc1.png)

选择此处暂存

*   ***一旦创建，进入*** `***Incoming Webhooks***` ***并打开***

![](img/e3954ca88b4895ccb0745b8deb9eb2e0.png)

传入 webhook

![](img/c23055c5b65f00bb791f3da53f64715a.png)

打开传入网络挂钩

*   ***打开后，你会在同一页面的*** 下方看到类似这样的内容

![](img/3729e0989fd27974ef798edcf85174eb.png)

Slack webhook API

*   ***点击*** `***Add New Webhook to Workspace***` ***会将您重定向到一个页面，您可以在该页面选择它可以发送消息的空闲通道。现在，只需选择一个频道，并点击*允许**

![](img/1d8feca0c314f6eec4b96ea43d378124.png)

允许 bot 在传入的 webhook 的帮助下向 slack 通道发送消息

*   ***一旦完成，你会在*** `***Sample curl request to post to a channel***` ***处看到一个*** `***curl***` ***命令。只要复制它，粘贴到一个终端，你会在你的 slack 频道*** 看到一条消息

![](img/65aac9bcd353e472c3b498f3198f2383.png)

获取 cURL 命令来发送一个松弛消息

![](img/48266ae536356ce9c6668127d5226c1c.png)

执行命令

![](img/b5283626f656e5fdc22d98265620aaba.png)

并检查你的松弛信道的消息

现在，我们已经完成了 slack 通道的设置。可以通过改变 JSON post 数据中`"text"`的值来改变消息。

## runner.py —主脚本

这个脚本将负责运行不同的工具，比较输出，然后向 slack 发送消息。

因此，首先，我们将创建一个函数，将消息发送给 slack

```
import requests

def send_msg_to_slack(message):
    # Get your webhook URL `Incoming Webhooks` page. It will be like 
    # https://hooks.slack.com/services/T04RRRRRRRR/B04RRRRRRRR/B55JREDACTEDREDACTEDAAAA
    # The link above is just a random link
    webhook_url = "<paste_your_webhook_URL_here>"
    payload = {"text": message}
    requests.post(webhook_url, json=payload)
```

现在，我们需要在 python 的帮助下运行一个子域枚举工具(这里我将用 amass 演示)，然后比较旧文件和新文件。(**重要提示**:命令只能在目标的目录下运行，脚本在`.` (按照上面的目录结构))

而在此之前，我们会使用`os`模块的`system()`函数来执行命令。这也可以通过`subprocess`模块完成。

```
from os import system

def run_amass():
    with open("max.txt", 'r') as file:
        for target in file:
            target = target.replace("\n", '')
            system(f"cd targets/{target}/ && amass enum -d {target} -o new-subdomains.txt")

            # read the old subdomain list and save it in old_subdomains
            old_subdomains = []
            with open(f"targets/{target}/subdomains.txt", 'r') as old_subdomain_list:
                for subdomain in old_subdomain_list:
                    subdomain = subdomain.replace('\n', '')
                    old_subdomains.append(subdomain)

            # read the new subdomain list and save it in new_subdomains
            new_subdomains = []
            with open("targets/{target}/new-subdomains.txt", 'r') as new_subdomain_list:
                for subdomain in new_subdomain_list:
                    subdomain = subdomain.replace("\n", '')
                    new_subdomains.append(subdomain)

            # compare them and send new ones to slack and
            # also write them to old subdomain list
            found_subdomains = [] # will store the unique subdomains/newly found
            for subdomain in new_subdomains:
                if subdomain in old_subdomains:
                    pass
                else:
                    found_subdomains.append(subdomain)
            if len(found_subdomains > 0):
                msg = "The following new subdomains were found:-"
                for subdomain in found_subdomains:
                    msg = msg + "\n" + subdomain
                send_msg_to_slack(msg)
            # now, write them to old subdomains file
            to_write = open(f"targets/{target}/subdomains.txt", 'r').read()
            for subdomain in found_subdomains:
                if to_write == "":
                    to_write = subdomain
                else:
                    to_write = to_write + "\n" + ""
            open(f"targets/{target}/subdomains.txt", 'w').write(to_write)
```

# 初始化. py

由于可能有 1000 个可用的域，所以我们需要自动创建文件夹，并最初运行 amass 的任务。(我们需要创建一个名为`init.txt`的文件，它将包含我们想要应用的所有域。同样，我们只需要执行`mkdir targets`来创建一个目录。之后，脚本就可以开始了)

```
import os

with open("init.txt", 'r') as file:
    for line in file:
        line = line.replace('\n', '')
        os.mkdir(f"targets/{line}")
        os.system(f'cd targets/{line}/ && amass enum -d {line} -o subdomains.txt')
        open('domains.txt', 'a').write(("\n" + line))
```

根据本文上面给出的文件结构，这个脚本也应该在`.`目录中运行

# 总结一下— main.sh

我们现在需要运行`sorter.py`然后再运行`runner.py`来做我们想做的事情。所以我们可以简单地用一个 shell 脚本来完成它

```
python3 sorter.py > max.txt
python3 runner.py
```

保存后，执行`chmod +x main.sh`使文件可执行

# 使用 crontab 计划它

正如我们在上一篇文章中所讨论的，我们可以使用 crontab 来调度脚本，所以您可以通过向 crontab 添加以下几行来实现这一点

```
00 01 * * * cd /path/to/your/bot/folder/ && ./main.sh
```

这将根据您的系统时间在每天凌晨 01:00 执行脚本

如果你对这篇文章有任何疑问，欢迎访问[黑客之家](https://www.houseofhackers.xyz/t/setting-up-your-bug-bounty-scripts-with-python-and-bash/161?u=shriyanss)的这个帖子，并在那里给我贴上标签

## [给我买杯咖啡](https://www.buymeacoffee.com/shriyanss)

![](img/2093d0f16d94a8942508624035f676b1.png)

给我买杯咖啡

希望你喜欢这篇文章。请随时关注我并订阅我的邮件列表，这样每当我写下一部分时，您都可以获得这篇文章:)

[订阅这里](https://shriyanssudhi.medium.com/subscribe)

> 我希望你喜欢这篇文章。欢迎**关注**我了解更多信息。
> 
> **推特:**[https://twitter.com/ss0x00](https://twitter.com/ss0x00)(我这里比较活跃*😄*)
> 
> **领英:**[https://www.linkedin.com/in/shriyans-s-a62826216/](https://www.linkedin.com/in/shriyans-s-a62826216/)
> 
> **提示:**你可以为一篇文章**鼓掌多达 50 次**，你可以将这篇文章添加到你的列表(阅读列表)中，以便在你需要的时候方便地找到它
> 
> ;)

## 来自 Infosec 的报道:Infosec 每天都有很多内容，很难跟上。[加入我们的每周简讯](https://weekly.infosecwriteups.com/)以 5 篇文章、4 个线程、3 个视频、2 个 GitHub Repos 和工具以及 1 个工作提醒的形式免费获取所有最新的 Infosec 趋势！