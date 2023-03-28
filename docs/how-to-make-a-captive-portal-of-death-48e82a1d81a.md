# 如何制造一个死亡之门

> 原文：<https://infosecwriteups.com/how-to-make-a-captive-portal-of-death-48e82a1d81a?source=collection_archive---------0----------------------->

你来吧。我做到了。每个人都不假思索地去做。

你会毫不犹豫地将手机连接到机场或咖啡店的“免费公共 Wi-Fi ”,即使你的手机警告你这是一个“不安全”的网络。有时你会面临一个*强制门户*:一个烦人的弹出窗口，要求你在连接前登录或接受条款&条件。

![](img/c6e623e024d8a1fbd658d8271e32d323.png)

只需几件设备，你就可以用*你能想象到的任何页面*作为你的强制门户，制作你自己的“免费公共 Wi-Fi”(或任何你想命名的东西)。

希望在看到这种设置是多么容易之后，在将凭证或个人数据输入 Wi-Fi 网络的强制网络门户之前，您会更加谨慎。

# 装备

这是我用来对付这个坏男孩的设备:

*   [Raspberry Pi Zero](https://www.raspberrypi.org/products/raspberry-pi-zero/) 带有 Raspbian OS 的图像
*   [ALFA AWUS036NEH 长距离 802.11b/g/n Wi-Fi USB 适配器](https://www.amazon.com/gp/product/B0035OCVO6/ref=oh_aui_detailpage_o00_s00?ie=UTF8&psc=1)
*   [USB 集线器](https://thepihut.com/products/4-port-usb-hub-usb-2-0)增加 Wi-Fi 加密狗的覆盖范围(这样它就不依赖于直接来自 Pi 的 5V 电源)

# 创建热点

## 1.主机接入点守护程序

在你的 Raspberry Pi 上，安装 **hostapd** 并配置`/etc/hostapd/hostapd.conf`如下:

```
interface=wlan1
ssid=YOUR_NETWORK_NAME_HERE
hw_mode=g
channel=11
macaddr_acl=0
```

每次 Pi 启动时启用 **hostapd** :

`$ sudo systemctl enable hostapd`

然后在文件`/etc/default/hostapd`中设置`DAEMON_CONF="/etc/hostapd/hostapd.conf"`。

*   每次 Pi 启动时，hostapd 都会启动
*   它会尝试使用接口`wlan1`。这是您的 Wi-Fi 加密狗正在使用的接口。对我来说是`wlan1`。
*   硬件模式`g`指的是 IEEE 802.11g (2.4 GHz)。
*   `channel=11`有些武断。2.4 GHz 频段有 14 个频道。
*   `macaddr_acl=0`表示访问控制列表将接受所有内容，除非它明确在*拒绝*列表中。

## 2.DHCP 服务器(动态主机配置协议)

安装 **isc‐dhcp‐server** 并编辑文件`/etc/dhcp/dhcpd.conf`。取消该行的注释

```
# authoritative
```

现在将它添加到文件的末尾:

```
subnet 10.0.10.0 netmask 255.255.255.0 {
    range 10.0.10.2 10.0.10.254;
    option domain-name-servers 8.8.8.9,8.8.4.4;
    option routers 10.0.10.1;
    interface wlan1;
}
```

这意味着连接到网络的客户端将在`10.0.10.2-254`之间接收 IP 地址。路由器(Raspberry Pi) IP 地址是`10.0.10.1`。IP 地址的前 24 位是地址的*网络*部分的一部分，剩余的 32 位留给热点上的*主机*。IP 地址`8.8.8.9`和`8.8.4.4`仅仅是谷歌的 DNS 服务器。

## 3.配置网络接口

为 wlan1 修改文件`/etc/network/interfaces`

```
auto wlan1
iface wlan1 inet static
    address 10.0.10.1
    netmask 255.255.255.0
    # Important to set it to monitor mode,
    # otherwise hostapd will FAIL to start!
    wireless-mode monitor
```

注意`auto INTERFACE`是指开机时启动界面。接下来，取消注释

```
# net.ipv4.ip_forward=1
# net.ipv6.conf.all.forwarding=1
```

在文件`/etc/sysctl.conf`中。

## 4.配置 iptables

运行命令

`$ sudo iptables ‐t nat ‐A POSTROUTING ‐o wlan0 ‐j MASQUERADE`

*   `-t nat` →使用 NAT 表(网络地址转换)
*   `-A POSTROUTING` →(或`--append`)在数据包即将发出时对其进行更改
*   `-o wlan0` →(或`--out-interface`)与`-A`选项链接，指定发送数据包的接口(**使用 Raspberry Pi 的主普通接口**，该接口应通过另一个网络连接互联网)
*   `‐j MASQUERADE` →允许在不中断原始流量的情况下路由流量

然后只需运行以下命令就可以持久化 iptables

`$ sudo apt‐get install iptables‐persistent`

如果您需要重新保存 *iptables* 配置，请这样做:

```
$ iptables-save > /etc/iptables/rules.v4
$ ip6tables-save > /etc/iptables/rules.v6
```

## 5.保存并重启

`$ sudo shutdown -r now`

# 创建强制网络门户

## 1.nodogsplash

为此，我们将使用开源项目 [nodogsplash](https://github.com/nodogsplash/nodogsplash) 。它的文档说明它是“一个强制网络门户，提供了一种简单的方法，通过在授权访问互联网之前向用户显示一个启动页面来提供对互联网的受限访问。”

我用的是 3.0 版本，所以我不能保证如果你用另一个版本的话这也能工作。在克隆 Git repo 之后，运行`git checkout v3.0.0`来签出相同的版本。

用来自[的内容编辑文件`/etc/nodogsplash/nodogsplash.conf`此要点](https://gist.github.com/trevphil/2734524812f8a1b53f3c09ec35656a1f)。为了理解所有的设置，我推荐[阅读文档](https://gist.github.com/trevphil/2734524812f8a1b53f3c09ec35656a1f)。但简而言之:

*   它将使用接口`wlan1`，即 Wi-Fi 加密狗正在使用的接口。
*   允许预认证用户访问**端口 53** 很重要，这样他们的设备才能解析 DNS(下面会详细介绍)。
*   我们还使用[转发认证服务](https://nodogsplashdocs.readthedocs.io/en/stable/fas.html) (FAS)监听端口 8000，原因如下所述。

> *为什么预认证用户需要访问端口 53？当计算机或移动设备连接到 Wi-Fi 网络时，通常会有一个内置机制来检测强制网络门户并启动本机浏览器来接受条款&条件。例如，一部 iPhone 向*[*http://captive.apple.com*](http://captive.apple.com/)*发出请求，并检查响应是否为“成功”如果是*而不是*“成功”，那么手机知道它需要通过一个强制网络门户。*如果没有端口 53，设备将无法解析 DNS 并获得连接是否成功的响应。

## 2.federation of american scientists 美国科学家联合会

FAS 是俘虏之门的肉。在这里，您可以制作特殊的网站或网页，用户必须通过这些网站或网页才能访问互联网。它可以是任何东西:一张毛猴吃葡萄的图片，一个条款和条件页面，或者更恶意的东西，比如一个看起来和流行网站一样的登录页面。如果你给 Wi-Fi 热点起了一个与你正在模仿的流行网站相关的名字，这可能会特别狡猾。而且不合法，所以不要做！不要将凭证放入公共 Wi-Fi 强制门户网站！

网页不仅可以看起来像任何东西，你还可以用运行在 Raspberry Pi(它现在充当服务器)上的几乎任何东西来制作它。也就是说 HTML+CSS+Javascript，Angular，Ruby on Rails，node.js 等等。对于这个例子，我选择了 [Python-Flask](http://flask.pocoo.org/) 。

Python 服务器被配置为监听端口 8000，因为这是我们在 **nodogsplash** 配置中指定的端口。当用户进入强制网络门户时，他将被提供 Python-Flask 服务器的索引(`/`)页面。 **nodogsplash** 将通过 URL 中的可选参数将`?tok=XXX&redir=YYY`发送到索引页面。在你做完之后...不管你想做什么...用户应该被重定向到`http://<nodogsplashIP:nodogsplashPORT>/nodogsplash_auth/?tok=tok&redir=redir`，这里`tok`和`redir`的值与初始 URL 中传递的值相同。在 **nodogsplash** 接收到这个 GET 请求后，它知道用户已经被授权访问互联网。

`nodogsplashIP`应该是路由器的 IP 地址(Pi ),如果你用的是我的配置文件，`nodogsplashPORT`应该是 2050。

# 在树莓派上这样做的技巧

## 命令

*   `lsusb` →显示连接的 USB 设备
*   `lsmod` →显示加载的内核模块和一般有用的系统信息
*   `dmesg` →显示系统信息
*   `lshw` →显示硬件信息
*   `wpa_cli` →启动命令行工具来设置/扫描 Wi-Fi 网络(如果您通过 SSH 连接到 Pi，这尤其有用)

## 随机的东西

您可以在`/etc/rc.local`中添加命令，以便在启动时运行它们。请注意，如果一个命令失败，脚本将退出，因此后续的命令将不会执行。

在 SD 卡的引导分区中放置一个名为`ssh`的空文件，以自动启用 SSH(注意在`config.txt`文件中包含`dtoverlay=dwc2`非常重要)。

您也可以在 SD 卡的引导分区中创建一个名为`wpa_supplicant.conf`的文件，以自动连接到网络。下面是一个例子:

```
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev update_config=1# AP scanning
ap_scan=1# ISO/IEC alpha2 country code in which the device is operating 
country=DEnetwork={
    ssid="NETWORK_SSID"
    psk="NETWORK_PASSWORD"
    key_mgmt=WPA-PSK
}
```

*黑客快乐！*

*原载于*[*trevphil.com*](https://trevphil.com/posts/captive-portal)*。*