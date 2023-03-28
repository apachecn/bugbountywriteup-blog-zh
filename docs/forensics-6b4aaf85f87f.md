# 取证—记录

> 原文：<https://infosecwriteups.com/forensics-6b4aaf85f87f?source=collection_archive---------0----------------------->

![](img/d6429d7e16756df6088ab80631e8116b.png)

## TryHackMe

## 这是一个被入侵系统的内存转储，做一些取证功夫来探索内部。

# 任务 1:波动取证

1.  下载 victim.zip
2.  这个转储文件的操作系统是什么？(操作系统名称)

![](img/0ea647cd401a0e686acdb6cbce3a5253.png)

```
Ans: windows
```

3.SearchIndexer 的 PID 是什么？

![](img/c44bbeee4f4b0b7298fe5a1b4ee674d7.png)

```
Ans: 2180
```

4.用户最后访问的目录是什么？

(最后一个文件夹名是什么？)使用日期和时间进行搜索

使用 **shellbags** 命令查找目录

```
./volatility -f victim1.raw --profile=Win7SP1x64 shellbags
```

![](img/2635e6c5f0d76977a8bcb33a9372717e.png)

```
Ans: Deleted_files
```

# 任务 2

1.  有许多可疑的开放端口；是哪一个？(答案格式:协议:端口)

第一个的协议和端口

![](img/b1fe560213a7922d13a1ffa27ed4b4fd.png)

```
Ans: UDP:5005
```

2.Vads 标记和执行保护是恶意进程的有力指示器；你能找到它们是哪一个吗？(答案格式:Pid1Pid2Pid3)

我们找到的第一个 3 PID 就是答案

![](img/da1d1f3fceed8cb033c31796d5aaa6af.png)![](img/a7acea7ed9b7896e029a2d616e5e2c7b.png)![](img/c945b472cbb42424fb4af0ee26c6e8df.png)

```
Ans: 1860;1820;2464
```

# 任务 3 —国际奥委会传奇

1.www.go****。ru '(写完整的网址，不带任何引号)

![](img/729309f22042a6e9e74fc7eb95df73ff.png)![](img/a2bbf0efc7b099c6766300faa0ac096b.png)

```
Ans: [www.goporn.ru](http://www.goporn.ru)
```

2.www.i****。com '(写完整的网址，不带任何引号)

![](img/6584265379cb62ee31a7eae2708d70f1.png)

```
Ans: [www.ikaka.com](http://www.ikaka.com)
```

3.www.ic******。' com '

![](img/9826259546bb05f95116af532181942d.png)

```
Ans: [www.icsalabs.com](http://www.icsalabs.com)
```

4.202.***.233.***(写完整 IP)

![](img/607280add8d7b4449974f51e8dd8cdea.png)

```
Ans: 202.107.233.211
```

5.***.200.**.164(写完整 IP)

![](img/f6678aa372cb7c6e81292dbf291e508b.png)

```
Ans: 209.200.12.164
```

6\. 209.190.***.***

![](img/2fc38007febb97e9d7cdcbd16e41db41.png)

```
Ans: 209.190.122.186
```

7.PID 2464 唯一的环境变量是什么？

![](img/ea54002808d23f872474340a5bad7ce2.png)

```
Ans: Answer is Highlighted in the above Picture Thankyou For Reading!!!Happy Hacking!!Author —  Karthikeyan N | Cyberw1ng
```

## 来自 Infosec 的报道:Infosec 每天都有很多内容，很难跟上。[加入我们的每周简讯](https://weekly.infosecwriteups.com/)以 5 篇文章、4 个线程、3 个视频、2 个 GitHub Repos 和工具以及 1 个工作提醒的形式免费获取所有最新的 Infosec 趋势！