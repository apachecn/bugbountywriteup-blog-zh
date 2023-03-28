# 如何在 Ubuntu 22.04 LTS 上安装弹性堆栈

> 原文：<https://infosecwriteups.com/how-to-install-elastic-stack-on-ubuntu-22-04-lts-a2f1b00eced?source=collection_archive---------0----------------------->

![](img/c99af8b5271ef7fce99ca59bde239adb.png)

[图像来源](https://blog.knoldus.com/how-to-deploy-elk-stack-on-kubernetes/)

**什么是弹性叠加？**

被称为 elk stack 的 elastic stack 是一组免费的开源工具，旨在实时收集数据、分析和可视化。工具说明如下:

*   Elasticsearch —用于在弹性数据库中存储数据
*   Logstash —用于从不同来源收集数据
*   Kibana —用于可视化弹性搜索中存储的数据

*安装 ELK SIEM***

## # 1:安装所需的模块

更新系统包；
`sudo apt-get update`

先安装 openjdk 等依赖，再安装 elastic stack
`sudo apt-get install openjdk-11-jdk`

`sudo apt-get install apt-transport-https`
`sudo apt-get install curl`
`sudo apt-get install gnupg2`

在一个命令中安装上面列出的所有模块；
`sudo apt-get install openjdk-11-jdk wget apt-transport-https curl gnupg2 -y`

检查 java 版本；
`java -version`

## # 2:在 Ubuntu 上安装和配置 ElasticSearch

首先，我们必须添加一个签名密钥，并且必须在我们的系统中添加存储库，因为 Elasticsearch 没有预装在 Ubuntu 中，我们必须手动完成。

按照以下命令添加 elasticsearch 签名密钥；
`wget -qO — [https://artifacts.elastic.co/GPG-KEY-elasticsearch](https://artifacts.elastic.co/GPG-KEY-elasticsearch) — no-check-certificate | sudo apt-key add -`

接下来使用下面的命令将存储库添加到/etc/apt/sources . list . d/elastic-7 . x . list 中；
`echo “deb [https://artifacts.elastic.co/packages/7.x/apt](https://artifacts.elastic.co/packages/7.x/apt) stable main” | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list`

运行回购后，更新系统包；
`sudo apt-get update -y`

安装 elasticsearch
`sudo apt-get install elasticsearch -y`

对 elesticsearch 配置文件进行修改；
`sudo nano /etc/elasticsearch/elasticsearch.yml`

在“网络”部分更改这些行；
`network.host: localhost
#http.port: 9200(remove ‘#’ here)`

在“发现”部分添加此行；
`discovery.type: single-node`

保存配置文件并退出。

启动 elacticsearch 服务；
`sudo systemctl start elasticsearch`

在系统启动时启用 elacticsearch
`sudo systemctl enable elasticsearch`

检查 elasticsearch 服务状态；
`sudo systemctl status elasticsearch`

## # 3:在 Ubuntu 上安装和配置 Kibana

在 Ubuntu 上安装 kibana


对 kibana 配置文件进行修改；
`sudo nano /etc/kibana/kibana.yml`

删除以下行中的“#”；
`server.port: 5601
server.host: “localhost”
elasticsearch.hosts: [“[http://localhost:9200](http://localhost:9200)"]`

保存配置文件并退出。

启动基巴纳服务；
`sudo systemctl start kibana`

在系统启动时启用 kibana
`sudo systemctl enable kibana`

检查 kibana 服务的状态；
`sudo systemctl status kibana`

## # 4:在 Ubuntu 上安装和配置 Logstash

在 ubuntu 上安装 logstash
`sudo apt-get install logstash`

创建下面的配置文件并插入下面的行来加载日志存储节拍；
`sudo nano /etc/logstash/conf.d/2-beats-input.conf`

> 输入{
> 
> 节拍{
> 
> 端口=> 5044
> 
> }
> 
> }

保存并关闭文件。

创建下面的配置文件并插入以下行来过滤 logstash 输入并生成输出；
`sudo nano /etc/logstash/conf.d/2-elasticsearch-output.conf`

> 输出{
> 
> 弹性搜索{
> 
> hosts => ["localhost:9200"]
> 
> 管理模板= >假
> 
> index = > " {[[@元数据](http://twitter.com/metadata)][节拍]}-% {[[@元数据](http://twitter.com/metadata)][版本]}-%{+YYYY。MM.dd} "
> 
> }
> 
> }

保存并关闭编辑器。

启动 logstash 服务；
`sudo systemctl start logstash`

在系统启动时启用 logstash
`sudo systemctl enable logstash`

停止 logstash 服务；
`sudo systemctl stop logstash`(除非必要，否则不要运行)

检查日志的状态；
`sudo systemctl status logstash`

## # 5:在 Ubuntu 上安装和配置 Filebeat

安装 Filebeat 将日志发送到 Logstash
`sudo apt-get install filebeat`

对 filebeat 配置文件进行修改；
`sudo nano /etc/filebeat/filebeat.yml`

注释下面的行

`#output.elasticsearch:
#Array of hosts to connect to.
#hosts: [“localhost:9200”]`

取消下列行的注释

`output.logstash:
hosts: [“localhost:5044”]`

保存并退出编辑器。

启动 filebeat 服务；
`sudo systemctl start filebeat`

在系统启动时启用 filebeat


检查 filebeat 服务的状态；
`sudo systemctl status filebeat`

启用 filebeat 系统模块；


启用 filebeat logstash 模块；
`sudo filebeat modules enable logstash`

加载索引模板；
`filebeat setup — index-management -E output.logstash.enabled=false -E ‘output.elasticsearch.hosts=[“localhost:9200”]’`

启动 filebeat 服务；
`sudo service filebeat start`

检查 elasticsearch 是否正在从 filebeat 接收 datalog
`curl -XGET [http://localhost:9200/_cat/indices?v](http://localhost:9200/_cat/indices?v)`

使用网址
[http://localhost:5601](http://localhost:5601)访问基巴纳网络界面

如果集成检查出现错误，执行以下命令
启用 filebeat kibana 模块；
`sudo filebeat modules enable kibana`

在 YouTube 上观看分步指南；

如果你喜欢这篇文章，请给我一两个掌声

*来自 Infosec 的报道:Infosec 上每天都会出现很多难以跟上的内容。* [***加入我们的每周简讯***](https://weekly.infosecwriteups.com/) *以 5 篇文章、4 个线程、3 个视频、2 个 Github Repos 和工具以及 1 个工作提醒的形式免费获取所有最新的 Infosec 趋势！*