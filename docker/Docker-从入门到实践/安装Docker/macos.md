# macOS 安装 Docker

## 系统要求

[Docker Desktop for Mac](https://docs.docker.com/docker-for-mac/) 要求系统最低为 macOS Mojave 10.14。

## 安装

### 使用 Homebrew 安装

[Homebrew](https://brew.sh/) 的 [Cask](https://github.com/Homebrew/homebrew-cask) 已经支持 Docker Desktop for Mac，因此可以很方便的使用 Homebrew Cask 来进行安装：

```bash
$ brew install --cask docker
```

### 手动下载安装

如果需要手动下载，请点击以下 [链接](https://desktop.docker.com/mac/main/amd64/Docker.dmg) 下载 Docker Desktop for Mac。

> 如果你的电脑搭载的是 M1 芯片（`arm64` 架构），请点击以下 [链接](https://desktop.docker.com/mac/main/arm64/Docker.dmg) 下载 Docker Desktop for Mac。你可以在 [官方文档](https://docs.docker.com/docker-for-mac/apple-silicon/) 查阅已知的问题。

如同 macOS 其它软件一样，安装也非常简单，双击下载的 `.dmg` 文件，然后将那只叫 [Moby](https://www.docker.com/blog/call-me-moby-dock/) 的鲸鱼图标拖拽到 `Application` 文件夹即可（其间需要输入用户密码）。

![](../img/2-2.png)

## 运行

从应用中找到 Docker 图标并点击运行。

![](../img/2-3.png)

运行之后，会在右上角菜单栏看到多了一个鲸鱼图标，这个图标表明了 Docker 的运行状态。

![](../img/2-4.png)

每次点击鲸鱼图标会弹出操作菜单。

![](../img/2-5.png)

之后，你可以在终端通过命令检查安装后的 Docker 版本。

```bash
$ docker --version
Docker version 20.10.0, build 7287ab3
```

如果 `docker version`、`docker info` 都正常的话，可以尝试运行一个 [Nginx 服务器](https://hub.docker.com/_/nginx/)：

```bash
$ docker run -d -p 80:80 --name webserver nginx
```

服务运行后，可以访问 <http://localhost>，如果看到了 "Welcome to nginx!"，就说明 Docker Desktop for Mac 安装成功了。

![](../img/2-6.png)

要停止 Nginx 服务器并删除执行下面的命令：

```bash
$ docker stop webserver
$ docker rm webserver
```

## 镜像加速

如果在使用过程中发现拉取 Docker 镜像十分缓慢，可以配置 Docker [国内镜像加速](mirror.md)。

## 参考链接

* [官方文档](https://docs.docker.com/docker-for-mac/install/)