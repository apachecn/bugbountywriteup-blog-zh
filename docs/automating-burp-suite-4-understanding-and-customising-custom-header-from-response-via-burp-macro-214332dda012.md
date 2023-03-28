# 自动化 Burp Suite -4 |通过 Burp 宏从响应中了解和定制自定义标题—编写自己的 Burp 扩展

> 原文：<https://infosecwriteups.com/automating-burp-suite-4-understanding-and-customising-custom-header-from-response-via-burp-macro-214332dda012?source=collection_archive---------0----------------------->

> 这是第 4 篇教程，我使用 **jython** 开发了一个 Burp 扩展，并使用 **Burp Suite 宏在从**响应体/响应**头派生的**请求头中的**自定义头上实现了添加。**

可以直接调用这个自定义标头扩展，它可以用于自动化请求标头中的 CSRF 令牌或 JWT 令牌。有多个自定义扩展实现了 JWT 或授权令牌，但这个扩展可以根据需要进行自定义。此外，这篇博客将指导你如何使用 Jython 在 Burp Suite 中编写扩展。这可用于从响应中获取动态值。

![](img/5706fd1ecbd7af386c1618e6c2a5c22e.png)

[参考](https://www.yeahhub.com/wp-content/uploads/2018/04/burp-suite-disable-detect-portal.png)

# 第 2 部分—创建通过扩展更新标题的宏

## 假设:

a)要添加到请求主体中的 X-CSRF 令牌。
b) CSRF 令牌来源于响应体。
c)宏的基础知识。根据需要使用自定义正则表达式修改扩展。
e)自定义头已经存在于所有的请求中。自定义打嗝扩展被加载。

## 创建宏和运行扩展的通用步骤:

1.  在[http://localhost/log in . PHP](http://localhost/login.php)上运行 dvwa

![](img/5e542e81ebb0e2b46de676e3734ec704.png)

2.因为这是 **DVWA** ，所以没有自定义标题。
我们将把`X-CSRF: xxxxxxx`替换为从请求体`value='c82d0a16fd455d8db048022a5d6bd0a9'`获取的**动态值**

![](img/f389f308c8aa3e7675196b29646c2e97.png)

3.让我们从 Burp 历史中选择请求，并快速创建宏。
**项目选项>会话>添加。**

![](img/9b8bd175a8405bcabf5553189254e360.png)

4.在`Session handling rule editor`中添加`Rule Description:`Blog-DynamicResponseHTMLBody。然后在`Rule Actions`中点击`Add`，然后点击`Run a macro`。

![](img/ebc1b5f47fabc37a7ff131d8f5b2e6d1.png)

5.然后在`Session handling action editor`中，选择`Add a new macro`。

![](img/d61501649b635187a1416f0bda6253f9.png)

6.在`Macro Recorder`中，选择包含**动态值**的请求，点击 **OK** 。

![](img/befe3eb73d065deaaa687037cab12901.png)

7.在`Macro Editor`中，添加`Macro description` : FetchDynamicValue。然后点击`Configure Item`。

![](img/8665221259ec6069cf96704293b4ee67.png)

8.因为没有饼干，所以上面的盒子没有打钩。现在在`Custom parameter locations in response`上，点击`Add`。

![](img/d19546e9a6d8689fbdc5e29851a69534.png)

## **A)执行宏从响应体动态更新扩展值。**

1.  加载用于从请求体获取动态值的 Burp 扩展。

![](img/91efd4ad881de5cf9f97e5af9f161851.png)

2.执行上述常见步骤。

3.对于`Define Custom Parameter`，提供`parameter name` : **any_value** (这可以是任何值，我们只是需要它稍后使用 Burp API 从 python 扩展获取宏响应)。
从**响应体**中选择数值。

![](img/6ef54debddbf943267f92b22872083b2.png)

4.`Configure Macro Item`中的自定义参数应该有 **any_value** 。然后点击**确定**。

![](img/f4cb03251e4b68070b74d2c9dd02bbfe.png)

5.点击确定，保存并关闭`Macro Editor`。

![](img/6f6731cb3fbf9bd6f76e150c0e2f7f56.png)

6.对于`Session handling action editor`，选择`After running the macro, invoke a Burp extension action handler`，选择自己的扩展[**macro _ custom _ header _ resbody . py**](https://github.com/justm0rph3u5/BurpSuite-CustomHeader/blob/main/macro_custom_header_resbody.py)**。** 点击**确定。**

![](img/51fe98004a3cc7b98a2290283253d97c.png)

7.保存`Session handling action editor and switch to Scope`。

8.在`Session handling rule editor`的`Scope`页签中，根据需要更换`Tools Scope`和`URL Scope`。点击**确定**。

![](img/2db91c7a10bcf320bf5e92b84dc423a3.png)

9.现在添加自定义标题，因为这是演示 DVWA，它不包含 CSRF 标题。我们可以通过调用另一个[自定义标题扩展](https://github.com/PortSwigger/add-custom-header)来添加自定义标题。

`X-CSRF: xxxxxx`并发送请求。

![](img/1c5171b890891bdae59d5026901995d1.png)

10.检查`X-CSRF:`令牌的值，该值是动态更新的。

![](img/da0b2e79c6b05c6c2101306d7c68f4f6.png)

## **B)执行宏以动态更新来自响应报头的扩展值。**

1.  加载用于从请求体获取动态值的 Burp 扩展。

![](img/b71e64a8fc9b9baa1b708ba9ef22ff30.png)

2.在`Session handling rule editor`中添加`Rule Description`，在`Rule Actions`中选择`Run a macro`。

![](img/88c9f2ddb2c3a5bd73cb6ffb3d37a461.png)

3.在`Session handling rule editor`中，选择`Add a new macro`。

![](img/429ba64df515f9cf21696a4d7ab9b23b.png)

4.对于`Define Custom Parameter`，提供`parameter name` : **any_random_value** (这可以是任何值，我们只是需要它稍后使用 Burp API 从 python 扩展中获取头值的宏响应)。
从**响应头**中选择数值。

![](img/734302b36d5ba6ea0673250aca2e22bc.png)

5.在`Configure Macro Item`中，取消勾选`Cookie handling`，因为不需要；在`Custom parameter locations in response`中，勾选`any_random_value`并点击确定。

![](img/10c6bec38897e969e2b7b509583ebb66.png)

6.然后在`Macro Editor`中，进入`Macro Description`并点击**确定**。

![](img/e8b4cf4110557190fab757fae0676f55.png)

7.然后在`Session handling action editor`中选择新创建的宏，即`FetchDynamicValue-Header`。之后在`After running the macro, invoke a Burp extension action handler:` [中选择扩展名 macro _ custom _ header _ resh eader . py](https://github.com/justm0rph3u5/BurpSuite-CustomHeader/blob/main/macro_custom_header_resheader.py)

![](img/c79b1dfedb3516fef3adb2a04cb8a2a7.png)

8.然后切换到`Scope`选项卡。对于`Tools Scope`,选择**范围**和 **URL 范围**。

![](img/3f4d3105e7d2b499665faf82b8f4d480.png)

如题

9.现在添加定制的`X-CSRF: xxxxxxx`令牌，并在中继器中发送请求。

![](img/8a993ae091c91b03354875b06834ef8c.png)

10.检查扩展的**值，它根据演示报头进行了修改(我们选择了日期报头，因为 DVWA 在响应报头中不包含任何 csrf 令牌)。**

![](img/9d68d6bf4b93342ef48f8265df252e68.png)![](img/8450094309a83dc144c0d7bc887b4613.png)

# 第 2 部分—关于扩展的所有内容:

1.  [**为响应头**](https://github.com/justm0rph3u5/BurpSuite-CustomHeader/blob/main/macro_custom_header_resheader.py) **:**

a)对于下面提到的响应头，部分是获取响应头。`macro_response_info.[getHeaders()](https://portswigger.net/burp/extender/api/burp/iresponseinfo.html)`获取响应头的值。
然后`macro_body_offset.get(1)[14:]`从特定索引开始切片。

> ***该值需要根据表头的动态值进行更改。***

```
macro_response_info = self.helpers.analyzeResponse(macroItems[0].getResponse())

self.stdout.println('Loading custom header for Macro complete: By justm0rph3u5')

# get the list of headers from the response, if token is present in the response header then we need to list all the header and extract the value
macro_body_offset = macro_response_info.getHeaders()

#from the macro body(which contains the response headers), we are extracting dynamic value of the header
new_header = macro_body_offset.get(1)[14:]
```

![](img/4848cacc20b6dfe59dd8a973e3ef6b0a.png)

b)在加载扩展之前，需要更新提到`X-CSRF`头的第二部分。该字符串检查所提到的报头是否出现在请求报头列表中，以执行进一步的修改。

在两个地方改变数值`X-CSRf`。

```
for header in headers:
    #Change this value according to the custom header present in the request. So if X-SESSION_ID: xxxxxxxx is the header then change the string to 'X_SESSION_ID'
    if '**X-CSRF**' in header:
        head_delete = header

#remove the header
headers.remove(head_delete)

#add new header, some wierd java error may come. please diy
#While adding the new header, kindly change the value to 'X_SESSION_ID', from above example.
headers.add('**X-CSRF:** ' + new_header)
```

c)这部分代码更新了 Burp 套件中的消息体和消息头。

```
# create new message with headers and body
new_message_request = self.helpers.buildHttpMessage(headers, message_body)
baseRequestResponse.setRequest(new_message_request)
```

![](img/f8453271167b811bb099a1094cb11dc1.png)

d)最后，定制的 Burp 套件扩展已经就绪，可用于添加任何具有动态值的标题。*这需要进一步修改，但就目前而言，效果很好。*

**2。** [**为响应体中的动态值**](https://github.com/justm0rph3u5/BurpSuite-CustomHeader/blob/main/macro_custom_header_resbody.py)

a)对于响应体中的动态值，下面提到的部分是获取响应体值。
`macroItems[0].[getResponse()](https://portswigger.net/burp/extender/api/burp/ihttprequestresponse.html)`从宏响应中提取令牌。然后`self.helpers.bytesToString(macro_body_value)`提供响应内容。将对该内容运行 Regex 以获取准确的值。

```
macro_response = self.helpers.analyzeResponse(macroItems[0].getResponse())
self.stdout.println('Loading custom header for Macro complete: By justm0rph3u5')
# extract the token from the macro response
macro_message = macroItems[0].getResponse()

# print(self.helpers.bytesToString(macro_message))
#this part of the code deals with fetching value of HTML Response Body

macro_offset = macro_response.getBodyOffset()
macro_body_value = macro_message[macro_offset:-1]
macro_body_str = self.helpers.bytesToString(macro_body_value)
```

b)还要更新正则表达式，这个正则表达式将用于从动态 html 页面中提取值。
对于`value='c82d0a16fd455d8db048022a5d6bd0a9'`，使用该正则表达式`value=\'\S+\'`。

> ***这个值需要根据来自身体的动态值来改变。***

![](img/bddf7ddd484bc5cd743244b4756e879f.png)

c)使用 python 中的 **re 模块**并创建 regex，该 regex 从 **HTTP 响应体**中提取并切片令牌的**精确值**。

根据精确值修改:`match_1.group()[7:-1]`

```
#Regex checks the value of the Token to be fetched from the html body. Here is was csrf token in the response body. Modify regex and slice it accordingly

matched = re.finditer(regex, macro_body_str, re.MULTILINE)

for matchNum, match_1 in enumerate(matched, start=1):

    #change this value of index according to the regex.
    #Modify this
    new_header=match_1.group()[7:-1]
```

d)在加载扩展之前，需要更新提到`X-CSRF`头的部分。该字符串检查所提到的报头是否出现在请求报头列表中，以执行进一步的修改。

将两处的值`X-CSRf`更改为所需的标题。例如 X 会话 ID、JWT 令牌等。

```
for header in headers:
    if 'X-CSRF' in header:
        head_delete = header

headers.remove(head_delete)

# add new header, some wierd java error may come. please diy
# While adding the new header, kindly change the value to 'X_SESSION_ID', from above example.
headers.add('X-CSRF: ' + new_header)
```

e)此代码更新了 Burp 套件中的消息正文和标题。

```
# create new message with headers and body
new_message_request = self.helpers.buildHttpMessage(headers, message_body)
baseRequestResponse.setRequest(new_message_request)
```

![](img/6cbcca31e7a5c016394ba99bc2619b91.png)

d)可能有其他自定义的 Burp Suite 扩展来修改标题，但在搜索数小时后，该扩展可以与简单的`Burp Macro`一起使用，并可以快速获取值和更新标题。

**注:**本文试图解决这个 portswigger 论坛中提到的问题。

[](https://forum.portswigger.net/thread/update-header-in-session-handling-macros-a0132444) [## 更新会话处理/宏中的标题

### 你好，我正在开发一个将 CSRF 令牌用于登录表单的应用程序。令牌是一个隐藏的值，在…

forum.portswigger.net](https://forum.portswigger.net/thread/update-header-in-session-handling-macros-a0132444) 

***最后，我们可以通过会话处理/宏更新 Burp 套件中的标题。***

## 关键要点:

1.  学习了如何在 Jython 中创建 burp 扩展。
2.  学习了 Java 和 Jython 编程。
3.  了解 Burp APIs 和宏。
4.  成功解决了论坛上长期悬而未决的问题。

> [在第三个教程中，从响应体中获取 CSRF 令牌，并使用**宏在请求参数中更新它。**阅读第 3 部分，这将有助于理解自定义标题替换。](https://justm0rph3u5.medium.com/automating-burp-suite-3-creating-macro-to-replace-csrf-token-from-response-body-to-request-param-de37eb54ab5f)

## **参考:**

1.  [https://www.youtube.com/playlist?list = PLD 3 kjnwbcwyelw-RM 7 ta w9 tspplvsobvd](https://www.youtube.com/playlist?list=PLD3kJNWBcwYElw-RM7taW9TSPPLvsObvd)
2.  [https://raesene . github . io/blog/2016/06/19/Burp-Plugin-JWT-Tokens/](https://raesene.github.io/blog/2016/06/19/Burp-Plugin-JWT-Tokens/)
3.  [https://www.okiok.com/burp-extensions-python/](https://www.okiok.com/burp-extensions-python/)
4.  [https://github . com/subodh Krishna/Digest-Header-Updater-Burp-Extension/blob/master/update-Digest _ Header . py](https://github.com/subodhkrishna/Digest-Header-Updater-Burp-Extension/blob/master/update-digest_header.py)
5.  [https://github . com/parma violet/AuthHeaderUpdate/blob/main/auth _ header _ update . py](https://github.com/parmaviolet/AuthHeaderUpdate/blob/main/auth_header_update.py)
6.  [https://forum . ports swigger . net/thread/update-header-in-session-handling-macros-a 0132444](https://forum.portswigger.net/thread/update-header-in-session-handling-macros-a0132444)
7.  [https://forum . ports swigger . net/thread/burp-extensions-using-make http request-46572 BD 1](https://forum.portswigger.net/thread/burp-extensions-using-makehttprequest-46572bd1)
8.  [https://www.youtube.com/watch?v=IdM4Sc7WVGU](https://www.youtube.com/watch?v=IdM4Sc7WVGU)

[![](img/4bc5de35955c00939383a18fb66b41d8.png)](https://www.buymeacoffee.com/justmorpheus)