# SQLMap-eval 的奥秘

> 原文：<https://infosecwriteups.com/the-mystery-of-sqlmaps-eval-f6c7bf43e1f?source=collection_archive---------4----------------------->

掌握利用最复杂的 SQL 注入的能力

![](img/b40961126e2ded673c53dd17587bd390.png)

有时候你需要四种元素的力量来自动开发 SQL 注入。
那是`--eval`给你的，下面是如何接管这个权力。

根据具体情况，您可能需要修改请求的不同部分。
`SQLMap`使用 python 的`exec()`方法来执行你的脚本。该工具将请求的不同部分作为局部变量传递给该方法。这样，您可以访问和修改它们。

请求的 post 主体可以是不同的数据类型，比如 JSON、XML 等。为了提供对每种类型元素的访问，`SQLMap`通过[这些正则表达式](https://github.com/sqlmapproject/sqlmap/blob/master/lib/core/settings.py#L835)来识别类型。然后询问您是否希望该工具处理数据:
`JSON/XML/... data found in POST/PUT/... body. Do you want to process it?`
显然，如果您希望一切按预期运行，您需要回答是。

# 访问请求的不同部分

## 上呼吸道感染

没有查询字符串的请求的 URI 可以通过`uri`参数访问。
如果用户在 URL 的*路径*中指定了一个定制的注入点，那么`uri`参数也将包含 URL 编码的注入负载。

**举例:**
*命令:* `sqlmap -u “http://example.com/path?id=123"`
*局部变量:* `uri --> http://example.com/path`

*命令:* `sqlmap -u “http://example.com/path*?id=123"`
*局部变量:* `uri --> http://example.com/path<URL-ENCODED PAYLOAD>`

## 获取查询

每个 GET 查询参数的值，加上注入的有效负载(如果有的话)，都可以通过带有原始 GET 参数名称的局部变量来访问。
**示例:**
*命令:* `sqlmap -u "http://example.com/path?param1=123&param2=456&class.id=789"`
*局部变量:*
`param1 --> 123`
`param2 --> 456`
`EVAL_636c6173732e6964 --> 789`(阅读下面的注释)

**注意:**如果参数名包含特殊字符或者是 python 中的保留关键字，在将变量传递给`exec()`方法之前，`SQLMap`根据以下模式更改其名称:
`EVAL_<HEX OF ORIGINAL NAME>`
它适用于传递给`exec()`方法的所有变量。
包括 cookies 和 POST 数据。

## 饼干

`Cookie`头的内容，加上注入的有效载荷(如果有的话)，可以通过`cookie`变量访问。
*命令:* `sqlmap -u "http://example.com/path?id=123" --cookie=”C1=123; C2=456"`
*局部变量:* `cookie --> C1=123; C2=456`

## 头球

与请求头相关的`exec()`方法中有两个字典:
`_locals['headers']`
`_locals['auxHeaders']`
您可以通过`headers`字典访问请求头，但是修改它不会影响请求。
要修改最终请求的头，你需要`update()`字典。
默认情况下`auxHeaders`的值为`None`。这意味着您需要将所有的头——无论修改与否——添加到`auxHeaders`字典中。
**示例:**
假设您想要修改`User-Agent`头的值，并添加一个自定义认证头`X-Auth`。
这可以是代码:

```
# _locals['auxHeaders'] is None here
_locals['headers']['User-Agent'] = "pentesting, no worries!!"
_locals['headers']['X-Auth'] = "My_Secret_Token"
_locals['auxHeaders'].update(_locals['headers'])
```

## 帖子正文

`SQLMap`使用正则表达式识别六种类型的发布数据:

JSON
JSON 类( [regex](https://github.com/sqlmapproject/sqlmap/blob/master/lib/core/settings.py#L842) )
XML
多部分
数组类( [regex](https://github.com/sqlmapproject/sqlmap/blob/master/lib/core/settings.py#L848) )
基于表单

要了解类似 JSON 和类似 array 的数据类型，请参考识别它们的正则表达式。

在写这篇文章的时候，`SQLMap`只解析`form-based`和`json`数据类型。
对于其他数据类型，你没有访问元素的值。

但是您仍然可以通过声明一个与元素名称同名的变量来修改元素的值。`SQLMap`使用 [a regex](https://github.com/sqlmapproject/sqlmap/blob/master/lib/request/connect.py#L1399) 将原始值替换为新值。
注意，这个实现有数据类型`array-like`的问题，但是你可以在这里找到解决方案[。](https://github.com/sqlmapproject/sqlmap/pull/5013#issuecomment-1061416373)

## 有效载荷

`SQLMap`注入到请求中的 SQL 负载可以通过`_locals['payload']`变量访问。

## 长脚本

当你要执行的代码是几行时，可以在终端:
`sqlmap ... --eval="#first_line; #second_line; #third_line"`中添加脚本作为`--eval`的值

但是如果您的脚本较长，那么在终端中编写整个脚本并不是一个好主意。相反，您可以在一个`.py`文件中编写您的代码，并将其作为一个模块导入:
`sqlmap ... --eval="import myModule; #use_functions_of_myModule"`

## 排除故障

如果你想在`SQLMap`内部调试你的代码或者想熟悉环境，你可以使用`ipdp`模块。它给你一个调试外壳，你可以四处移动，看看发生了什么。`locals()`方法可以是一个很好的起点。
`sqlmap ... --eval=”import ipdb; ipdb.set_trace()”`

## 新的实现

如您所见，对于`--eval`标志还有改进的空间。
所以我重写了`--eval`标志的[代码](https://github.com/sqlmapproject/sqlmap/blob/master/lib/request/connect.py#L1274)。在这个新的实现中，以统一的方式支持所有数据类型。另外，我将原始的`GET` / `POST`数据作为局部变量访问。因此，如果用户需要修改请求，而*已经本地存在的变量*没有用，他可以使用原始数据并以他需要的方式解析它。

**文档:** 用户可以访问这些字典作为本地变量:

`headers`:所有的请求头。修改这个字典*将*影响请求。
`get_data`:已处理的获取查询数据
`post_data`:已处理的发文主体。无论数据类型是什么，用户都可以访问**该数据类型的所有**元素和属性。
`get_query`:原始获取查询数据
`post_body`:原始发文体数据

**注意**:修改`get_query`或`post_body`会覆盖对`get_data`和`post_data`所做的相应更改。

**代号:**

```
if conf.evalCode:
            delimiter = conf.paramDel or DEFAULT_GET_POST_DELIMITER
            variables = {"uri": uri, "get_query": get, "headers": headers, "post_body": post, "get_data": {}, "post_data": {}, "lastPage": threadData.lastPage, "_locals": locals()}
            original_get = get
            original_post = post
            if get:
                for part in get.split(delimiter):
                    if '=' in part:
                        name, value = part.split('=', 1)
                        name = name.strip()
                        value = urldecode(value, convall=True)
                        variables['get_data'][name] = value
            if kb.postHint:
                if kb.postHint in (POST_HINT.XML, POST_HINT.SOAP):
                    variables['post_data'] = xmltodict.parse(post)
                if kb.postHint == POST_HINT.JSON:
                    variables['post_data'] = json.loads(post)
                if kb.postHint == POST_HINT.JSON_LIKE:
                    if json_like_type == 3:
                        post = re.sub(r'(,|\{)\s*([^\'\s{,]+)\s*:', '\g<1>"\g<2>":', post)
                    if json_like_type == 4:
                        post = re.sub(r'(,|\{)\s*([^\'\s{,]+)\s*:', "\g<1>'\g<2>':", post)
                    if json_like_type in (2, 4):
                        post = post.replace("\\'", REPLACEMENT_MARKER).replace('\"','\\"').replace("'",'"').replace(REPLACEMENT_MARKER, "'")
                    variables['post_data'] = json.loads(post)
                if kb.postHint == POST_HINT.MULTIPART:
                    multipart = MultipartDecoder(bytes(post, 'utf-8'), contentType)
                    boundary = '--' + multipart.boundary.decode('utf-8')
                    for part in multipart.parts:
                        name = re.search(r'"([^\"]*)"', part.headers._store[b'content-disposition'][1].decode('utf-8')).group(1)
                        value = part.text
                        variables['post_data'][name] = value
                if kb.postHint == POST_HINT.ARRAY_LIKE:
                    post = re.sub(r"\A%s" % delimiter, "", post)
                    array_name = re.findall(r"%s(.*?)\[\]=" % delimiter, post)[0].strip()
                    variables['post_data'] = []
                    for value in post.split("%s[]=" % array_name)[1:]:
                        variables['post_data'].append(value.replace(delimiter, ""))
            elif post:
                for part in post.split(delimiter):
                    if '=' in part:
                        name, value = part.split('=', 1)
                        name = name.strip()
                        value = urldecode(value, convall=True, spaceplus=kb.postSpaceToPlus)
                        variables['post_data'][name] = valueevaluateCode(conf.evalCode, variables)if kb.postHint:
                if kb.postHint in (POST_HINT.XML, POST_HINT.SOAP):
                    post = xmltodict.unparse(variables['post_data'])
                if kb.postHint == POST_HINT.JSON:
                    post = json.dumps(variables['post_data'])
                if kb.postHint == POST_HINT.JSON_LIKE:
                    post = json.dumps(variables['post_data'])
                    if json_like_type in (3, 4):
                        post = re.sub(r'"([^"]+)":', '\g<1>:', post)
                    if json_like_type in (2, 4):
                        post = post.replace('\\"', REPLACEMENT_MARKER).replace("'", "\\'").replace('"', "'").replace(REPLACEMENT_MARKER, '"')
                if kb.postHint == POST_HINT.MULTIPART:
                    for name, value in variables['post_data'].items():
                        post = re.sub(r"(?s)(name=\"%s\"(?:; ?filename=.+?)?\r\n\r\n).*?(%s)" % (name, boundary), r"\g<1>%s\r\n\g<2>" % value.replace('\\', r'\\'), post)
                if kb.postHint == POST_HINT.ARRAY_LIKE:
                    post = array_name + "[]=" + (delimiter + array_name + "[]=").join(variables['post_data'])
            else:
                post = delimiter.join(f'{key}={value}' for key, value in variables['post_data'].items())uri = variables['uri']
            get = delimiter.join(f'{key}={value}' for key, value in variables['get_data'].items())
            auxHeaders.update(variables['headers'])
            cookie = variables['headers']['Cookie'] if 'Cookie' in variables['headers'] else None
            get = variables['get_query'] if variables['get_query'] != original_get else get
            post = variables['post_body'] if variables['post_body'] != original_post else post
```

您需要将这些库添加到`thirdparty`文件夹:
[xmltodict](https://github.com/martinblech/xmltodict)
[requests _ toolblet](https://github.com/requests/toolbelt)

有关该实现的更多信息，请参考 Github 上的[拉请求](https://github.com/sqlmapproject/sqlmap/pull/5013)。

有帮助吗？
我不要求你给我买杯咖啡，
教我点东西…
不和:`**REND#9702**`