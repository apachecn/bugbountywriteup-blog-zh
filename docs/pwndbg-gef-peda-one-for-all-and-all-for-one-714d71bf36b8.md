# pwn dbg+GEF+Peda——我为人人，人人为我

> 原文：<https://infosecwriteups.com/pwndbg-gef-peda-one-for-all-and-all-for-one-714d71bf36b8?source=collection_archive---------0----------------------->

## 同时安装所有插件，并通过简单的命令进行切换。

![](img/25c6592db66438780042c1178956f4fa.png)

毫无疑问，GDB 是一个神奇的工具，几乎每个网络安全专业人士、实习生、爱好者和研究人员都用过它。它是程序调试的瑞士军刀，然而有一个问题。香草 GDB 在用户体验方面很糟糕。

这就是开发许多插件的原因，这些插件可以使反向和调试的过程变得容易得多。也就是说，三个最受欢迎的是:

> https://github.com/pwndbg/pwndbg
> 
> 佩达:https://github.com/longld/peda
> 
> 全球环境基金:[https://github.com/hugsy/gef](https://github.com/hugsy/gef)

当然，它们都有利弊。也许是为了任务，也许是功能，甚至是界面。我们都有自己的偏好。就我个人而言，我更喜欢 Pwndbg 的界面，但说真的，Peda 的循环模式创建和偏移搜索功能非常方便。

尽管如此，我讨厌每当我想使用不同的插件时不得不手动更改或替换`.gdbinit`文件。这不是时间和精力的问题，而是因为它分散了我主要任务的注意力，这是我想要避免的。

因此，这篇博文的目的是描述一种非常简单的在单一命令中切换插件的方法。

# TL；DR；

我已经创建了一个 bash 脚本，它在一个命令中执行下面的指令，所以为了快速设置，克隆下面的库并运行`install.sh`。

 [## apogiatzis/gdb-peda-pwndbg-gef

### 这是一个在单一命令中安装 Pwndbg、GEF 和 Peda GDB 插件的脚本。运行 install.sh，然后使用一个…

github.com](https://github.com/apogiatzis/gdb-peda-pwndbg-gef) 

# 装置

最初，需要下载和安装插件。因此，请遵循以下命令:

**Pwndbg**

```
git clone [https://github.com/pwndbg/pwndbg](https://github.com/pwndbg/pwndbg)
cd pwndbg
./setup.sh
cd ..
mv pwndbg ~/pwndbg-src
echo "source ~/pwndbg-src/gdbinit.py" > ~/.gdbinit_pwndbg
```

## 佩达

```
git clone https://github.com/longld/peda.git ~/peda
```

**全球环境基金**

```
wget -q -O ~/.gdbinit-gef.py https://github.com/hugsy/gef/raw/master/gef.py
echo source ~/.gdbinit-gef.py >> ~/.gdbinit
```

# 集所有功能于一身

本质上，这些插件修改了`.gdbinit`文件，并随 gdb 一起启动。现在，这里有一个技巧，如果我们有一个包含所有插件配置的`.gdbinit`文件，这样它们就可以基于 gdb 命令被有条件地激活，会怎么样？这正是我们将要做的。

打开您的`.gdbinit`文件，删除任何内容并粘贴以下配置:

```
define init-peda
source ~/peda/peda.py
end
document init-peda
Initializes the PEDA (Python Exploit Development Assistant for GDB) framework
end

define init-pwndbg
source ~/.gdbinit_pwndbg
end
document init-pwndbg
Initializes PwnDBG
end

define init-gef
source ~/.gdbinit-gef.py
end
document init-gef
Initializes GEF (GDB Enhanced Features)
end
```

此外，在您的`/usr/bin`文件夹中创建以下 3 个文件:

首先创建`/usr/bin/gdb-peda`并粘贴以下内容:

```
#!/bin/sh
exec gdb -q -ex init-peda "$@"
```

然后`/usr/bin/gdb-pwndbg`

```
#!/bin/sh
exec gdb -q -ex init-pwndbg "$@"
```

最后，`/usr/bin/gdb-gef`

```
#!/bin/sh
exec gdb -q -ex init-gef "$@"
```

最后一步是为前面创建的所有三个文件授予可执行权限。为此，运行:

```
chmod +x /usr/bin/gdb-*
```

仅此而已。你看到了吗？简单。

现在，您可以通过运行以下三个命令之一来测试它:

```
gdb-peda
gdb-pwndbg
gdb-gef
```

希望这对人们有所帮助。下次见。

*关注* [*Infosec 报道*](https://medium.com/bugbountywriteup) *获取更多此类精彩报道。*

[](https://medium.com/bugbountywriteup) [## 信息安全报道

### 收集了世界上最好的黑客的文章，主题从 bug 奖金和 CTF 到 vulnhub…

medium.com](https://medium.com/bugbountywriteup)