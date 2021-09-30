## 在 VPS 上安装 SSRoT 服务端

VPS 的操作系统最好是 `ubuntu` `18.04+`

由于涉及到 web 服务器的安装和数字证书的申请, 安装过程较为繁琐复杂, 不可能真的一键到底, 中间有几处需要您输入 `域名`, `端口`, `密码` 等信息, 其中除了 `域名` 必须人工输入以外, 其余都可以使用默认值, 回车即可.

用 `ssh` 登入主机后, 使用 `sudo -i` 命令切换到 `root` 权限, 然后运行下列命令, 按照提示输入应答, 即可完成安装.

> 对于新创建的主机, 这时候可能连 `wget` 工具都没有, 那就先把它装上: ubuntu, `apt install wget -y` ; CentOS, `yum install wget -y` .

```
wget --no-check-certificate https://raw.githubusercontent.com/ShadowsocksR-Live/shadowsocksr-native/master/install/ssrn-ot-install.sh
chmod +x ssrn-ot-install.sh
./ssrn-ot-install.sh 2>&1 | tee ssr-n-ot.log

```

> 安装失败的绝大部分原因参阅 [这里](./%E5%87%86%E5%A4%87%E5%B7%A5%E4%BD%9C#%E5%B0%86-%E5%9F%9F%E5%90%8D-%E5%92%8C-%E8%99%9A%E6%8B%9F%E4%B8%BB%E6%9C%BA-%E7%9A%84-ip-%E5%85%B3%E8%81%94%E4%B8%8A)

安装结束以后, 配置文件全文会显示在屏幕上, 该文件是 服务器/客户端 通用的. 复制/粘贴 下来保存成 json 文件就可以供 [本地 客户端](./客户端用法) 使用了.

当然. 这时候位于 `/fakesite/` 文件夹内的网站内容, 还是一个孤零零的示例网页文件, 下一步您得尽快填补一些看起来有趣的内容, 做法参看 [创建假网站](./创建假网站) 一节. 否则, 等 `GFW` 醒过闷儿来, 就可能封杀这个网站了.

祝玩得开心.

>  安装结束以后，各种配置和设定可以看 [成果汇总](./%E6%89%8B%E5%B7%A5%E5%AE%89%E8%A3%85-SSRoT-%E6%9C%8D%E5%8A%A1%E5%99%A8%E5%85%A8%E7%A8%8B%E8%AF%A6%E8%A7%A3#%E6%88%90%E6%9E%9C%E6%B1%87%E6%80%BB)

> 注意：对于某些开启了 IPv4 / IPv6 双栈的服务器，请求目标网站地址时可能会失败，这时你得 [关掉整个主机的 IPv6 支持](./%E7%A6%81%E7%94%A8-IPv6)。

### 更新现有 SSRoT 服务端方法

执行以下命令就可以自动更新。
```
sudo -i
rm -rf update-server.sh
wget https://raw.githubusercontent.com/ShadowsocksR-Live/shadowsocksr-native/master/install/update-server.sh
chmod +x update-server.sh
./update-server.sh

```
执行完毕以后，如果出现绿色的 `active (running)` 字样，表示更新成功，如下图。如果失败，请备份你的配置文件 `/etc/ssr-native/config.json` 到其它文件夹后全新安装。

![image](https://user-images.githubusercontent.com/30760636/96358628-f53b9f00-113b-11eb-8f95-e4ccdedc7f48.png)


## IPv6-only 主机上安装 SSRoT 服务端专用脚本，不要乱用

由于当前各服务商对 IPv6 的支持普遍较弱，DNS 查询经常卡死，所以登入主机后做的第一件事，让 DNS 查询支持 IPv6/IPv4 网络(参阅 https://nat64.xyz/ )，用下列命令在 /etc/resolv.conf 文件开头插入新的记录：

```
sed -i '1i\nameserver 2a01:4f8:c2c:123f::1' /etc/resolv.conf
```

然后才运行下列命令行。

```
wget https://raw.githubusercontent.com/ShadowsocksR-Live/shadowsocksr-native/master/install/ssrn-ot-install-ipv6.sh
chmod +x ssrn-ot-install-ipv6.sh
./ssrn-ot-install-ipv6.sh 2>&1 | tee ssr-n-ot.log
```
> 附注；如果您安裝過程中有報錯，可能更新一下系統就解決問題，在 ubuntu 或者 debian 中三條命令如下。
> ```
> apt update -y
> apt upgrade -y
> apt autoremove -y
> ```
提醒一下，IPv6 DNS 解析不稳定，时灵时不灵，所以你没事得登入 VPS 换换 DNS 服务器 IP 玩儿，否则 SSRoT 服务端可能会间歇性抽风。


## IBM cloud 云服务器上安装 SSRoT 服务端

创建 IBM cloud 云服务实例时，选 Java 或 Node.js 类型，内存大小随意。

安装过程中，仅 应用名称 是必须输入的，内存大小与创建实例时指定大小要匹配，其它的一路回车就可以了。

```
wget https://raw.githubusercontent.com/ShadowsocksR-Live/shadowsocksr-native/master/install/ibmcloud.sh
bash ibmcloud.sh
```

由于 IBM 对滥用其免费云服务的打击力度加大，对于白嫖族来说, 部署 SSRoT 在 IBM 免费服务器上其实意义不大，一般几个小时就销号；但如果付费购买一个，应该是个不错的选择。


## 在 Heroku 上部署 SSRoT 服务端

稀缺资源，请勿滥用，日常使用中观看视频时请采用 144P 分辨率，否则一旦 [Heroku](https://heroku.com) 发飙大家都没得用，You know？

https://github.com/ShadowsocksR-Live/ssr2heroku/tree/main

如果你不是重度翻墙用户，建议 使用 [Heroku](https://heroku.com) 的免费主机容器, 用两个账号注册，轮换着每个用半月，基本上日常工作学习的翻墙需求都解决了。

[Heroku](https://heroku.com) 是有十多年悠久历史的老牌云服务商,稳定可靠,不差钱的各位瓜众请付费购买他们的服务,很超值的.
