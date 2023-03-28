# 为什么 WordPress 应该放弃旧的 PHP 密码加密算法。

> 原文：<https://infosecwriteups.com/why-wordpress-should-abandon-old-php-password-encryption-algorithms-ca3afa23e013?source=collection_archive---------4----------------------->

作为一个硬化问题，可以公开讨论。

事实上，这个问题已经被公开讨论了很多年:

[https://core.trac.wordpress.org/ticket/50027](https://core.trac.wordpress.org/ticket/50027)

WordPress 使用可移植的 php 密码哈希框架，它使用 bcrypt 哈希算法等等，所以密码加密包括 PHP 函数调用 password_hash 脚本，使用 PASSWORD_BCRYPT 或 PASSWORD_ARGON2I 或 PASSWORD_DEFAULT，最常见的是 password_default 作为单向哈希算法。

> 然而，如果一个攻击者破坏了一个数据库并导航到 wordpress 密码，这些密码是加密的，他/她找不到原始密码，这意味着他无论如何也不能登录 wordpress 网站。但如果他是从后往前做的呢？如果他设法使用自己的密码访问网站，并在密码发布或泄露中，他会发布他想要的真实用户名和密码(大多数情况下他会随机进行)，组合在 wordpress 登录中工作，从而“以某种方式”绕过使用 php 的 wordpress 加密算法。

# 让我们快速简单地重现一下，解释一下:

1.  创建网站/子域

*   例如:example.com 现在做一个 wordpress 安装在 example.com 的根文件夹或子域。
*   自动创建了一个新用户(管理员),这次使用的用户名不是很常见，也很复杂。
*   比方说:`admin_vzyog96`我的 wordpress 安装程序分配给我的。现在，新用户也有一个密码:我被分配了密码:`pePi7TEmp3o$ez8o`，它在数据库中的加密版本是:`$P$B.f9pHoT7fKNuCadbF8x3pGoe60tis1`

现在转到数据库，其中列出了用户:一个新安装的用户，作为一个攻击者访问数据库，我必须保持访问持续的数据提取，否则可能会发生数据泄露或勒索等。但是怎么做呢？因为存储的密码是单向散列的？

*   通过逆向计算得出。

1.  使用下面的简单 PHP 脚本生成一个随机密码:

```
<?php

// Get the new attacker password
$password = 'mypassword';

// Generate the hash

$hash = password_hash($password, PASSWORD_DEFAULT);

// Display hashed password to use
echo $hash;

?>
```

我得到的密码是:`$2y$10$k2JDEx4b8aI6rmFM942Inuy9./CazL1Dr3Pu9Qcl90W3zaJcq4q/e` 5。现在复制它，转到数据库并直接编辑密码(大多数数据库，sql，允许通过双击密码值字段进行编辑)。粘贴新密码；记住原来的是:`mypassword`

1.  现在转到你网站的 wordpress admin 登录 URL:ie。example.com/wp-admin 使用从一开始分配的用户名:`admin_vzyog96`和您生成的新密码`$2y$10$k2JDEx4b8aI6rmFM942Inuy9./CazL1Dr3Pu9Qcl90W3zaJcq4q/e`。密码让你登录。作为攻击者，我现在可以拥有用户名:`admin_vzyog96`和密码:`$2y$10$k2JDEx4b8aI6rmFM942Inuy9./CazL1Dr3Pu9Qcl90W3zaJcq4q/e`，并且可以随心所欲地使用这些数据，现在我拥有了完整的登录组合集。

这算什么问题？？

> *最初作为攻击者，我无法登录 WordPress，因为我没有原始密码，我也无法仅用用户名和单向散列密码做任何事情，但现在我有了这两个密码，它们可以工作了。*

这很容易做到。

> 有人可能也不知道受害者(考虑到 wordpress 基本上需要拖放知识和一点创造力)知道数据库受损的唯一方法是使用 WordPress 工具包的自动登录，这主要来自 cpanel，而不是所有的 WHM 服务提供商。并不是所有的 wordpress 站点都用这个工具包来处理登录。

我意识到 MD5 散列也可以代替 WordPress 散列密码，并且仍然认证，因为`wp_includes/pluggable.php`将仍然处理 MD5 并加密，然后与数据库进行比较，没有任何错误，这意味着没有来自默认 PHP 密码散列框架的特殊密钥/安全性。

这怎么证明不是 php 的特性呢？？

试试 crypt，一种使用 php 和密钥的散列算法:

```
<?php

// Get the password from the user
$password = 'mypassword';
$key = "khgfewtyui89283764treyduhjzksjahdgfret6738q29iojashdgftye7w8q9ioakjshdgfyrew8uioqwuehgyrfyewuiqouehgdfeyw78q9iowue8y7648392qiowushdgfshajk";

// Generate the hash
$hash = crypt($password, $key);

// Print the hash to the screen
echo $hash;
?>
```

登录不起作用，无论您复制多少密码，哈希都不会被破解，攻击者只有无用的哈希密码和用户名。这是为什么呢？因为 wordpress 安装现在没有 crypt 密钥，并且角色互换，如果 wordpress 安装有密钥，而攻击者没有…他将永远无法生成密码散列。

> 由此，我相信你理解了迄今为止所有 WordPress 版本密码加密不足背后的逻辑。

# 建议

> *也许 WordPress DevOps 团队应该考虑 crypt 或比 crypt 更好的密码哈希算法，因为它比当前 WordPress 版本中使用的密码哈希算法更安全，因为 md5 复制相同的密码，至于密钥，可以通过 MD5 或任何其他加密算法生成，以根据安装情况分配不同的密钥*

我相信，考虑到在新的场景中，攻击者不能以任何方式复制散列或向数据库添加任何散列，因为他的散列在与数据库中的散列字符串进行比较之前不会绕过密码散列中的 crypt 密钥，所以这将解决问题。crypt 密钥是不能被绕过的，正如我提到的，为 wordpress 的所有新安装考虑一个随机的已经加密的密钥，这样没有两个安装共享相同的密钥，或者没有人复制安全密钥的随机分配。

> *我在 WordPress 6.1.1 上试过这个，意思是到目前为止所有的 WordPress 版本都受到这个问题的影响*

# 这能证明有什么安全影响吗？

密码加密强度不足导致(但不限于):

1.  随着攻击者可以随意维护受害者数据库中的访问权限，攻击不断增加。
2.  所有 wordpress 安装都容易被接管，所有版本都容易被接管，因为我称之为“从后到前”的哈希和方法在 wordpress 的所有版本中都是可复制的，并且再次绕过了在数据库中存储“安全”密码的加密。这基本上是为了在入侵后阻止攻击者或在访问后修改凭据，但现在不再是这样了。
3.  攻击者现在可以用他们自己的密码组合来发布 wordpress 泄露的数据库，他们可以将密码更改为任何值，并且新值可以正确验证。

*   如果密码没有充分加密，个人信息(如姓名、地址和财务信息)可能会面临被未经授权的个人访问的风险，这些人现在可以设置任何密码，并根据自己的意愿保持访问。当某人的个人信息被用于实施欺诈或其他犯罪时，身份盗窃也会发生。机密信息的丢失:如果密码没有正确加密，未经授权的个人就有可能访问和泄露机密业务信息，从而导致潜在的财务损失和公司声誉受损

旧的 php 加密已经存在了太长时间，攻击者已经掌握了利用它的艺术，开发者和 WordPress 社区仍然在争论它是否应该改变，或者有一个更好的加密算法，当然是基于 php 的。

> 有许多想法，但总的来说，关键是:是时候改进密码加密了

![](img/a2ecd66079c11b33cfe5e595c2a5c6f4.png)

斯蒂芬·菲利普斯-Hostreviews.co.uk 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片