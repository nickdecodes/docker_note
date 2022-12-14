# docker-note

- [官网](https://www.docker.com)
- [安装](#安装)
- [使用](#使用)
- [问题](#问题)

------

容器现在风靡于 IT 界 —— 这很好理解。容器是轻量级的，包含应用运行所需所有东西（代码、库、运行时环境、系统设置，以及依赖关系）的独立的包。每个容器都部署于它自己的 CPU、内存、块 I/O，以及网络资源上，所有这些都不依赖于某个内核和操作系统。这也是容器与虚拟机之间最大的不同；相比之下，虚拟机是一个运行于宿主机操作系统上的完整的操作系统平台，而容器不是。

容器允许你以一种前所未有的方式扩展交付能力（不管内部还是外部的）。例如，你可以快速部署多个 NGINX 实例（甚至可以应用于多个不同的阶段 —— 比如开发和生产阶段）。跟虚拟机不同，容器不会消耗太多系统资源。

[Docker](https://www.docker.com/) 使得创建、部署，和管理容器变得特别简单。更好的是，安装和使用 Docker 在 Linux 平台上特别的方便。

## 安装

在你安装前，你需要更新 `apt` 然后进行必要的升级。一定要注意，若系统内核升级了，你会需要重启系统。因此最好挑个服务器能重启的时间进行。

运行下面命令更新 `apt`：

```bash
sudo apt update
```

完成后，使用命令升级系统：

```bash
sudo apt upgrade
```

若内核升级了，你需要用下面命令重启服务器：

```bash
sudo reboot
```

若内核没有升级，你就可以安装 Docker 了（无需重启）。安装 Docker 的命令为：

```bash
sudo apt install docker.io
```

若你使用的是其它 Linux 发行版，当尝试用相应的包管理器来安装时却发现没有 `docker.io` 包，则你应该安装 `docker` 包。例如，在 [Fedora](https://getfedora.org/) 上安装应该用命令：

```bash
sudo dnf install docker
```

若你使用的是 [CentOS](https://www.linuxidc.com/topicnews.aspx?tid=14) 7，那么最好使用安装脚本来安装 docker。首先使用命令 `sudo yum check-update` 更新系统。升级完后，输入下面命令来下载并运行所需的脚本：

```bash
curl -fsSL https://get.docker.com/ | sh
```

默认情况下，只有管理员权限能够运行 docker 命令。考虑到安全问题，你不会想用 root 用户或使用 `sudo` 来运行 Docker 的。要解决这个问题，你需要将自己的用户加入到 docker 组中。命令如下：

```bash
sudo usermod-a -G docker $USER
```

完成操作后，登出系统然后再重新登录，应该就搞定了。不过若你的平台是 [Fedora](https://www.linuxidc.com/topicnews.aspx?tid=5)，则添加用户到 docker 组时会发现这个组是不存在的。那该怎么办呢？你需要首先创建这个组。命令如下：

```bash
sudo groupadd docker &&sudo gpasswd -a ${USER} docker &&sudosystemctl restart docker
newgrp docker
```

登出后再登录。就可以开始用 Docker 了。

## 使用

启动，暂停以及启用 Docker

安装好 Docker 后，你可以让系统在启动时自动启动 Docker 守护进程。使用下面两个命令来实现这一点：

```bash
sudo systemctl start docker
sudo systemclt status docker
sudo systemctl enable docker
```

若需要暂停或重启 Docker 守护进程，则命令为：

```bash
sudo systemctl stop docker
sudo systemctl restart docker
```

现在可以用 Docker 来部署容器了。

由于网络原因，我们在pull Image 的时候，从Docker Hub上下载会很慢。。。所以，国内的Docker爱好者们就添加了一一些国内的镜像（mirror）,方便大家使用。

1. [登录阿里开发者平台：https://dev.aliyun.com/search.html](https://www.cnblogs.com/atuotuo/p/6264800.html#_label0)
2. [点击“创建我的容器镜像”](https://www.cnblogs.com/atuotuo/p/6264800.html#_label1)
3. [注册/登录后，进入Docker 镜像仓库 (https://cr.console.aliyun.com/#/imageList)，选中加速器 Tab](https://www.cnblogs.com/atuotuo/p/6264800.html#_label2)
4. [根据个人需要, 选择运行Docker 的OS，按照要求修改Docker配置文件](https://www.cnblogs.com/atuotuo/p/6264800.html#_label3)

使用docker命令

查看镜像安装情况/列出本地镜像

```bash
docker images
```

从镜像仓库中拉取或者更新指定镜像

```bash
docker pull
ex:
	docker pull mysql:8.0
```

重启docker

```bash
systemclt restart docker
```

列出容器

```bash
docker ps
-a :显示所有的容器，包括未运行的。
```

进入镜像

```bash
docker exec
ex:
	docker exec -i -t  mynginx /bin/bash：在容器mynginx中开启一个交互模式的终端，即通过SSH协议进入容器（使用exit退出后容器不停止运行）
```

停止容器服务

```bash
docker rm 容器服务别名  or 容器id
```

移除已经安装镜像

```bash
docker rmi 镜像名：tag  or  镜像id
```

停止容器服务

```bash
docker stop 容器服务别名  or 容器id
```

启动容器服务

```bash
docker start 容器服务别名  or 容器id
```

创建一个新的容器并运行一个命令

```bash
docker run
-d: 后台运行容器，并返回容器ID
-p: 端口映射，格式为：主机(宿主)端口:容器端口
--name="nginx-lb": 为容器指定一个名称
-v：目录映射，格式为：主机目录:容器目录
ex:
	docker run --name test -ti ubuntu /bin/bash  #复制ubuntu容器并且重命名为test且运行，然后以伪终端交互式方式进入容器，运行bash
	docker run -d -p 2222:22 --name test soar/centos:7.1  #以镜像soar/centos:7.1创建名为test的容器，并以后台模式运行，并做端口映射到宿主机2222端口，P参数重启容器宿主机端口会发生改变
```

进入容器执行

```bash
docker attach
```

启动一个或多少已经被停止的容器

```bash
docker start
```

停止一个运行中的容器

```bash
docker stop
```

杀掉一个运行中的容器（强制）

```bash
docker kill
```

重启容器

```bash
docker restart
```

列出指定的容器的端口映射，或者查找将PRIVATE_PORT NAT到面向公众的端口。

```bash
docker port
```

获取容器的日志

```bash
docker logs
-f : 跟踪日志输出
--since :显示某个开始时间的所有日志
-t : 显示时间戳
--tail :仅列出最新N条容器日志
```

```bash
docker cp：拷贝主机docker cp /www/runoob 96f7f14e99ab:/www/
```

## 问题

解决jupyter notebook问题：socket.error: [Errno 99] Cannot assign requested address6

```c
// 创建Jupyter notebook的配置文件jupyter_notebook_config.py，在终端中输入：

jupyter notebook --generate-config
// 或者版本不同
ipython profile create
// 在到命令输出的位置找到jupyter_notebook_config.py，sudo gedit或者vim打开，有时候要先chmod赋予权限，把该文件的：

c.NotebookApp.ip = '127.0.0.1'
c.NotebookApp.open_browser = True
// 这两个去除注释，打上自己的端口，我用的是127.0.0.1即可
```

