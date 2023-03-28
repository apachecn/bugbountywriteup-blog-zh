# 分解-命令注入

> 原文：<https://infosecwriteups.com/breaking-down-command-injections-97d1029576?source=collection_archive---------0----------------------->

命令注入或操作系统命令注入是注入漏洞的一种，攻击者能够利用未经整理的用户输入进一步在服务器中运行默认的操作系统命令。

> **代码注入:**允许攻击者添加他们自己的代码，然后由应用程序执行。
> 
> **命令注入:**攻击者扩展应用程序的默认功能，应用程序执行系统命令，而不注入代码。

![](img/52b785d75ee3393ea6d8ee9afb5109e4.png)

## 根据 OWASP 的说法，什么是命令注入攻击？

命令注入是一种攻击，其目标是通过易受攻击的应用程序在主机操作系统上执行任意命令。当应用程序传递不安全的用户提供的数据(表单、cookies、HTTP 头等)时，命令注入攻击是可能的。)到系统外壳。在这种攻击中，攻击者提供的操作系统命令通常以易受攻击的应用程序的权限执行。命令注入攻击很大程度上是由于输入验证不充分造成的。

## 在应用程序源代码中识别配置项:OWASP

1.  在基于 PHP 的应用程序中:易受攻击的代码

```
<?php
print("Specify the file to delete");
print("<p>");
$file=$_GET['filename'];
system("rm $file");
?>
```

在上面的代码片段中，应用程序向用户请求一个`filename`值，该值被直接提供给`system`命令以供进一步执行，而没有对`filename`参数中的值进行清理或转义。

因此，攻击者可以修改请求来运行系统命令，如下面的请求中的`id`:

**请求**

```
http://127.0.0.1/delete.php?filename=bob.txt;id
```

因此，攻击者可以通过他修改后的请求提取`id`的私有值，如下响应所示:

**响应**

```
Please specify the name of the file to deleteuid=33(www-data) gid=33(www-data) groups=33(www-data)
```

2.类似地，在基于 **Perl** 的应用程序中:易受攻击的代码
`use CGI qw(:standard);
$name = param(‘name’);
$nslookup = “/path/to/nslookup”;
print header;
if (open($fh, “$nslookup $name|”)) {
while (<$fh>) {
print escapeHTML($_);
print “<br>\n”;
}
close($fh);
}`
假设攻击者提供了这样一个域名:

`cwe.mitre.org%20%3B%20/bin/ls%20-l`-
攻击码将“%3B”序列解码为“；”字符，而%20 解码成一个*空格。*`open()`语句将处理如下字符串:
`/path/to/nslookup cwe.mitre.org ; /bin/ls -l`
结果，攻击者执行“`/bin/ls -l`”命令并获得程序工作目录——CWE 中所有文件的列表。

3.在基于 Java 的应用程序中:易受攻击的代码

以下代码从系统属性中读取要执行的*外壳脚本*的名称。它受制于 OS 命令注入的第二个变体。
`String script = System.getProperty(“SCRIPTNAME”);
if (script != null)
System.exec(script);`

## 作为攻击者你能做什么？

攻击者可以上传恶意程序或文件、获取密码、获得后门访问权限、提升权限、执行意外命令、危害服务器上的任何敏感文件，或者将攻击升级到网络接口。

## 利用命令注入的步骤:

1.  使用`ping`命令通过使服务器 *ping* 其环回接口一段特定时间来**触发时间延迟**。
2.  如果没有完成过滤，在平台(Windows 或 Unix)上引起一个 *30 秒*的时间延迟:

> `|| ping -i 30 127.0.0.1 ; x || ping -n 30 127.0.0.1`

3.**监控应用程序响应所用的时间**，针对每个诱导的时间延迟:

> `| ping –i 30 127.0.0.1 |
> | ping –n 30 127.0.0.1 |
> & ping –i 30 127.0.0.1 &
> & ping –n 30 127.0.0.1 &
> ; ping 127.0.0.1 ;
> %0a ping –i 30 127.0.0.1 %0a
> ` ping 127.0.0.1 ``

4.如果出现时间延迟，应用程序**可能容易受到**命令注入的影响。

5.多次重复测试案例，以确认延迟是由“**网络延迟**或其他异常”导致的**而非**。

6.尝试更改`*-n*` 或`*-i*` 参数的值，并确认所经历的**延迟随着所提供的值系统地变化**。

1.  如果成功，尝试注入类似`ls`或`dir`的命令。检查您是否可以**将命令的结果检索到您的浏览器**。
2.  如果您无法直接检索结果:尝试**打开一个带外通道返回到您的计算机**。尝试使用*将工具复制到服务器，使用`telnet` 或`netcat`创建一个反向 shell 返回到您的计算机，并使用 mail 命令通过 ***SMTP*** 发送命令输出。*
3.  *您可以**将命令的结果重定向到 *webroot* 中的一个文件**，然后您可以使用浏览器直接检索该文件。*

*例如:`dir > c:\inetpub\wwwroot\foo.txt`*

*4.当你找到了注入命令和检索结果的方法时，**决定你的特权**级别；使用— `whoami`或尝试将无害文件写入受保护的目录。*

*5.然后，您可能会寻求**提升权限，获得对敏感应用程序数据的后门访问，或者攻击从受损服务器可到达的其他主机。***

## *如何识别 WebApps 中的命令注入漏洞？*

*在 URL 中显示文件名的 web 应用程序中。*

***Perl** —将管道符号`|`追加到文件名的末尾。*

*修改前的网址:
`http://sensitive/cgi-bin/userData.pl?doc=profile.txt`
修改后的网址:
`http://sensitive/cgi-bin/userData.pl?doc=/bin/ls|`
这将执行`/bin/ls`命令。*

***PHP** —在 URL 的末尾附加一个分号`;`，后跟一个操作系统命令。；在 URL 编码中是%3B。*

*网址修改:
`http://sensitive/something.php?dir=%3Bcat%20/etc/passwd`*

*![](img/b9a8e6c41488558659fcae2eb5dcf695.png)*

## *理解特殊字符在命令注入中的作用*

*将特殊字符与用户输入结合起来，允许您修改或转移应用程序以执行非预期的操作。在命令注入中，执行命令 2 的请求由攻击者动态排序。以下特殊字符可用于命令注入，如`|``;``&``$``>``<``'``!`*

> *`cmd1|cmd2`:使用`|`将使命令 2 的执行与命令 1 是否执行无关。*
> 
> *`cmd1;cmd2`:使用`;`将使命令 2 的执行与命令 1 是否执行无关。*
> 
> *`cmd1||cmd2`:只有当命令 1 执行失败时，才会执行命令 2。*
> 
> *`cmd1&&cmd2`:只有命令 1 执行成功，才会执行命令 2。*
> 
> *`$(cmd)`:例如`echo $(whoami)`或`$(touch test.sh; echo ‘ls’ > test.sh)`
> **cmd** :用于执行特定的命令。比如 whoami
> *

## *如何绕过缓解措施？*

*尝试使用如下所示的一些字符来绕过应用程序进行的检查或转义。*

*对于*命令注入*试字符或字符组合如下图来测试应用程序所实现的防御:`|``;``&``$``>``<``'``\``!``>>``#`*

*转义或过滤特殊字符为***【Windows】*`)``<``>``&``*``‘``|``=``?``;``[``]``^``~``!``.``”``%``@``/``\``:``+``,````

*转义或过滤特殊字符为*`{``}``(``)``>``<``&``*``‘``|``=``?``;``[``]``$``–``#``~``!``.``%``/``\``:``+``,````

## **进一步开发**

**敏锐地发现更容易受到命令注入攻击的系统命令。检查是否可以直接运行这些命令。**

****Java-**`Runtime.exec()`
**C/c++-**
`system
exec
ShellExecute`
**Python-**
`exec
eval
os.system
os.popen
subprocess.popen
subprocess.call`
**PHP-**
`system
shell_exec
exec
proc_open
eval`**

**![](img/324a216c2b345082e60c222ed1baf3d3.png)**

**再见。**

## ****参考文献:****

**[](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/12-Testing_for_Command_Injection) [## WSTG -最新

### 本文描述了如何测试应用程序的操作系统命令注入。测试人员将尝试注入操作系统命令…

owasp.org](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/12-Testing_for_Command_Injection) [](https://www.amazon.in/Web-Application-Hackers-Handbook-Exploiting-ebook/dp/B005LVQA9S) [## 网络应用黑客手册:寻找和利用安全漏洞

### 网络应用黑客手册:寻找和利用安全漏洞电子书

www .亚马逊. in](https://www.amazon.in/Web-Application-Hackers-Handbook-Exploiting-ebook/dp/B005LVQA9S)**