> ip 是一个用来查询或维护 路由（ routing ）、网络设备（ device ）、策略路由（ policy routing ）和 隧道（ tunnel ）的网络工具。

本文提供一些用法示例，详细文档请查看手册： [ip(8) - Linux manual page](https://man7.org/linux/man-pages/man8/ip.8.html) ，或者命令行下运行 man 命令：

```bash
$ man ip
```

## 网络设备
ip 命令提供了很多子命令，其中子命令 ip link 用于查询或配置网络设备。

### 查看网卡信息

查看网卡 enp0s8 的详细信息：

```bash
$ ip link show enp0s8
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP mode DEFAULT group default qlen 1000
    link/ether 08:00:27:0e:18:e5 brd ff:ff:ff:ff:ff:ff
```

由此可见，这张网卡的 [MTU](https://fasionchan.com/network/ethernet/mtu/) 是 1500 ，[MAC地址](https://fasionchan.com/network/ethernet/mac/) 是 08:00:27:0e:18:e5 。

### 启用禁用
将网卡 enp0s8 设置为 启动 状态：

```bash
$ ip link set enp0s8 up
```

将网卡 enp0s8 设置为 禁用 状态：

```bash
$ ip link set enp0s8 down
```

### 网卡混杂模式

正常模式下，网卡将过滤目的 MAC地址 不是自己的数据包。 在某些场景，比如网络嗅探，我们需要抓取并分析其他网络数据包。 这时，可以为网卡开启 [混杂模式](https://network.fasionchan.com/zh_CN/latest/protocols/ethernet.html#promisc-mode) 。 该模式开启后后，网卡将接受到达接口的所有数据包，不管 MAC地址 是啥。

运行以下命令，为网卡 enp0s8 开启混杂模式：

```bash
$ sudo ip link set enp0s8 promisc on
```

操作完毕后，再次查询网卡状态，将看到 `PROMISC` 标识：

```bash
$ ip link show enp0s8
3: enp0s8: <BROADCAST,MULTICAST,PROMISC,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP mode DEFAULT group default qlen 1000
    link/ether 08:00:27:0e:18:e5 brd ff:ff:ff:ff:ff:ff
```

## 地址

子命令 addr 用于查看网络设备的地址信息，比如查看所有网卡的地址：

```bash
$ ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:4a:14:df brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global enp0s3
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe4a:14df/64 scope link
       valid_lft forever preferred_lft forever
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:c8:04:83 brd ff:ff:ff:ff:ff:ff
    inet 192.168.56.10/24 brd 192.168.56.255 scope global enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fec8:483/64 scope link
       valid_lft forever preferred_lft forever
```

其中，link 开头的是链路层地址，也就是 MAC地址；inet 开头的是 IP 地址；而 inet6 开头的是 IPv6 地址。

addr 子命令还能给网络设备设置地址，例如给 enp0s8 网卡设置地址 192.168.56.2 ：

```bash
$ ip addr add 192.168.56.2/24 dev enp0s8
```