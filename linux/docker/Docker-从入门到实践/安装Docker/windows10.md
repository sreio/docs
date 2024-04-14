# Windows 10 安装 Docker

## 系统要求

[Docker Desktop for Windows](https://docs.docker.com/docker-for-windows/install/) 支持 64 位版本的 Windows 10 Pro，且必须开启 Hyper-V（若版本为 v1903 及以上则无需开启 Hyper-V），或者 64 位版本的 Windows 10 Home v1903 及以上版本。

## 安装

**手动下载安装**

点击以下 [链接](https://desktop.docker.com/win/main/amd64/Docker%20Desktop%20Installer.exe) 下载 Docker Desktop for Windows。

下载好之后双击 `Docker Desktop Installer.exe` 开始安装。

**使用 [winget](https://docs.microsoft.com/zh-cn/windows/package-manager/) 安装**

```powershell
$ winget install Docker.DockerDesktop
```

## 在 WSL2 运行 Docker 

若你的 Windows 版本为 Windows 10 专业版或家庭版 v1903 及以上版本可以使用 WSL2 运行 Docker，具体请查看 [Docker Desktop WSL 2 backend](https://docs.docker.com/docker-for-windows/wsl/)。

## 运行

在 Windows 搜索栏输入 **Docker** 点击 **Docker Desktop** 开始运行。

![](../img/2-7.png)

Docker 启动之后会在 Windows 任务栏出现鲸鱼图标。

![](../img/2-8.png)

等待片刻，当鲸鱼图标静止时，说明 Docker 启动成功，之后你可以打开 PowerShell 使用 Docker。

> 推荐使用 [Windows Terminal](https://docs.microsoft.com/zh-cn/windows/terminal/get-started) 在终端使用 Docker。

## 镜像加速

如果在使用过程中发现拉取 Docker 镜像十分缓慢，可以配置 Docker [国内镜像加速](mirror.md)。

## 参考链接

* [官方文档](https://docs.docker.com/docker-for-windows/install/)
* [WSL 2 Support is coming to Windows 10 Versions 1903 and 1909](https://devblogs.microsoft.com/commandline/wsl-2-support-is-coming-to-windows-10-versions-1903-and-1909/)