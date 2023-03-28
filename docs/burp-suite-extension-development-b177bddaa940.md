# Burp 套件扩展开发

> 原文：<https://infosecwriteups.com/burp-suite-extension-development-b177bddaa940?source=collection_archive---------5----------------------->

![](img/9b674046011b234297865dfb00b982bc.png)

穆罕默德·拉赫马尼在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在本文中，我将讨论如何为流行的 Web 应用程序安全工具 Burp Suite 开发一个扩展。Burp 套件扩展是 Burp 套件 web 安全测试平台的自定义插件。Burp Suite 是安全专家用来测试 web 应用程序和 API 安全性的流行工具。扩展允许用户通过添加新的特性和功能来扩展 Burp Suite 的功能。例如，一个扩展可能会向 Burp Suite 用户界面添加一个新选项卡，或者向平台添加一种新的扫描或攻击类型。扩展通常是用 Java 编程语言编写的，可以使用 Burp Suite Extender 工具来加载和管理它们。

要编写一个 Burp Suite 扩展，您首先需要对 Java 编程语言有一个基本的了解。一旦你有了这些，你可以按照以下步骤开始:

1.  下载并在您的计算机上安装 Java 开发工具包(JDK)。这将为您提供编写和编译 Java 代码所需的工具。
2.  下载并安装最新版本的 Burp 套件。这将允许您访问 Burp Suite Extender，您将使用它来加载和管理您的扩展。
3.  打开 Burp Suite Extender 并单击“添加”按钮创建新的扩展。
4.  选择“扩展类型”为“Java ”,并给你的扩展命名。
5.  在“扩展类”字段中，输入扩展类的完全限定名。这应该是包含扩展的 main()方法的 Java 类的名称。
6.  单击“选择文件”来选择包含您的扩展类的 JAR 文件。这应该是您在编译 Java 代码时创建的 JAR 文件。
7.  单击“Next”并输入任何必要的详细信息，例如扩展的配置文件的名称和位置。
8.  再次单击“下一步”并查看您的扩展设置摘要。如果一切正常，单击“完成”将您的扩展加载到 Burp Suite 中。

加载扩展后，您可以使用 Burp Suite Extender 来管理它，并查看它生成的任何输出或错误消息。您还可以使用 Burp Suite 中的其他工具，如代理和中继器，来测试您的扩展，并查看它在真实场景中的表现。

![](img/f9feb97fd860efa1d85dbd24027074f2.png)

照片由[穆罕默德·拉赫马尼](https://unsplash.com/@afgprogrammer?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

以下是开发 Burp 套件扩展的一些代码示例:

1.  Java: 可以使用 Java 编程语言开发 Burp 套件扩展。Burp Suite Extender API 提供了一组 Java 接口，允许您开发可以加载到 Burp Suite 中的自定义扩展。
2.  **Python:** Burp Suite 还支持使用 Python 编程语言开发扩展。Burp Suite Extender API 提供了一组 Python 类，允许您开发可加载到 Burp Suite 中的自定义扩展。
3.  **JavaScript:** Burp Suite 还支持使用 JavaScript 编程语言开发扩展。Burp Suite Extender API 提供了一组 JavaScript 类，允许您开发可以加载到 Burp Suite 中的自定义扩展。

要开始开发 Burp Suite 扩展，您需要对计划使用的编程语言有一个基本的了解，并对 web 应用程序安全性和 Burp Suite Extender API 有很好的理解。您可能还会发现参考 Burp Suite 团队提供的文档和示例以及任何其他在线资源和教程会有所帮助。

> **Java :**

![](img/543f8ae7deb358ed28a353a1dc000bf4.png)

米歇尔·勒恩斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

下面是一个用 Java 编写的简单 Burp Suite 扩展的示例，它记录了对 Burp Suite 控制台的所有请求和响应:

```
import burp.*;

public class Logger implements IHttpListener {
  private IBurpExtenderCallbacks callbacks;
  private IExtensionHelpers helpers;

  public Logger(IBurpExtenderCallbacks callbacks) {
    this.callbacks = callbacks;
    this.helpers = callbacks.getHelpers();
  }

  @Override
  public void processHttpMessage(int toolFlag, boolean messageIsRequest, IHttpRequestResponse messageInfo) {
    if (messageIsRequest) {
      // Log the request to the console
      callbacks.printOutput(helpers.bytesToString(messageInfo.getRequest()));
    } else {
      // Log the response to the console
      callbacks.printOutput(helpers.bytesToString(messageInfo.getResponse()));
    }
  }
}
```

这个扩展实现了`IHttpListener`接口并覆盖了`processHttpMessage`方法，每当 Burp Suite 处理请求或响应时都会调用这个方法。该扩展使用`callbacks.printOutput`方法将请求或响应记录到 Burp Suite 控制台。

下面是一个用 Java 编写的 Burp Suite 扩展的示例，它将所有请求和响应记录到一个文件中:

```
import burp.*;

import java.io.File;
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Paths;

public class FileLogger implements IHttpListener {
  private IBurpExtenderCallbacks callbacks;
  private IExtensionHelpers helpers;
  private File logFile;

  public FileLogger(IBurpExtenderCallbacks callbacks) {
    this.callbacks = callbacks;
    this.helpers = callbacks.getHelpers();
    this.logFile = new File("log.txt");
  }

  @Override
  public void processHttpMessage(int toolFlag, boolean messageIsRequest, IHttpRequestResponse messageInfo) {
    if (messageIsRequest) {
      // Log the request to the file
      try {
        Files.write(Paths.get(logFile.getAbsolutePath()), messageInfo.getRequest());
      } catch (IOException e) {
        e.printStackTrace();
      }
    } else {
      // Log the response to the file
      try {
        Files.write(Paths.get(logFile.getAbsolutePath()), messageInfo.getResponse());
      } catch (IOException e) {
        e.printStackTrace();
      }
    }
  }
}
```

这个扩展实现了`IHttpListener`接口并覆盖了`processHttpMessage`方法，每当 Burp Suite 处理请求或响应时都会调用这个方法。该扩展使用 Java NIO 库中的`Files.write`方法将请求或响应记录到文件中。

要使用这个扩展，您需要使用 Java 编译器将其编译成一个`.class`文件，并使用 Extender 选项卡将其加载到 Burp Suite 中。

下面是一个用 Java 编写的 Burp Suite 扩展的示例，它向 Burp Suite UI 添加了一个自定义选项卡:

```
import burp.*;

import javax.swing.*;
import java.awt.*;

public class CustomTab implements ITab {
  private JPanel panel;

  public CustomTab(IBurpExtenderCallbacks callbacks) {
    // Set up the panel
    this.panel = new JPanel();
    this.panel.setLayout(new BorderLayout());

    // Add a label to the panel
    JLabel label = new JLabel("Hello, World!");
    this.panel.add(label, BorderLayout.CENTER);
  }

  @Override
  public String getTabCaption() {
    return "Custom Tab";
  }

  @Override
  public Component getUiComponent() {
    return this.panel;
  }
}
```

这个扩展实现了`ITab`接口并覆盖了`getTabCaption`和`getUiComponent`方法。`getTabCaption`方法返回将出现在 Burp Suite UI 中选项卡上的标题，`getUiComponent`方法返回将显示在选项卡上的面板。

要使用这个扩展，您需要使用 Java 编译器将其编译成一个`.class`文件，并使用 Extender 选项卡将其加载到 Burp Suite 中。

下面是一个用 Java 编写的 Burp Suite 扩展示例，它向 HTTP History 选项卡添加了一个自定义列:

```
import burp.*;

import java.util.ArrayList;
import java.util.List;

public class CustomColumn implements ITableItemInsertionListener {
  private IBurpExtenderCallbacks callbacks;
  private IExtensionHelpers helpers;

  public CustomColumn(IBurpExtenderCallbacks callbacks) {
    this.callbacks = callbacks;
    this.helpers = callbacks.getHelpers();

    // Register the extension as a table item insertion listener
    callbacks.registerTableItemInsertionListener(this);
  }

  @Override
  public void newTableItem(ITableItem item) {
    // Add a custom column to the HTTP History tab
    List<String> customColumn = new ArrayList<>();
    customColumn.add("Custom Column");
    item.addRow(customColumn);
  }
}
```

这个扩展实现了`ITableItemInsertionListener`接口并覆盖了`newTableItem`方法，每当一个新的项目被添加到 HTTP History 选项卡的表中时，就会调用这个方法。该扩展使用`addRow`方法向项目添加一个定制列。

要使用这个扩展，您需要使用 Java 编译器将其编译成一个`.class`文件，并使用 Extender 选项卡将其加载到 Burp Suite 中。

> **Python :**

![](img/38859acba26cb855459bef362778e60b.png)

[大卫·克洛德](https://unsplash.com/fr/@davidclode?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

下面是一个用 Python 编写的简单 Burp Suite 扩展的示例，它记录了对 Burp Suite 控制台的所有请求和响应:

```
from burp import IBurpExtender
from burp import IHttpListener

class BurpExtender(IBurpExtender, IHttpListener):
  def registerExtenderCallbacks(self, callbacks):
    # Set up extension
    self.callbacks = callbacks
    self.helpers = callbacks.getHelpers()

    # Register extension as an HTTP listener
    callbacks.registerHttpListener(self)

  def processHttpMessage(self, toolFlag, messageIsRequest, messageInfo):
    if messageIsRequest:
      # Log the request to the console
      self.callbacks.printOutput(self.helpers.bytesToString(messageInfo.getRequest()))
    else:
      # Log the response to the console
      self.callbacks.printOutput(self.helpers.bytesToString(messageInfo.getResponse()))
```

该扩展实现了`IBurpExtender`和`IHttpListener`接口，并覆盖了`processHttpMessage`方法，每当 Burp Suite 处理请求或响应时，都会调用该方法。扩展使用`callbacks.printOutput`方法将请求或响应记录到 Burp Suite 控制台。

要使用此扩展，您需要将其保存到扩展名为`.py`的文件中，并使用 Extender 选项卡将其加载到 Burp Suite 中。

下面是一个用 Python 编写的 Burp Suite 扩展的示例，它将所有请求和响应发送到一个 Slack 通道:

```
from burp import IBurpExtender
from burp import IHttpListener
import requests

SLACK_WEBHOOK_URL = "https://hooks.slack.com/services/YOUR/WEBHOOK/URL"

class BurpExtender(IBurpExtender, IHttpListener):
  def registerExtenderCallbacks(self, callbacks):
    # Set up extension
    self.callbacks = callbacks
    self.helpers = callbacks.getHelpers()

    # Register extension as an HTTP listener
    callbacks.registerHttpListener(self)

  def processHttpMessage(self, toolFlag, messageIsRequest, messageInfo):
    if messageIsRequest:
      # Send the request to Slack
      request = self.helpers.bytesToString(messageInfo.getRequest())
      self.sendToSlack(request)
    else:
      # Send the response to Slack
      response = self.helpers.bytesToString(messageInfo.getResponse())
      self.sendToSlack(response)

  def sendToSlack(self, message):
    # Send a message to a Slack channel using a webhook
    payload = {
      "text": message
    }
    requests.post(SLACK_WEBHOOK_URL, json=payload)
```

该扩展实现了`IBurpExtender`和`IHttpListener`接口，并覆盖了`processHttpMessage`方法，每当 Burp Suite 处理请求或响应时，都会调用该方法。扩展使用 webhook 和`requests`库向 Slack 通道发送请求或响应。

要使用这个扩展，您需要安装`requests`库并将该扩展保存到一个扩展名为`.py`的文件中。然后，您可以使用 Extender 选项卡将其加载到 Burp Suite 中。你还需要用你的 Slack webhook 的 URL 替换`SLACK_WEBHOOK_URL`变量。

下面是一个用 Python 编写的 Burp Suite 扩展的示例，它向 Burp Suite 上下文菜单添加了一个自定义菜单项:

```
from burp import IBurpExtender
from burp import IContextMenuFactory

class BurpExtender(IBurpExtender, IContextMenuFactory):
  def registerExtenderCallbacks(self, callbacks):
    # Set up extension
    self.callbacks = callbacks
    self.helpers = callbacks.getHelpers()

    # Register extension as a context menu factory
    callbacks.registerContextMenuFactory(self)

  def createMenuItems(self, invocation):
    # Create a menu item that will be displayed in the context menu
    menuItem = JMenuItem("Custom Menu Item")

    # Define the action that will be taken when the menu item is clicked
    def actionPerformed(event):
      # Get the selected message
      request = invocation.getSelectedMessages()[0]
      print(request)

    # Add the action listener to the menu item
    menuItem.addActionListener(actionPerformed)

    # Return the menu item
    return [menuItem]
```

这个扩展实现了`IContextMenuFactory`接口并覆盖了`createMenuItems`方法。`createMenuItems`方法返回一个菜单项列表，当用户右键单击 Burp Suite 中的请求或响应时，该列表将显示在上下文菜单中。该扩展定义了一个动作侦听器，当单击菜单项时将触发该侦听器，在这种情况下，该侦听器将选定的消息打印到控制台。

要使用此扩展，您需要将其保存到扩展名为`.py`的文件中，并使用 Extender 选项卡将其加载到 Burp Suite 中。

> **JavaScript :**

![](img/32e85d6e0a120a8c1bb814e57c913353.png)

奥斯卡·伊尔迪兹在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

下面是一个用 JavaScript 编写的简单 Burp Suite 扩展的示例，它记录了对 Burp Suite 控制台的所有请求和响应:

```
importClass(org.parosproxy.paros.extension.ExtensionAdaptor);
importClass(org.parosproxy.paros.network.HttpMessage);

var extension = new ExtensionAdaptor() {
  processHttpMessage: function(toolFlag, messageIsRequest, messageInfo) {
    if (messageIsRequest) {
      // Log the request to the console
      console.log(messageInfo.getRequest());
    } else {
      // Log the response to the console
      console.log(messageInfo.getResponse());
    }
  }
};
```

这个扩展覆盖了`processHttpMessage`方法，每当 Burp Suite 处理请求或响应时都会调用这个方法。扩展使用`console.log`方法将请求或响应记录到 Burp Suite 控制台。

要使用此扩展，您需要将其保存到扩展名为`.js`的文件中，并使用 Extender 选项卡将其加载到 Burp Suite 中。

下面是一个用 JavaScript 编写的 Burp Suite 扩展的示例，它将所有请求和响应发送到一个 Discord 通道:

```
importClass(org.parosproxy.paros.extension.ExtensionAdaptor);
importClass(org.parosproxy.paros.network.HttpMessage);

var Discord = Java.type("com.google.gson.Gson");
var HttpURLConnection = Java.type("java.net.HttpURLConnection");
var URL = Java.type("java.net.URL");

var DISCORD_WEBHOOK_URL = "https://discordapp.com/api/webhooks/YOUR/WEBHOOK/URL";

var extension = new ExtensionAdaptor() {
  processHttpMessage: function(toolFlag, messageIsRequest, messageInfo) {
    if (messageIsRequest) {
      // Send the request to Discord
      var request = messageInfo.getRequest();
      this.sendToDiscord(request);
    } else {
      // Send the response to Discord
      var response = messageInfo.getResponse();
      this.sendToDiscord(response);
    }
  },

  sendToDiscord: function(message) {
    // Send a message to a Discord channel using a webhook
    var connection = new URL(DISCORD_WEBHOOK_URL).openConnection();
    connection.setDoOutput(true);
    connection.setRequestMethod("POST");
    connection.setRequestProperty("Content-Type", "application/json");
    var payload = {
      "content": message
    };
    connection.getOutputStream().write(new Discord().toJson(payload).getBytes("UTF-8"));
    connection.getInputStream().read();
  }
};
```

这个扩展覆盖了`processHttpMessage`方法，每当 Burp Suite 处理请求或响应时都会调用这个方法。扩展使用 webhook 和来自 Java API 的`HttpURLConnection`和`Gson`类向 Discord 通道发送请求或响应。

要使用此扩展，您需要将其保存到扩展名为`.js`的文件中，并使用 Extender 选项卡将其加载到 Burp Suite 中。您还需要用您的 Discord webhook 的 URL 替换`DISCORD_WEBHOOK_URL`变量。

下面是一个用 JavaScript 编写的 Burp Suite 扩展示例，它向 Burp Suite 工具栏添加了一个自定义按钮:

```
importClass(org.parosproxy.paros.extension.ExtensionAdaptor);
importClass(java.awt.event.ActionListener);
importClass(javax.swing.JButton);

var extension = new ExtensionAdaptor() {
  // Initialize the extension
  initExtension: function() {
    // Create a button
    var button = new JButton("Custom Button");

    // Define the action that will be taken when the button is clicked
    var actionPerformed = function(event) {
      console.log("Button clicked!");
    };

    // Add the action listener to the button
    button.addActionListener(new ActionListener(actionPerformed));

    // Add the button to the toolbar
    this.customizeUiComponent(button);
    this.addToToolbar(button);
  }
};
```

这个扩展覆盖了`initExtension`方法，该方法在加载扩展时被调用。该扩展创建了一个按钮，并定义了一个当按钮被点击时将被触发的动作监听器。然后扩展使用`customizeUiComponent`和`addToToolbar`方法将按钮添加到 Burp Suite 工具栏。

要使用此扩展，您需要将其保存到扩展名为`.js`的文件中，并使用 Extender 选项卡将其加载到 Burp Suite 中。

![](img/c228b745366b4c7732625089b5ab4cc0.png)

照片由 [2H 媒体](https://unsplash.com/@2hmedia?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

以下是一些学习开发 Burp 套件扩展的书籍推荐:

1.  "**黑帽 Python:** 黑客和 Pentesters 的 Python 编程"作者 Justin Seitz:这本书提供了 Python 编程的介绍，并解释了如何使用 Python 构建各种类型的安全工具，包括 Burp Suite 扩展。
2.  “**黑客用 Java:**学习 Java 和渗透测试”作者 John Erik:这本书提供了 Java 编程的介绍，并解释了如何使用 Java 构建各种类型的安全工具，包括 Burp Suite 扩展。
3.  Kevin Cardwell 和 Neil Bergman 的《实用 web 应用渗透测试》:这本书为使用 Burp Suite 进行 Web 应用渗透测试提供了一个深入的指南，并包含了为 Burp Suite 开发定制插件和集成的章节。

有许多资源可以帮助开发 Burp Suite 扩展，包括:

1.  **Burp 套件文档:**这是 Burp 套件的官方文档，提供了关于如何使用不同编程语言开发扩展的详细信息，以及关于可用于扩展的 API 和接口的信息。你可以在 https://portswigger.net/burp/documentation 找到文档。
2.  **Burp Suite extender API:**这是用于开发 Burp Suite 扩展的官方 Java API。它包括扩展可用的类和方法的详细信息，以及代码示例。你可以在 https://portswigger.net/burp/extender/api/的[找到扩展器 API。](https://portswigger.net/burp/extender/api/)
3.  **Burp Suite extender 示例:**这些是 PortSwigger 提供的示例扩展，演示了如何使用 Burp Suite extender API 的各种功能。你可以在[https://portswigger.net/burp/extender/examples/](https://portswigger.net/burp/extender/examples/)找到例子。
4.  **在线教程和博客:**有许多在线教程和博客为开发 Burp 套件扩展提供指导。这些是学习最佳实践和开始扩展开发的有用资源。

![](img/8bc6ae726f84784979482d3057af547a.png)

路西法·晨星和克洛伊·德克尔警探

在本文中，我试图回答如何为 Burp Suite 编写扩展的问题。我希望示例代码和附加资源对您有所帮助。保重，在我的下一篇文章中再见。

## 来自 Infosec 的报道:Infosec 每天都有很多内容，很难跟上。[加入我们的每周简讯](https://weekly.infosecwriteups.com/)以 5 篇文章、4 个线程、3 个视频、2 个 GitHub Repos 和工具以及 1 个工作提醒的形式免费获取所有最新的 Infosec 趋势！