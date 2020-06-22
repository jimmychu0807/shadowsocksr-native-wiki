如果你的 VPS 主机启用了 IPv6 功能, 那么所有从域名字符串解析成 IP 地址的操作均会返回 IPv6 格式的地址, 
而有些目标地址指向的服务器并不支持 IPv6 地址类型, 导致连接失败, 因此我们有必要在我们的 VPS 上禁用 IPv6 功能.

在 **Ubuntu** 系统上的操作如下.

用 vi 编辑文件 /etc/sysctl.conf
```
sudo vi /etc/sysctl.conf
```

在文件的最后加入下面的行。
```
# IPv6 disabled
net.ipv6.conf.all.disable_ipv6=1
net.ipv6.conf.default.disable_ipv6=1
net.ipv6.conf.lo.disable_ipv6=1
```

保存并关闭, 然后重启 `sysctl`
```
sudo sysctl -p
```

检查 `ifconfig` 的输出，这里应该没有 IPv6 地址了。
> 如果 `ifconfig` 尚未安装, 就用 `sudo apt install net-tools` 命令装上

```
ifconfig
```
```
eth0 Link encap:EthernetHWaddr08:00:27:5f:28:8b
inet addr:192.168.1.3Bcast:192.168.1.255Mask:255.255.255.0
UP BROADCAST RUNNING MULTICAST MTU:1500Metric:1
RX packets:1346 errors:0 dropped:0 overruns:0 frame:0
TX packets:965 errors:0 dropped:0 overruns:0 carrier:0
collisions:0 txqueuelen:1000
RX bytes:1501691(1.5 MB) TX bytes:104883(104.8 KB)
```

如果不行，尝试重启系统并再次检查 `ifconfig`
