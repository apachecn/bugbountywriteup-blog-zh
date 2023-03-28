# 部署到 Google Cloud 中的 Kubernetes:挑战实验室

> 原文：<https://infosecwriteups.com/deploy-to-kubernetes-in-google-cloud-challenge-lab-47c301b1fccc?source=collection_archive---------0----------------------->

![](img/dccf9fd603219e805344ce1df720c1f6.png)![](img/0055ec86d8280bdc2b8008f4490053b2.png)

您可以在这里找到您的项目 id

# 任务 1:创建 Docker 映像并存储 Docker 文件

![](img/b00665711cc524a37b95d23b42c23bd6.png)

# 解决办法

我们需要运行这个命令`source <(gsutil cat gs://cloud-training/gsp318/marking/setup_marking.sh)`来安装谷歌检查我们进度所需的脚本

![](img/b4b60cf25cb34bd4ccf56b48595fbb27.png)

现在我们需要运行这个命令来克隆来自 github `gcloud source repos clone valkyrie-app --project={put your project id here}`
的任务文件(不要忘记输入你的项目 id)

![](img/f9089abd1e129aaf850f82cd382ecbe8.png)

现在我们将转到 valkyrie-app 目录`cd valkyrie-app`，用这个命令`cat > Dockerfile <<EOF`创建一个 docker 文件

我们将粘贴任务说明中给出的配置:

```
FROM golang:1.10
WORKDIR /go/src/app
COPY source .
RUN go install -v
ENTRYPOINT ["app","-single=true","-port=8080"]
EOF
```

![](img/0d236aa40b1e2eb16f8b69ce8b0e5176.png)

现在使用这个命令`docker build -t valkyrie-app:v0.0.1 .`构建图像

![](img/28f03904b19e3c65e63d9410ae47220d.png)

现在，我们将运行之前下载的脚本，以便检查我们是否做了所有正确的事情

```
cd ~/marking                       
./step1.sh
```

![](img/ec453ec1ac173865384780357b592176.png)

现在按下实验页面上的`check my progress`，它应该会变成绿色

![](img/50ca151bf9d38635de1c51239b0c85d7.png)

# 解决办法

我使用这个命令将我的目录更改为`valkyrie-app`

```
cd ..                      
cd valkyrie-app
```

现在我们将使用这个命令`docker run -p 8080:8080 --name valkyrie-app valkyrie-app:v0.0.1 &`来运行我们在任务 1 中构建的 docker 映像

![](img/50bae814cce6f02ca5db9509ba7647a8.png)![](img/fcf27137870f31ea6cbb114977185f90.png)

现在单击 web 预览

现在打开一个新的云 shell，运行我们之前下载的脚本，这样它会检查我们是否做对了所有事情

```
cd ~/marking                     
./step2.sh
```

![](img/9b05929f12d6dbcfc2962188fb681b13.png)

现在按下实验页面上的`check my progress`，它应该会变成绿色

![](img/6574492f14885df45357ddb3b72cdfab.png)

# 解决办法

现在我们将推送 Docker 图像

`docker tag valkyrie-app:v0.0.1 gcr.io/{put your project id here}/valkyrie-app:v0.0.1`

(不要忘记填写您的项目 id)

`docker images`

![](img/9ff04411a68b6542cef59d74beb1adc1.png)

`docker push gcr.io/{put your project id here}/valkyrie-app:v0.0.1`

(不要忘记填写您的项目 id)

![](img/a7b3fb9e553814c6c35bba5b1bff3c97.png)

现在按下实验页面上的`check my progress`，它应该会变成绿色

![](img/54c4218974b152a38d115ebc252d1986.png)

# 解决办法

首先，通过使用这些命令，我们应该在`valkyrie-app/`中的`k8s`目录中

`cd valkyrie-app/`

`cd k8s`

使用`gcloud container clusters get-credentials valkyrie-dev --zone us-east1-d`获取集群的身份验证凭据

![](img/2f13d0d070493ccfe5f19f91dd9b25a2.png)

现在我们需要使用`nano deployment.yaml`编辑`deployment.yaml`

![](img/cd54a2ce49df710d5a666e018eacecd8.png)

将`IMAGE_HERE`更改为[gcr.io/](http://gcr.io/){在此输入你的项目 id }/瓦尔基里-app:v0.0.1

(不要忘记填写您的项目 id)

![](img/774e818c78a5f82fce2f551fde79242f.png)

然后按`Ctrl o`保存然后按`enter`然后按`Ctrl x`退出

现在我们将使用:

```
kubectl create -f deployment.yaml                      
kubectl create -f service.yaml
```

部署部署。YAML 和服务。YAML

![](img/02e9fdaa565c738fb65446afb545e7eb.png)

现在按下实验页面上的`check my progress`，它应该会变成绿色

![](img/a3c9147a8075c8145fba9ae4d1ab96ef.png)

# 解决办法

将副本从 1 个增加到 3 个

kubectl 规模部署瓦尔基里-开发-副本 3

![](img/f53592c5f2a8382724b439abb086c7e0.png)

现在我们使用`cd ..`进入`valkyrie-app`目录

并使用下面的 git 命令将名为 kurt-dev 的分支合并到 master 中:

`git merge origin/kurt-dev`

![](img/980fc42dbb99c63154d2323cddb73856.png)

现在我们将构建并推送标签为`2.0.0`的新版本

`docker build -t valkyrie-app:v0.0.2 .`

![](img/cc1daab165341fbe93dc0fb0d529dbf3.png)

`docker tag valkyrie-app:v0.0.2 gcr.io/{put your project id here}/valkyrie-app:v0.0.2`

(不要忘记填写您的项目 id)

`docker images`

![](img/e8102cb2fa532617c07daef3a764ca28.png)

`docker push gcr.io/{put your project id here}/valkyrie-app:v0.0.2`

(不要忘记填写您的项目 id)

![](img/3f16d51bc0746d642b8ccc74aef523ee.png)

使用以下命令将后端和前端的图像标签从`v0.0.1`更改为`v0.0.2`:

`kubectl set image deployment valkyrie-dev backend=gcr.io/{put your project id here}/valkyrie-app:v0.0.2 frontend=gcr.io/{put your project id here}/valkyrie-app:v0.0.2`
(别忘了写上你的项目 id)

![](img/9df42fc9869421cc8c1f7482fbf61ffe.png)

现在，按下实验页面中的`check my progress`,它应该会变成绿色

![](img/e7966d561a1bd0877ef14097fc119340.png)

# 解决办法

要知道密码，请运行以下命令

`printf $(kubectl get secret cd-jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo`

![](img/203a13fed160227e99dda1cb56157fc8.png)

为了避免以后出现错误，请运行以下命令来终止任何正在运行的容器

docker ps

![](img/f7efeee9879774144c7122a2a4dcdb6c.png)

码头集装箱 kill $(码头 ps -aq)

![](img/d9ccdf4c9ac4b2eb4c37087bc5d2c0a7.png)

使用以下命令连接到 Jenkins 控制台:

```
export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/component=jenkins-master" -l "app.kubernetes.io/instance=cd" -o jsonpath="{.items[0].metadata.name}")
kubectl port-forward $POD_NAME 8080:8080 >> /dev/null &
```

![](img/c0a59b45946b9a9ac7af045713235bf5.png)

点击云壳中的 Web 预览按钮，然后点击“在 8080 端口上预览”连接到 Jenkins 控制台。

![](img/4b77950db837bbcb6ffc1e994516aec0.png)

用户名:`admin`

密码:我们之前运行了这个命令作为密码`printf $(kubectl get secret cd-jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo`

在 Jenkins 用户界面中，点击左侧导航栏中的`Manage Jenkins`。

![](img/3cb5c55b1ecfb7db12c4fe39f42d0d39.png)

在 Jenkins 用户界面中，单击左侧导航栏中的`Manage Credentials`。

![](img/65825bb941a0928adb056e72731545f4.png)

点击`Jenkins`

![](img/b9f97770cd3239fc88998f7f9df19db6.png)

点击`Global credentials (unrestricted).`

![](img/ecffdca44e03674347e314fe5b6d2bca.png)

点击左侧导航栏中的`Add Credentials`。

![](img/efc00a4c200651ce9f5c5aa99c10c54b.png)

从`Kind drop-down`中选择`Google Service Account from metadata`

![](img/6a3215acb2d150e89e64c94eb80075eb.png)

点击`OK`。

![](img/4b2e2dd57680633c30596ff79824b2f6.png)

再次进入仪表板，点击`new item`

![](img/ec0c5eb3f0116de46aad1707c9eb63ea.png)

将项目命名为`valkyrie-app`，然后选择`Multibranch Pipeline`选项并点击`OK`。

![](img/55f7f7dfcf244fadd1823e98e0e80dab.png)

点击`Add Source`。

![](img/ccfaf2048d05f895c51f332fda1be19a.png)

选择`git`。

![](img/96095c5d5f3b8aa4823025397fbade84.png)

将您的样例应用程序存储库中的 HTTPS 克隆 URL`https://source.developers.google.com/p/{put your project id here}/r/valkyrie-app`粘贴到项目存储库字段中

(不要忘记填写您的项目 id)

![](img/42e7d6305331bbaadc17873df3bf4f40.png)

从`Credentials`下拉列表中，选择您在前面的步骤中添加服务帐户时创建的凭据的名称。

![](img/536b6985a1988debbe8a4ed7c51138cd.png)

在`Scan Multibranch Pipeline Triggers`部分，勾选`Periodically if not otherwise run`框，将间隔值设置为`1 minute`。

![](img/7347aacb106a2a3703878b7314fdb419.png)

点击`apply`然后点击`save`。

![](img/321867c9b9c62ad467ccd1ae6db191d7.png)

纳米`Jenkinsfile`。

![](img/efe3e92ab1be8f9dd8e019aca49c77d5.png)

用您的 GCP 项目 ID 替换`YOUR_PROJECT`

![](img/a8494d592ecfdca6860fc1e3b1cfd136.png)

纳米`source/html.go`

提交并推动更改:

```
git config --global user.email $PROJECT
git config --global user.name $PROJECTgit add *
git commit -m 'green to orange'
git push origin master
```

现在按下实验页面上的`check my progress`，它应该会变成绿色

# 我陷入困境时使用的资源

chriskyfung GitHub 回购

# 我喜欢与不同的人联系，所以如果你想打招呼，我会很高兴见到你！:)

[**领英**](https://www.linkedin.com/in/noureldin-ehab-a57940190/)

[**推特**](https://twitter.com/Nouureldin_Ehab)

🔈🔈Infosec Writeups 正在组织其首次虚拟会议和网络活动。如果你对信息安全感兴趣，这是最酷的地方，有 16 个令人难以置信的演讲者和 10 多个小时充满力量的讨论会议。 [**查看更多详情，在此注册。**](https://iwcon.live/)

[](https://iwcon.live/) [## IWCon2022 - Infosec 书面报告虚拟会议

### 与世界上最优秀的信息安全专家建立联系。了解网络安全专家如何取得成功。将新技能添加到您的…

iwcon.live](https://iwcon.live/)