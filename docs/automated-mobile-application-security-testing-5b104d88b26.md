# 自动化移动应用安全测试

> 原文：<https://infosecwriteups.com/automated-mobile-application-security-testing-5b104d88b26?source=collection_archive---------2----------------------->

## 使用移动安全框架(MobSF)的自动化静态和动态移动应用安全测试

![](img/0fe95321655a3f8e61f91faf95b76077.png)

[移动安全框架(MobSF)](https://mobsf.github.io/docs/#/) 标志

许多人认为移动安全是一个安全的领域，因为它编译了 APK 和 IPA 文件。认为代码是隐藏的和安全的是愚蠢的。自动化工具可以轻松地审查应用程序，并可以集成到开发周期中。它可以节省时间和金钱，同时使应用程序更加安全。只是不要忘记，这些工具可能并且正在被恶意行为者使用。

## 移动安全框架(MobSF)

[移动安全框架(MobSF)](https://mobsf.github.io/docs/#/) 是一个自动化的一体化移动应用程序(Android/iOS/Windows)笔测试、恶意软件分析和安全评估框架，能够执行静态和动态分析。

使用 MobSF 的主要优势之一是，它是一个免费的开源工具，并且托管在本地环境中，因此敏感数据永远不会与云交互。

## Python 版本

如果你不能控制你的 python 版本，设置起来会很麻烦。如果你安装了任何一种 python 版本 2；删除它。我已经确认了 MobSF 可以在 python 3.7 和 python 3.8 上运行。

> [截至 2020 年 1 月 1 日，将不会对 **Python 2** 进行新的错误报告、修复或更改，并且不再支持 **Python 2** 。](https://www.python.org/doc/sunset-python-2/)

## 操作系统

我已经确认 MobSF 可以与 Ubuntu 20.04 和 Windows 10 兼容。在我写这篇文章的时候，它还不能在 macOS 11: Big Sur 上运行。一个可能的解决方案是降级操作系统或者通过 Docker 运行 [MobSF。](https://mobsf.github.io/docs/#/docker)

> 动态分析不可通过 docker 获得

## 静态应用安全测试(SAST)

SAST 通常被称为源代码分析，它是一种分析源代码或二进制代码以发现潜在的安全和质量问题的方法。

通过使用自动化工具，您可以更快地完成通常困难且耗时的任务。这也开启了在开发环境中自动测试代码的可能性。

当您无法访问源代码时，逆向工程是静态分析的主要部分之一(通常称为黑盒测试)。

> 通过使用 MobSF，你可以下载反编译的 Java 和 Smali 代码。

## 动态应用安全测试(DAST)

与 SAST 相比，动态安全测试可以定义为一种更加黑箱化的测试。它将执行程序的功能，以分析程序在使用过程中的功能或行为。

## 自动生成的报告

您可以自动生成 pdf 报告，这对于渗透测试来说是一个方便的工具。

![](img/a4baa0b68bad7bc6427139467bc5bbed.png)

自动生成 SAST 报告 [OWASP 移动安全测试指南(MSTG)](https://owasp.org/www-project-mobile-security-testing-guide/) [不可破解应用](https://github.com/OWASP/owasp-mstg/tree/master/Crackmes)

# 如何获得 MobSF

## 检查要求

确保你有他们的文档页上描述的所有要求。[转到需求](https://mobsf.github.io/docs/#/requirements)

## 装置

文档页面简单描述了安装过程。你可以从这个[转到安装](https://mobsf.github.io/docs/#/installation)

## 奔跑

您可以通过运行以下脚本在安装后运行 MobSF:

对于 Linux/Mac

> 。127.0.0.1:8000

对于 Windows

> run.bat 127.0.0.1:8000

![](img/69227a4491fb5a9f45db848763964e24.png)

运行移动安全框架(MobSF)的终端

现在，您应该能够在浏览器中找到 MobSF，网址为 [http://127.0.0.1:8000/](http://127.0.0.1:8000/) 。您可以通过在此上传您的应用文件来运行静态分析(例如 APK 或 IPA)

![](img/991623ef4ab00064a1a2d63c9b4b32dd.png)

移动安全框架(MobSF)起始页视图

我正在使用 [OWASP 移动安全测试指南(MSTG)](https://owasp.org/www-project-mobile-security-testing-guide/) 不可破解的应用程序来演示静态分析和动态分析。

**静态分析**

![](img/c6f55bbcebd1d2ceb24612c2aa9fb66a.png)

在 [OWASP 移动安全测试指南(MSTG)](https://owasp.org/www-project-mobile-security-testing-guide/) [不可破解应用](https://github.com/OWASP/owasp-mstg/tree/master/Crackmes)上运行静态分析时的视图

**动态分析**

![](img/046aa15652987e95db89d4a334587920.png)

在 [OWASP 移动安全测试指南【MSTG】](https://owasp.org/www-project-mobile-security-testing-guide/)[不可破解应用](https://github.com/OWASP/owasp-mstg/tree/master/Crackmes)上运行动态分析时查看

> 你可以在他们的 [GitHub](https://github.com/OWASP/owasp-mstg/tree/master/Crackmes) 免费获取 [OWASP 移动安全测试指南(MSTG)](https://owasp.org/www-project-mobile-security-testing-guide/) 无法破解的应用。这些应用程序是为破解而设计的。

[](https://github.com/MobSF/Mobile-Security-Framework-MobSF) [## MobSF/移动-安全-框架-MobSF

### 版本:3.3 测试版移动安全框架(MobSF)是一个自动化的一体化移动应用程序…

github.com](https://github.com/MobSF/Mobile-Security-Framework-MobSF)  [## 移动安全框架- MobSF 文档

### 移动安全框架- MobSF 是一个自动化的一体化移动应用程序(Android/iOS/Windows)笔测试…

mobsf.github.io](https://mobsf.github.io/docs/#/)