# OpenEMR 5.0.1.3—(经过身份验证的)任意文件操作

> 原文：<https://infosecwriteups.com/openemr-5-0-1-3-authenticated-arbitrary-file-actions-f7006e636b8c?source=collection_archive---------3----------------------->

![](img/75703256fcce4f9a83d2eabdcee637df.png)

回到 2018 年，我和一群安全研究人员决定在 OpenEMR 上一试身手，寻找安全漏洞。完整报告可在[这里](https://www.open-emr.org/wiki/images/1/11/Openemr_insecurity.pdf)找到。这是一本非常好的读物，我推荐完整阅读。然而这篇博文只是记录了我对这个项目的贡献。以下是我在合作中收到的三份简历。这些都是负责任地披露和修补，所以升级到最新版本将是明智的。

1.CVE-2018–15140-验证任意读取

易受攻击的代码:

```
if ($_POST['mode'] == 'get'){
     echo file_get_contents($_POST['docid']);
     exit;
  }
```

这是一个漏洞，使得攻击者能够在未打补丁的 OpenEMR 实例上向/portal/import_template.php 发出恶意请求。该请求的结果是对位于文件系统上的本地文件的任意文件读取。如果将参数模式设置为 get 作为其值，则由于应用程序将用户输入传递到 file_get_contents()而不进行任何清理，因此可能存在此漏洞。这个输入的结果是本地文件，然后在 html 响应中回显。

2.CVE-2018–15142-验证任意写入

易受攻击的代码:

```
} else if ($_POST['mode'] == 'save') {
    file_put_contents($_POST['docid'], $_POST['content']);
    exit(true);
}
```

这是/portal/import_template.php 中的一个漏洞，使得攻击者能够将 php 文件写入本地文件系统。如果参数模式设置为保存，则此操作有效。如果是这种情况，post 参数 docid 和 content 将被传递给 file_put_contents()，而不进行任何清理。docid 是文件名，内容包括恶意 php 代码。这本身并没有太大的影响，因为您无法执行该文件，但当与以前发现的任意文件读取配对时，会导致远程代码执行。

3.CVE-2018–15141-已验证的任意文件删除

易受攻击的代码:

```
} else if ($_POST['mode'] == 'delete') {
     unlink($_POST['docid']);
     exit(true);
 }
```

这也是/portal/import_template.php 中的一个漏洞，使得攻击者能够删除系统中的任何文件，如果文件名已知并且允许删除的权限。当 post 参数模式设置为删除时，这是可能的。然后将包含攻击者指定的文件名的 docid 参数传递给 unlink()，而不进行清理。

谢谢你的阅读，希望你喜欢。您可以在 ExploitDB、[这里](https://www.exploit-db.com/exploits/45202)找到其中任何一个的概念证明。你也可以在这里或者我的博客 [https://jsecu.github.io](https://jsecu.github.io) 上阅读 CTF 和 bug bounty 的文章。如果您有任何问题，请随时在 Twitter 上给我发消息，地址是@Pullerze。更多的 Bug 奖励和安全报道即将推出！