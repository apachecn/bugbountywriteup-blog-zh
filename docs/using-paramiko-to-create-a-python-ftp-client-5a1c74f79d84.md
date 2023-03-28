# 使用 Paramiko 创建 Python FTP 客户端

> 原文：<https://infosecwriteups.com/using-paramiko-to-create-a-python-ftp-client-5a1c74f79d84?source=collection_archive---------4----------------------->

![](img/f03e399f123f78a74a023b3d70d591dc.png)

## 介绍

FTP 或文件传输协议是一种广泛使用的网络协议，用于在计算机之间传输文件。它已经存在了几十年，今天仍然被广泛使用，尽管出现了像 SFTP 和 HTTPS 这样的新协议。FTP 客户端用于连接到 FTP 服务器并在它们之间传输文件。

Python 是一种流行的编程语言，适用于各种应用，包括 web 开发、数据分析和自动化。它有一套丰富的库和框架，可以轻松快速地构建强大的应用程序。其中一个库是 paramiko，它是 SSH 协议的 Python 实现。

Paramiko 是一个强大的库，允许 Python 开发人员连接到远程服务器并执行各种任务，包括文件传输。在本文中，我们将探索如何使用 paramiko 创建一个 Python FTP 客户端。我们将研究使用 paramiko 连接到 FTP 服务器、传输文件和执行各种其他任务的不同方法。

## Paramiko 入门

在我们开始使用 paramiko 创建 FTP 客户端之前，我们需要安装它。我们可以使用 Python 包管理器 pip 来做到这一点。打开终端，输入以下命令:

```
pip install paramiko
```

这将在您的系统上安装 paramiko 库。一旦安装完成，我们就可以开始在 Python 脚本中使用它了。

连接到 FTP 服务器:

使用 paramiko 创建 FTP 客户端的第一步是连接到 FTP 服务器。我们可以使用由 paramiko 库提供的 SSHClient 类来实现这一点。SSHClient 类允许我们使用 SSH 协议连接到远程服务器。

要使用 paramiko 连接到 FTP 服务器，我们需要导入 SSHClient 类并创建它的一个实例。然后，我们可以使用 connect()方法建立到服务器的连接。connect()方法接受几个参数，包括服务器的主机名、端口号以及用于身份验证的用户名和密码。

以下是如何使用 paramiko 连接到 FTP 服务器的示例:

```
import paramiko
#Create an SSHClient object
client = paramiko.SSHClient()
#Connect to the server
client.connect(hostname=’ftp.example.com’, port=22, username=’user’, password=’pass’) 
```

这段代码创建一个 SSHClient 对象，并使用指定的主机名、端口、用户名和密码连接到 FTP 服务器。FTP 服务器的默认端口号是 21，但有些服务器使用不同的端口号。确保在连接到服务器时使用正确的端口号。

## 传输文件

一旦我们连接到 FTP 服务器，我们就可以使用 paramiko 在服务器和我们的本地机器之间传输文件。Paramiko 提供了几种传输文件的方法，包括

```
sftp_client.put()
sftp_client.get() 
```

## 将文件上传到服务器

```
sftp_client.put('/path/to/local/file.txt', '/path/on/server/file.txt') 
```

## 从服务器下载文件

```
sftp_client.get('/path/on/server/file.txt', '/path/to/local/file.txt') 
```

## 监听文件

```
sftp_client.listdir() 
```

使用这种方法，我们可以列出服务器目录中的文件。该方法返回指定目录中的文件名列表。

以下是如何使用 sftp_client.listdir()列出服务器上根目录中的文件的示例:

列出服务器根目录中的文件

```
files = sftp_client.listdir('/')
print(files) 
```

这将打印服务器根目录中的文件名列表。我们可以使用这个方法来浏览服务器上的文件结构并执行各种任务。

## 执行 FTP 命令

除了传输文件，我们还可以使用 paramiko 向服务器发送 FTP 命令。paramiko 库提供了 FTPClient 类，它允许我们使用 SSH 协议向服务器发送 FTP 命令。

要使用 FTPClient 类，我们需要创建它的一个实例，并向它传递我们之前创建的 SSHClient 对象。然后，我们可以使用 FTPClient 对象向服务器发送 FTP 命令。

以下是如何使用 FTPClient 类向服务器发送 FTP 命令的示例:

```
# Create an FTPClient object
ftp_client = paramiko.FTPClient(client)
# Send an FTP command to the server
response = ftp_client.sendcmd('PWD')
print(response) 
```

这段代码创建一个 FTPClient 对象，并将 PWD(打印工作目录)命令发送到服务器。服务器将以服务器上的当前工作目录作为响应，该目录将被打印到屏幕上。

我们可以使用 FTPClient 对象向服务器发送任何 FTP 命令。一些常见的命令包括 CD(更改目录)、LS(列出目录内容)和 MKDIR(创建目录)。

## 结论

在本文中，我们探讨了如何使用 paramiko 创建 Python FTP 客户端。我们研究了使用 paramiko 连接到 FTP 服务器、传输文件和执行各种其他任务的不同方法。

Paramiko 是一个强大的库，允许 Python 开发人员使用 SSH 协议连接到远程服务器并执行各种任务。通过使用 paramiko，我们可以轻松地创建一个 Python FTP 客户端，它可以连接到 FTP 服务器、传输文件和执行各种其他任务。

总的来说，对于任何希望在远程服务器上自动化任务或使用 Python 执行文件传输的人来说，paramiko 都是一个有用的工具。无论您是 web 开发人员、数据分析师还是自动化工程师，paramiko 都可以帮助您更高效地完成任务。