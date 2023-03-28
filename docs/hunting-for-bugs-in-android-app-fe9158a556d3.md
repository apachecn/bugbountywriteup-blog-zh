# 在该死的不安全和易受攻击的应用程序中寻找漏洞

> 原文：<https://infosecwriteups.com/hunting-for-bugs-in-android-app-fe9158a556d3?source=collection_archive---------0----------------------->

## DIVA 安卓应用

该死的不安全易受攻击的应用 DIVA *是一个易受攻击的应用，旨在教授 Android 应用中发现的漏洞。这篇文章将带你发现其中的一些漏洞，并将随着我完成更多与 android 相关的挑战而不断更新。*

![](img/0b33273be4a75bf57fbcc505796787c9.png)

[https://pentesttools.net](https://pentesttools.net)

**第一步。**此处提取[中的 DIVA APK 文件。](http://www.payatu.com/wp-content/uploads/2016/01/diva-beta.tar.gz)

**第二步。**如果你想使用 android Studio 软件建立 Android 实验室，请点击这里访问我之前的文章[。](https://medium.com/bugbountywriteup/apk-testing-on-an-android-studio-c0addb12550)

**步骤三。**一旦你在模拟器上运行了 Diva 应用程序，如果你想查看 java 格式的源代码，就在你的 Mac 或 linux 终端上运行`jadx-gui`。

**第四步。让我们开始吧！**

![](img/214b9b6e948c3f78c17ebc772badf9cd.png)

这是我为这个 DIVA 应用程序使用的一个根 android 模拟器。

![](img/952de9743e3455ea260af0a9ac9b7e25.png)

# 1.不安全日志记录

![](img/2d0ba34e3cdf59ab7f5922d862787926.png)

在 Android Studio 终端中，访问`adb` 命令的绝对路径:

`cd ~/Library/Android/sdk/platform-tools`

现在启动设备模拟器外壳:`./adb shell`

运行`ps`命令，我可以看到 **jakhar.aseem.diva** 的 **pid** 也就是 **18976**

![](img/54f9bf8b1cffcbeffce81367b82c8f6a.png)

现在，要仅查看 diva 进程的日志，请运行以下命令:

`logcat | grep 18976`或者你可以简单地运行`./adb logcat`，这将是非常嘈杂的。

![](img/656215e2764b00f347bd48bf68874080.png)

正如我们所见，此应用程序正在记录敏感信息，如果其他应用程序拥有此设备日志的读取权限，则可以访问这些信息。

# 2.硬编码问题-第一部分

![](img/fc63c05c71e8aa823215cce3de1b56aa.png)

使用`jadx-gui`，我可以查看 Java 格式的 apk 源代码。注意这里的硬编码访问键。

![](img/ffef68107090bdd872ee652d5218cf67.png)![](img/a769c880c5aaeafddef97fb0d510dae7.png)

# 3.不安全的数据存储第一部分

*需要根设备*

![](img/21839f5df804cd1c73f503e343c63c73.png)

在这个应用程序中，我们可以看到输入的字段细节已经保存。

在 Android Studio 终端上启动一个根虚拟设备外壳，如下图所示，并访问保存该凭证的 **/data/data** 文件夹。

![](img/eeefbafd55381fd68e6d34206382340d.png)

该凭证存储在 **shared_prefs** 目录下的中。我知道这一点的原因是因为在它的源代码中(在 Jadx-gui 中),我可以看到保存凭证的地方也有源代码中提到的 SharedPreferences。

![](img/94b3755096a7a99b796d5cb6daf0c847.png)![](img/b91952f09aa1f0bd44521d7ee28397f1.png)![](img/710800f89a0ccea9f27fbe7e7b16464f.png)

# 4.不安全的数据存储第二部分

![](img/f47df9ae0017ccaff8d9799424d0ac02.png)

对于第二部分，源代码表明这次凭证存储在 SQL 数据库中。

![](img/6c9b76bc9c45f469dc383971ec5a529c.png)

在数据库中有 4 个文件。在 **ids2** 文件内容中找到了凭证。

![](img/607ed860e2c7fbe0be5472e61ce7f35f.png)![](img/0408c71a8cf93b96d660202f895805a1.png)

# 5.不安全的数据存储第三部分

![](img/5596ca8625b6acad70c530b8a360bcad.png)

保存了左图所示的凭证后，我查看了 java 源代码。

正如我们在下图中看到的，创建了一个临时文件来保存凭据。

临时文件是在/data/data/jakhar.aseem.diva 目录下创建的。

![](img/53eeb54e0525054449a4f6c5cdc89b82.png)![](img/c004127280953531ecf23e38c73fe577.png)

# 6.不安全的数据存储第四部分

![](img/d587cd767d23f07f056398d73c5b4e89.png)

在此任务中，当我试图保存我的凭据时，它显示“出现文件错误”。

查看源代码，注意应用程序正在尝试将凭证存储在设备外部存储中。所以在**设置>应用权限>存储>天后**下检查存储权限并授予访问权限

![](img/a019b4b76debe4bc13653bb0cf5d8e9f.png)![](img/10fc57496f1febd959cafd3bc0ee7f06.png)

在允许存储权限为 Diva 后，我再次尝试保存凭据，这次成功了！

现在，在终端中，您可以看到凭证保存在 **/sdcard/.uinfo.txt** 中

![](img/7f7ba4bcedca3afa3a8081cdbdd7b4dd.png)![](img/6af6ca91ee7efd547e022dda2531ad5c.png)

# 7.输入验证问题-第一部分

![](img/b09a828cc54b6c0e7b1c42f32ec9c37c.png)

该应用程序要求输入有效的用户名。如果输入的用户名是正确的，然后应用程序显示用户名密码和信用卡号码。

由于存在输入验证问题，我尝试了一个简单的 SQL 查询来显示用户凭证，如下所示。

# 8.输入验证问题-第二部分

![](img/5827b4a959cf9c670662c120bfcd0c89.png)![](img/c516fced26a4b67d6a5f9f45d295c563.png)

在这里，我首先访问了一个 web URL，看看它是否有效。接下来，我使用 **file:/ protocol** 来访问这个设备中的文件，我能够从不同的位置检索所有敏感信息。

![](img/edc51981c79844aab71b16a4c14c77b3.png)![](img/1ea4fcff098053b38e7aef5ec9bb124c.png)![](img/a579e6bb173f4ad3d74869bcc021ebb6.png)

# 9.访问控制问题-第一部分

![](img/44f87e647082df4e83a66fb45fd4ddd7.png)![](img/7f6bc0faf3412795bbd9757affbaa859.png)

可以通过单击“查看 API 凭据”来查看 API 凭据。挑战在于从应用程序外部访问 API 凭证。

![](img/0d5dba4a78e116ae05945dc9b68504d3.png)

运行`logcat`查看点击“查看 API 凭证”按钮后会发生什么。我们可以看到这里显示的活动管理器名称和操作。

![](img/5dc2e881415674edb3e02404a1b45fc3.png)

现在在 adb shell 中运行以下命令。这将打开应用程序并显示 API 凭证。

`am start jakhar.aseem.diva/.APICredsActivity`

![](img/7a3d025c590c99d6fd24e52b512fcdab.png)![](img/260b799eb8be08f35526834cdabdc882.png)

# 10.访问控制问题-第二部分

![](img/cbdd058b887c0ec1bb49abe680bcc6af.png)

这里，我们需要在不知道 PIN 的情况下从应用程序外部访问 API 凭证(向应用程序注册)。

点击**“已注册”**为我们提供 API 凭证、用户名和密码。

点击**“立即注册”**要求我们输入 pin。现在检查`logcat`输出，以便进一步研究。

![](img/4fa67b95802c7d89a35ba2a10668e062.png)![](img/b844fef36118ed5bb4fafc8cc42eb2ba.png)![](img/f7c91700ca245b055511f1175e439207.png)

注意 chk_pin 的**实际值**是 **check_pin**

接下来，我们需要禁用 PIN 来绕过这个要求并查看 API 凭证。

从`logcat`我们知道活动经理是**jakhar.aseem.diva/.APICreds2Activity**

`./adb shell am start -n jakhar.aseem.diva/.APICreds2Activity --ez check_pin false`

*- n 输入要进行的活动名称*

*- ez 是<数据> <布尔>*

![](img/f87d8a71ee0267621f433f97d12ea0c2.png)

现在检查你的 android 模拟器，你会发现应用程序已经打开了 API 证书，不需要 PIN。

![](img/6cc2d1201d504af1546bb947ff05a6f7.png)

# 11.访问控制问题-第三部分

![](img/577e0044be06ef43319e2b3435e34369.png)

该应用程序要求您创建一个 PIN 码，然后可以用来访问私人笔记。

使用密码，我可以进入私人笔记。

查看。xml 和。java 源代码文件查找代码缺陷:

*   AndroidManifest.xml
*   访问控制 3 活动
*   访问控制 3 无活动
*   notes 提供者

![](img/1442ac89516efcc3784b47861df78cd2.png)

从`logcat`我们可以看出活动经理是**jakhar.aseem.diva/.AccessControl3Activity**

![](img/c9718c01f7cd753a772bb2bd5a72ea56.png)

AndroidManifest.xml 显示内容提供者**JAK har . aseem . diva . provider . notes provider；android:enabled="true "和 android:exported="true"** 这意味着其他应用程序的组件可以访问它。

NotesProvider.java 源代码揭示了笔记保存的位置。

**CONTENT _ URI = uri . parse(" CONTENT://JAK har . aseem . diva . provider . notes provider/notes ")；**

![](img/1da788a29fe0fcaeff0b75b4bbaaa8c4.png)![](img/ddb51c211b89a26406dab3336a9d16aa.png)

运行下面的命令让我能够在 android studio 终端中访问笔记。

`./adb shell content query --uri content://jakhar.aseem.diva.provider.notesprovider/notes`

![](img/b6391c28281ff87ba2dc4b01f3670330.png)

# 12.硬编码问题-第二部分

![](img/533319270921b14a059724323a3e5d29.png)

*对于这个活动，我想使用逆向工程工具，而不是使用* `*jadx-gui*` *，因为它只将 APK 的 dex 文件反编译成 java 源代码。分析这些源代码需要查看库。所以)哪些* `*jadx-gui*` *的文件不支持。*

![](img/41d816ba5895fcb32bbdeab260cb70c6.png)![](img/a2a787e30eaa2fa6975e79b278a5d585.png)![](img/64767d5b791141fa12029a7515305805.png)

一旦我们理解了源代码，就很容易找到供应商密钥。这里有两种不同的方法。

*   **使用 Ghidra(见以下链接)**

[](https://medium.com/bugbountywriteup/how-to-use-ghidra-to-reverse-engineer-mobile-application-c2c89dc5b9aa) [## 如何使用 Ghidra 对移动应用进行逆向工程

### 揭开

medium.com](https://medium.com/bugbountywriteup/how-to-use-ghidra-to-reverse-engineer-mobile-application-c2c89dc5b9aa) 

*   **使用 apktool**

![](img/4d459a09e5d9df8299efcc871e57ef2a.png)

通过运行以下命令，从 linux 终端提取 diva-beta.apk 内容:

`apktool d diva-beta.apk`

接下来，查看 **libdivajni.so** 文件的内容，注意任何可疑的文本，并将其输入到用户输入字段中，看看是否工作正常。

`strings arm64-v8a/libdivajni.so`

![](img/353e0b4ddc6c010d32478b7878fd82f0.png)