# Python 源代码分析

> 原文：<https://infosecwriteups.com/python-source-code-analysis-53addcb3894?source=collection_archive---------1----------------------->

您可以通过下面的链接访问用 Python 编写的易受攻击的 web 应用程序。

![](img/0fa6cb7345d31763b635b04849ffb95a.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Hitesh Choudhary](https://unsplash.com/@hiteshchoudhary?utm_source=medium&utm_medium=referral) 拍摄的照片

[](https://owasp.org/www-project-vulnerable-flask-app/) [## OWASP 易损烧瓶应用程序

### erlik 2-Vulnerable-Flask-App Tested-Kali 2022.1 它是一个易受攻击的 Flask Web App。这是一个创建的实验室环境…

owasp.org](https://owasp.org/www-project-vulnerable-flask-app/) [](https://github.com/anil-yelken/Vulnerable-Flask-App/) [## GitHub-Anil-yelken/Vulnerable-Flask-App:Erlik 2-Vulnerable-Flask-App

### erlik 2-Vulnerable-Flask-App Tested-Kali 2022.1 它是一个易受攻击的 Flask Web App。这是一个创建的实验室环境…

github.com](https://github.com/anil-yelken/Vulnerable-Flask-App/) [](https://github.com/anil-yelken/Vulnerable-Soap-Service/) [## GitHub-Anil-yelken/Vulnerable-Soap-Service:Erlik-Vulnerable Soap Service

### Erlik -易受攻击的 Soap 服务已测试- Kali 2022.1 这是一个易受攻击的 SOAP web 服务。这是一个实验室环境…

github.com](https://github.com/anil-yelken/Vulnerable-Soap-Service/) 

# SQL 注入

发生 SQL 注入漏洞是因为没有使用参数查询:

cur . execute(" select * from test where username = ' % s ' " % name)

# XSS/HTML 注入

XSS 和 HTML 注入漏洞是由于在没有输入控制的情况下将输出与“Welcome”语句结合起来进行屏幕抑制而导致的。

```
@app.route("/welcome2/<string:name>")
def welcome2(name):
    data="Welcome "+name
    return data
```

# SSTI

SSTI 漏洞的发生是因为这个人没有检查他写的模板中的输入:

```
@app.route("/welcome2/<string:name>")
def welcome2(name):
    data="Welcome "+name
    return data@app.route("/hello")
def hello_ssti():
    if request.args.get('name'):
        name = request.args.get('name')
        template = f'''<div>
        <h1>Hello</h1>
        {name}
</div>
'''
		return render_template_string(template)
```

# 命令注入

在命令注入漏洞中，从用户接收的输入在没有任何控制的情况下由子流程模块运行。

```
@app.route("/get_users")
def get_users():
    try:
        hostname = request.args.get('hostname')
        command = "dig " + hostname
        data = subprocess.check_output(command, shell=True)
        return data
    except:
        data = str(hostname) + " username didn't found"
        return data@rpc( _returns=String)
    def get_log(ctx):
        try:
            command="cat soap_server.log"
            data=subprocess.check_output(command,shell=True)
            return(str(data))
        except:
            return("Command didn't run")
```

# 信息披露

因为应用程序中的每个事务都被记录，所以关键信息将出现在日志中。

```
@app.route("/get_log/")
def get_log():
    try:
        command="cat restapi.log"
        data=subprocess.check_output(command,shell=True)
        return data
    except:
    	pass@rpc( _returns=String)
    def get_log(ctx):
        try:
            command="cat soap_server.log"
            data=subprocess.check_output(command,shell=True)
            return(str(data))
        except:
            return("Command didn't run")
```

# LFI

由于 GET 方法没有提供对使用 filename 参数接收的输入的控制，因此会读取系统中的文件，从而出现 LFI 漏洞。

```
@app.route("/read_file")
def read_file():
    filename = request.args.get('filename')
    file = open(filename, "r")
    data = file.read()
    file.close()
    return jsonify(data=data),200@rpc(String, _returns=String)
    def read_file(ctx,file):
        file = open(file, "r")
        data = file.read()
        file.close()
        return(data)
```

# 荒漠化

对于 Python 的反序列化漏洞，可以在源代码中搜索 pickle.loads 语句。

data = pickle . loads(received _ data)

# 磁盘操作系统

使用 regex 搜索使用 GET 方法从用户处获得的用户名和密码信息时，会出现 DOS 漏洞。

```
@app.route("/user_pass_control")
def user_pass_control():
    import re
    username=request.form.get("username")
    password=request.form.get("password")
    if re.search(username,password):
        return jsonify(data="Password include username"), 200
    else:
        return jsonify(data="Password doesn't include username"), 200
```

# 文件上传

因为从用户接收的文件没有大小、扩展名、内容类型控制，所以所选择的文件被直接上传到系统。

```
@app.route('/upload', methods = ['GET','POST'])
def uploadfile():
   import os
   if request.method == 'POST':
      f = request.files['file']
      filename=secure_filename(f.filename)
      f.save(os.path.join(app.config['UPLOAD_FOLDER'], filename))
      return 'File uploaded successfully'
   else:
      return '''
<html>
   <body>
      <form  method = "POST"  enctype = "multipart/form-data">
         <input type = "file" name = "file" />
         <input type = "submit"/>
      </form>   
   </body>
</html>
      '''
```

# 日志的不正确输出中和

利用日志的不正确输出抑制漏洞，攻击者可以了解任何数据都可以写入日志，并可以注入恶意代码或导致日志显示不正确。

```
@app.route('/logs')
def ImproperOutputNeutralizationforLogs():
    data = request.args.get('data')
    import logging
    logging.basicConfig(filename="restapi.log", filemode='w', level=logging.DEBUG)
    logging.debug(data)
    return jsonify(data="Logging ok"), 200
```

[](https://github.com/anil-yelken/python-source-code-analysis/blob/main/README.md) [## python-source-code-analysis/readme . MD 位于 main Anil-yelken/python-source-code-analysis

### 您可以通过下面的链接访问用 Python 编写的易受攻击的 web 应用程序。OWASP 易损烧瓶应用程序…

github.com](https://github.com/anil-yelken/python-source-code-analysis/blob/main/README.md) [](https://github.com/anil-yelken) [## 阿尼尔-耶尔肯-概述

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/anil-yelken) 

## 来自 Infosec 的报道:Infosec 每天都有很多内容，很难跟上。[加入我们的每周简讯](https://weekly.infosecwriteups.com/)以 5 篇文章、4 条线索、3 个视频、2 个 GitHub Repos 和工具以及 1 个工作提醒的形式免费获取所有最新的 Infosec 趋势！