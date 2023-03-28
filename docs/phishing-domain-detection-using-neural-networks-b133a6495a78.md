# 基于神经网络的网络钓鱼领域检测

> 原文：<https://infosecwriteups.com/phishing-domain-detection-using-neural-networks-b133a6495a78?source=collection_archive---------1----------------------->

![](img/0f475ad814319ad9da9221f53a587ff3.png)

来源:[https://blog . idrive . com/2018/10/10/don ' t-be-a-victim-some-tips-to-avoid-phishing-scams/](https://blog.idrive.com/2018/10/10/dont-be-a-victim-some-tips-to-avoid-phishing-scams/)

## 神经网络在域名分析中的应用

# 域名分析

[StreamingPhish](https://github.com/wesleyraptor/streamingphish/) 是域名分析的实现之一。域名分析背后的想法是训练已知良性和网络钓鱼域的精选数据。在 StreamingPhish 中，一个 348 维的特征向量从域名中导出，然后使用[逻辑回归](https://scikit-learn.org/stable/modules/generated/sklearn.linear_model.LogisticRegression.html)进行训练。一些功能包括:

1.  如果域名中存在品牌名称
2.  如果某些定义的网络钓鱼词汇存在于 StreamingPhish 作者从[https://github . com/swifton security/PhishingRegex/blob/master/PhishingRegex . txt](https://github.com/SwiftOnSecurity/PhishingRegex/blob/master/PhishingRegex.txt)中精心挑选的目录中
3.  如果存在与钓鱼文字相似的文字，则相似度定义为 [Levenshtein 距离](https://en.wikipedia.org/wiki/Levenshtein_distance)
4.  …

这些都是很棒的特性，作者展示了非常好的结果(测试结果的准确率为 98.9%)。在这篇文章中，首先，我们将尝试看看我们是否可以在神经网络中使用这些功能，以及我们会得到什么样的结果，然后最终我们是否可以完全摆脱这些功能。

# 简单神经网络

首先，我们将在 TensorFlow 示例中创建一个类似于 [MNIST 的网络。该网络具有要素形状的输入图层。在 MNIST，它是二维的，但在我们这里，它是一维的，大小为 348。然后，我们有一个由 128 个节点组成的密集层，最后是良性与网络钓鱼类的两个输出节点。](https://www.tensorflow.org/datasets/keras_example#step_2_create_and_train_the_model)

上面的代码实现了我们刚刚讨论的内容。我们还获得了 98.9%的测试精度，这与现有的方法相当。虽然这些是有趣的结果，但我们从中获得了什么呢？我们还没有提高准确性(请注意，我们没有进行模型选择和交叉验证，所以我们应该从最初的实现中获得结果，这是有保留的。)或去掉了特性的必要性。因此，让我们去掉特性的必要性，看看我们的进展如何？

# 基于嵌入的卷积神经网络

用一维卷积嵌入是一种非常简单的文本分类方法。在我们的场景中，文本是一个域名，我们的目标是将它们分类为良性或网络钓鱼。下面是一个很棒的 StackOverflow 线程，解释了它的工作原理:【Keras 1d 卷积层如何处理单词嵌入—文本分类问题？(过滤器、内核大小和所有超参数)

我们的简单模型看起来类似于 StackOverflow 线程中的模型:

在我们的场景中，我们使用下面的代码将域名的每个字符转换为它的 ASCII 值，这使得嵌入词汇表的大小为 128(嵌入层的第一个参数)。

转换成 ascii 码

convert_to_ascii 方法获取字符串，并基于预定义的最大长度，使用数据中最大的字符串或手动选择，将字符串字符转换为它们的 ascii 值。

对于该模型，我们得到 96.12%的测试准确度，这低于使用精选特征和使用逻辑回归和简单神经网络的学习。虽然同时这个模型并不真的需要预定义的特性，所以它更通用。

# 基于 LSTM 的神经网络

LSTM 广泛用于文本分类，因此我们用它们构建了一个简单的网络:

这个模型的结果要差得多，给出的测试准确率为 93.19%。尽管它没有同时被优化。

# 无特征输入:神经网络性能

即使在一个简单的设置中，无特征的神经网络输入比有特征的更糟糕，我们认为它们是有价值的，因为它们可以被一般化并处理不同的场景。对于我们来说，接下来的步骤如下，看看我们是否可以提高性能:

1.  一般来说，数据越多，神经网络的性能越好。在这个设置中，我们有大约 10k+样本，这不是很多，因此包含更多数据将是查看我们是否可以提高性能的一种方法
2.  [拥抱脸](https://huggingface.co/)有很好的标记器和文本分类模型，所以值得研究一下
3.  优化当前简单模型的超参数将是研究改进性能的另一种方法

# 谷歌 Colab 笔记本

为了让 StreamingPhish 代码直接在 Google Colab 笔记本中工作，我们做了一些更改:

1.  我们从 GitHub 下载了 StreamingPhish 并解压。然后我们安装了一些依赖项。

2.之后，我们做了一些路径的改变。由于解压缩发生在您所在的当前目录 Colab 中，因此我们提供了一个相对路径:

3.脚本的其余部分保持不变，并按原样执行。这是最终的笔记本，请在驱动器中保存一份副本，尽情享受吧:

[](https://colab.research.google.com/gist/SalilJain/84f78722ff3007fed49da2cbb353cb77/phishing.ipynb#scrollTo=TxtKFgkQSN5j) [## 谷歌联合实验室

### 网络钓鱼

神经网络检测](https://colab.research.google.com/gist/SalilJain/84f78722ff3007fed49da2cbb353cb77/phishing.ipynb#scrollTo=TxtKFgkQSN5j) 

Medium 是一个很好的平台，可以了解最新最棒的技术。如果你不是会员，请考虑使用我的推荐链接:[https://salilkjain.medium.com/membership](https://salilkjain.medium.com/membership)成为会员，我会收到你的一部分会员费。在 [LinkedIn](https://www.linkedin.com/in/jainsalil/) 或 [Twitter](https://twitter.com/stridefrodo) 上与我联系。