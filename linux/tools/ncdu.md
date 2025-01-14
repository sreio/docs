## 1. 简介  
`ncdu`（NCurses Disk Usage）是一个快速且功能强大的磁盘使用分析工具，专为命令行环境设计。它基于`ncurses`库开发，可用来检查磁盘空间占用情况，类似于`du`命令，但提供了交互式界面，可以直观地浏览和管理文件/目录的大小。

### 主要特点：
- 快速扫描目录并显示其占用大小。
- 支持通过键盘导航查看子目录的详细信息。
- 提供删除文件或目录的功能。
- 适合用于服务器和低资源环境。

---

## 2. 在各平台系统的安装方式  

<!-- tabs:start -->
#### **Debian/Ubuntu 系列**  
```terminal
sudo apt update
sudo apt install ncdu
```

#### **CentOS/RHEL 系列**  
如果系统启用了 EPEL 源：
```terminal
sudo yum install epel-release
sudo yum install ncdu
```

#### **Fedora**  
```terminal
sudo dnf install ncdu
```

#### **Arch Linux/Manjaro**  
```terminal
sudo pacman -S ncdu
```

#### **macOS**（通过 Homebrew 安装）  
确保已安装 Homebrew：
```terminal
brew install ncdu
```

#### **Windows**  
需要通过 WSL（Windows Subsystem for Linux）或 Cygwin 等工具使用 Linux 环境，然后按照 Linux 的安装方法操作。

#### **源码安装**（适用于所有平台）  
1. 下载源码：
   ```terminal
   wget https://dev.yorhel.nl/download/ncdu-<version>.tar.gz
   ```
2. 解压并安装：
   ```terminal
   tar -xvzf ncdu-<version>.tar.gz
   cd ncdu-<version>
   ./configure
   make
   sudo make install
   ```
<!-- tabs:end -->

---



### 3. 使用示例  

#### **基本用法**  
1. 扫描当前目录并显示交互式界面：
   ```bash
   ncdu
   ```

2. 扫描指定目录：
   ```bash
   ncdu /path/to/directory
   ```

#### **删除功能**  
在界面中，选中某个文件或目录，按 `d` 键可以删除。

#### **保存扫描结果到文件**  
用于大目录的批量扫描：
```bash
ncdu -o result.ncdu /path/to/directory
```
读取保存的结果：
```bash
ncdu -f result.ncdu
```

#### **忽略指定目录**  
扫描时排除某些文件夹：
```bash
ncdu --exclude /path/to/exclude /path/to/directory
```

#### **仅统计占用的磁盘空间**  
包括硬链接或重复文件：
```bash
ncdu --du /path/to/directory
```

#### **帮助命令**  
查看所有选项：
```bash
ncdu --help
```

--- 

### 示例交互效果：
在命令行执行`ncdu`后，会显示类似以下界面：  
```
--- /home/user ----------------------------
.  10.5 GiB [##########] /Documents
   1.2 GiB [#         ] /Downloads
 512.0 MiB [          ] /Pictures
 128.0 MiB [          ] /Music
>   0.0  B [          ] /EmptyFolder
```
通过键盘上下键导航，按回车进入子目录，按 `q` 退出。
