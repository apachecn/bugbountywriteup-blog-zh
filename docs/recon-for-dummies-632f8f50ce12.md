# 侦察假人

> 原文：<https://infosecwriteups.com/recon-for-dummies-632f8f50ce12?source=collection_archive---------2----------------------->

![](img/3de0a426c79cf6c7d37baf5d5e85046c.png)

嘿大家，我希望你们都做得很好。现在，正如我所说的，我将会用所有可用的工具来创造我自己的侦查方法。在没有侦察的情况下对目标进行狩猎总是感觉缺少了什么，显然更少的攻击面也侦察会帮助你更好地了解你的目标。一件重要的事情是，这不会是你可能见过的极端侦察之一，定期扫描主机，寻找新的主机和子域。这是接近目标前的简单侦察。

现在，在使用工具之前，你首先需要将这些工具添加到你的武器库中。对于子域枚举，你需要不同的工具，如 assetfinder，knock，subfinder，amass 等。现在你需要一些工具来检查子域接管，视觉侦察的工具，Github dorking，抓取 URL 的工具，参数挖掘的工具，然后更多的工具。这时候你一定没事了！即使您已经安装了一些程序，也需要安装很多程序，但是如果您可以通过一个脚本安装所有程序呢？对！我们会做到的

首先在你的系统上安装 go，因为大多数工具都是用 go 编写的，而且速度很快。请在此跟进:

[](https://golang.org/doc/install) [## 下载并安装

### 按照此处描述的步骤快速下载并安装 Go。关于安装的其他内容，您可能会感兴趣…

golang.org](https://golang.org/doc/install) 

安装完成后，安装所有这些工具，为此我使用了 six2dez 的“reconftw”。因为它不仅会安装你需要的所有基本工具，而且还可以自动为你侦察和搜索漏洞。但是我不建议直接向你的目标开火，因为它非常嘈杂，有时会阻塞你的 IP。人工狩猎也很有趣。

重组 w:

[](https://github.com/six2dez/reconftw) [## six2dez/reconftw

### reconFTW 是一个工具，旨在通过运行最佳工具集来执行自动侦察目标域…

github.com](https://github.com/six2dez/reconftw) 

还要阅读所有文档，特别是安装后指南:

[](https://github.com/six2dez/reconftw/wiki/Post-Installation-Guide) [## six2dez/reconftw

### 通过 reconftw.cfg 文件可以控制整个工具的执行。猎人可以设置各种扫描模式…

github.com](https://github.com/six2dez/reconftw/wiki/Post-Installation-Guide) 

因为这个工具也为你的目标做被动枚举，所以你需要为 amass 和 subfinder 以及其他配置文件获取你的 API 键。获得所有这些 api 密钥将是一个耗时的过程，但这是值得的。至少得到免费的。

1.  **子域枚举:**

现在，与其为子域运行所有这些不同的枚举工具，然后合并结果，我认为如果我们让 reconftw 为我们做这项工作会更有效率。因为我们已经投入了时间。

> 。/reconftw . sh-d target.com-r(用于基本侦察)

1.1 现在您有了一个子域列表，如:

> admin.target.com、dev.target.com、api.target.com、shop.target.com 等。

1.2 是时候检查是否有任何 **http|https 服务**正在这些子域上运行，为此我们将使用**http probe。**

您可以通过以下方式在您的子域列表上运行 httprobe:

> cat subdomains.txt | httprobe

它会给你一个列表，列出所有对 httprobe 有响应的子域名。

输出:[https://example.co](https://example.com)米，[http://example.com](http://example.com)

默认情况下，您还可以检查不同的端口 80。

*提示:使用前请阅读所有工具的手册页。*

但是我更喜欢让 reconftw 也为我做这件事。reconftw 会将你的所有输出保存在 Recon/targetname 文件夹中，包括子域名列表、实时网站/URL 列表等。

在 httprobe 时间后退出重新配置。是时候从这里开始手动操作了。

2.**如果您需要，让我们对所有目标**进行截图:

我们将为此使用 gowitness，因为 reconftw 已经安装了它。

> > gowitness 文件

或者一次拍摄一个目标的屏幕截图:

> > gowitness single

它将采取服务器提供的响应截图。

3.**从您的目标收集所有 js 文件:**

3.1 首先让我们使用 gospider 从目标收集所有的 URL

[](https://github.com/jaeles-project/gospider) [## jaeles 项目/gospider

### Gospider -用 Go 编写的快速网络蜘蛛。通过在…上创建帐户，为 jaeles-project/gospider 开发做出贡献

github.com](https://github.com/jaeles-project/gospider) 

在安装和运行 gospider 之后，您一定已经看到它从目标获取了所有的 URL。

所以我们先过滤掉 js one 一次:

> gospider-S URLs . txt | grep js | tee-a js-URLs . txt

将所有输出保存到 js-urls.txt

3.2 这个 js-urls.txt 仍然包含不想要的文本，所以让我们只过滤掉 URL，经过一些尝试和错误后，我想到了这个

> 卡特彼勒 js-URLs . txt | grep-EO '(http|https)://[^\"]+' | cut-d]-f1 | sort-u

现在，您可以将输出保存到一个包含所有 js urls 的文件中。

3.3 现在检查这些 URL 是否可访问，以便进一步分析。因此，让我们通过卷曲过滤掉 200 个响应 URL

`cat real-js-urls.txt | parallel -j50 -q curl -w 'Status:%{http_code}\t Size:%{size_download}\t %{url_effective}\n' -o /dev/null -sk | grep Status:200 |` cut -d " " -f 3 > js_200.txt

现在您有了一个用于分析的活动 js 文件列表。

4.**在 js urls 上使用 link finder:**

但是如果你注意到 gospider 已经在 3.1 节中使用 linkfinder 为你做了这项工作。无论如何，我们将手动操作。

[](https://github.com/GerbenJavado/LinkFinder) [## GerbenJavado/LinkFinder

### 在 JavaScript 文件中查找端点的 python 脚本——gerben javado/link finder

github.com](https://github.com/GerbenJavado/LinkFinder) 

> 对于 I in $(cat js _ 200 . txt)；做 echo“扫描$ I”；python 3/root/Tools/link finder/link finder . py-I $ I-o CLI；完成的

在这里，我们将每个 js url 传递给 linkfinder，也许你会发现一些有趣的东西。

5.**参数发现:**

现在你想找到一些参数，你可以在那里寻找 ssrf，xss，rce 或任何东西，基本上枚举所有那些接受用户输入并处理它的端点。请记住，所有输入都可能是恶意的。

我们要用的工具:

[](https://github.com/tomnomnom/assetfinder) [## tomnomnom/assetfinder

### 查找与给定域潜在相关的域和子域。如果您已经安装并配置了 Go(即…

github.com](https://github.com/tomnomnom/assetfinder) [](https://github.com/lc/gau) [## lc/gau

### getallurls (gau)从 AlienVault 的开放威胁交换、Wayback Machine 和 Common Crawl 中获取已知的 URL

github.com](https://github.com/lc/gau) 

现在，基本的想法是，我们将抓取所有 URL 和参数的目标，然后过滤掉需要一些输入的 URL 和参数。

例如，让我们说一些以 url 作为输入的常见参数名称:

转到=，重定向=，url=，重定向 uri=

类似地，有一些参数需要一些输入，这些输入可以导致 xss、sqli、ssti、lfi 等。脆弱。

现在，一种方法是选择一个目标，然后将它传递给 gau 和 waybackurls，然后对一些你已经看到的易受攻击的模式进行 grep。

例如 waybacurls target.com | grep redirect _ uri

或者 waybacurls target.com | grep =

但你不可能记住所有漏洞的所有关键字和模式。这就是 tomnomnom 的另一个工具来拯救的地方。

gf:

[](https://github.com/tomnomnom/gf) [## 汤姆诺姆/gf

### grep 的包装器，以避免输入常见的模式。我经常使用 grep。当审计代码库时，查看…

github.com](https://github.com/tomnomnom/gf) 

Reconftw 安装它已经与模式也。

> gf-列表

查看是否有一些模式与漏洞相匹配。你也可以创建你的模式，阅读文档。

现在你只需要简单地将你的输出传送到 *| gf xss*

它将简单地为您 grep 所有可能导致 xss 的参数

让我们来看看它的实际应用:

> cat subdomains . txt | wayback URLs | gf redirect | QS replace | tee redirect . txt

我们将我们的子域传递给 waybackurls，它从 wayback 机器上获取所有已知的参数和 url，然后我们获取可能导致 URL 重定向的参数(这可能不是有意的行为，不要将其视为漏洞)。然后我们使用 qsreplace，它只过滤出唯一的输出，并删除重复的输出。

[](https://github.com/tomnomnom/qsreplace) [## tomnomnom/qsreplace

### 接受 stdin 上的 URL，用用户提供的值替换所有查询字符串值，只输出查询的每个组合…

github.com](https://github.com/tomnomnom/qsreplace) 

6.**发现隐藏参数**

[](https://github.com/s0md3v/Arjun) [## s0md3v/Arjun

### HTTP 参数发现套件 Arjun 可以为 URL enpoints 找到查询参数。如果你不明白那是什么意思，那是…

github.com](https://github.com/s0md3v/Arjun) 

假设你在 http://target.com/api/endpoint 的某个端点[上，想要发现应用程序是否接受任何隐藏的参数。](http://target.com/api/endpoint)

你可以在你的目标上运行 arjun，你可能会发现一些东西，它很容易使用。看一遍它的文档。

到目前为止，您已经:

1.  体面的子域名列表。
2.  实时 URL 列表
3.  Js 列表
4.  唯一端点
5.  唯一参数列表
6.  和其他数据，如果需要的话，您可以使用这些数据和工具进行进一步的侦察。

现在，令人难过的是，我认为这足以让你我涵盖一个相当不错的攻击面，手动寻找漏洞。侦察可能是发现更多 bug 的关键，但不能代表一切。你应该有一双敏锐的眼睛来发现奇怪的行为，这只能通过练习来实现。去学习和寻找新的错误类型，你最终会成长。

[](https://portswigger.net/web-security) [## 网络安全学院:PortSwigger 提供的免费在线培训

### 推动您的职业生涯灵活学习向专家学习网络安全学院是一个免费的在线培训中心，面向…

portswigger.net](https://portswigger.net/web-security) 

涵盖所有类型的错误，从这里他们是免费的，非常好:)

给你一些额外的阅读材料以便更好地侦察。你总能从他们身上学到更多:

侦察构建模块:

[](https://www.offensity.com/de/blog/just-another-recon-guide-pentesters-and-bug-bounty-hunters/) [## 只不过是圣灵降临者和虫子赏金猎人的另一个侦察指南

### 尤其是在猎捕虫子的时候，侦察是最有价值的事情之一。有…

www.offensity.com](https://www.offensity.com/de/blog/just-another-recon-guide-pentesters-and-bug-bounty-hunters/) 

手动 Github dorking:

[](https://orwaatyat.medium.com/your-full-map-to-github-recon-and-leaks-exposure-860c37ca2c82) [## 你对 Github 侦察和泄漏暴露的完整地图

### 你好，我叫奥瓦·亚特

orwaatyat.medium.com](https://orwaatyat.medium.com/your-full-map-to-github-recon-and-leaks-exposure-860c37ca2c82) 

我希望这是一个信息丰富的博客。与你的猎人同伴分享。请随时寻求反馈和改进。

也许我会为这个侦察过程写另一部分，用更先进的目标表面覆盖。我还会写下我一直在学习的 bug 以及如何找到它们。

直到下周。

谢谢你。