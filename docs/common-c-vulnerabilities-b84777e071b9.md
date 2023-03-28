# 常见的 C 漏洞

> 原文：<https://infosecwriteups.com/common-c-vulnerabilities-b84777e071b9?source=collection_archive---------0----------------------->

![](img/4847dc630c071ace0e2502bde6cc5a9b.png)

克里斯里德在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# **简介**

众所周知，C 编程语言在很多方面都非常敏感。即使在今天，当本科生学习编程时，他们首先接触的是 C 或 Java 编程语言。Python、Ruby 和其他编程语言将在稍后添加。但是，更重要的是，他们在学习时是否考虑了最佳实践？不幸的是，事实并非如此。他们实践和实现相同的危险功能，他们的努力成果在他们生产的项目中是显而易见的。结果是永无止境的错误和补丁循环。

作为这篇博客文章的一部分，我们想展示一些最常用和最易受攻击的函数，以及 C 编程语言固有的一些漏洞。

# **gets()**

gets()函数用于接受用户的输入。但是接受用户的输入会有什么问题呢？程序确实需要用户与之交互。示例包括登录提示、简单的计算器等。

问题的出现是因为 gets()不执行边界检查，这意味着用户可以让程序接受无限大小的字符串。这会通过覆盖内存区域导致缓冲区溢出攻击，还会导致代码执行。

**不安全的 C 代码:**

```
#include <stdio.h>int main () 
{ char str[50]; printf("Enter a string : "); gets(str); printf("You entered: %s", str);
 return(0);
}
```

**缓解:**不用 gets()，可以使用 f gets()函数。fgets()执行边界检查，使用该函数可以消除缓冲区溢出。

# **strcpy()**

strcpy 代表字符串复制，用于将字符串从源位置复制到目标位置。

语法是 strcpy(dest，src)。src 中的字符串将被复制到 dest 变量中。

如果向 src 提供足够大的字符串，就可能导致缓冲区溢出攻击。

**不安全的 C 代码:**

```
#include <stdio.h>#include <string.h>int main() {
char src[20] = "C programming";char dest[20];// copying src to deststrcpy(dest, src);puts(dest); // C programmingreturn 0;
}
```

**缓解:**您可以使用 strcpy()函数来代替 strcpy()。

# **strcat()**

strcat()函数用于将源字符串追加到目标字符串中。但是，就像上面提到的函数一样，它不检查长度/边界。这很容易导致分段错误或缓冲区溢出攻击。

**不安全的 C 程序:**

```
#include <stdio.h>#include <string.h>int main() {char s1[100] = "This is an ", s2[] = "insecure implementation";// strcat concatenates str1 and str2// the resultant string is stored in str1.strcat(s1, s2);return 0;}
```

**缓解:**可以用 strcat()函数代替 strcat()。

# **strcmp()**

strcmp 代表字符串比较。这个函数比较两个字符串，并根据比较结果返回一个整数。如果第一个值小于、等于或大于第二个值，则该整数将小于、等于或大于 0。

strcmp()很容易被用来造成缓冲区溢出攻击。一个恶意的参与者所要做的就是提供一个比变量实际能容纳的足够大的字符串。

**易受攻击的 C 程序**

```
#include <stdio.h>#include <string.h>int main() { char str1[20], str2[] = "abCd", str3[] = "abcd"; int result; gets(str1); // comparing strings str1 and str2 result = **strcmp(str1, str2);** printf("**strcmp(str1, str2) = %d**\n", result); // comparing strings str1 and str3 result = **strcmp(str1, str3);** printf("**strcmp(str1, str3) = %d**\n", result);return 0;
}
```

**缓解:**要缓解 strcmp()导致的缓冲区溢出，可以使用 strcmp()函数，或者在比较字符串之前先计算值的长度。

# **格式字符串漏洞**

C 语言利用格式说明符来接受和打印用户的输入。

例如，要打印整数日期类型值，可以使用%d 格式说明符，%s 表示字符串，等等。

**例如**

```
int x;scanf(“%d”, x);printf(“%d”,x);
```

*当程序需要一个值，但用户输入了一个格式说明符时，就会出现格式字符串漏洞。*

**这会导致数据泄露，程序崩溃，将任意数据写入任意位置。**(可能通过指定%n 格式说明符)。

**易受攻击的 C 程序:**

```
#include<stdio.h>int main(int argc, char* argv[]){printf(argv[1]);return 0;}
```

**缓解:**始终对格式字符串进行硬编码。

比如 printf("%s "，argv[1])；

# **整数溢出和下溢**

包括 C 在内的大多数编程语言中的数据类型都有固定的大小。C #中的 int 数据类型的大小为 4 个字节。声明为整数类型的变量可以存储的值的范围是–2，147，483，648 到 2，417，483，647。

但是，有没有想过，如果存储一个大于 2，417，483，647 或小于-2，417，483，648 的值，或者对整数执行运算，结果大于 2，417，483，647 或小于-2，417，483，648，会发生什么情况？

让我们来看看，**易受攻击的 C 程序**:

```
#include <stdio.h>int main(){int x = 2147483649;    //2147483647 + 2printf(“%d”,x);return 0;}
```

**输出:-2147483647**

您可能期望输出为 2147483649，但是由于它超出了整数数据类型的最大范围，所以它到达了–ve 的末尾，并在**–2，147，483，648** 上加 1。

**缓解**:使用检查确保数值不超过上限。

**结论:**

这篇博客文章涵盖了 C 编程语言中的一些漏洞。当然，开发人员可以通过使用错误的逻辑来添加漏洞，但是这些是在提到的函数中总是存在的一些漏洞。您必须确保您的程序尽可能远离这些函数。