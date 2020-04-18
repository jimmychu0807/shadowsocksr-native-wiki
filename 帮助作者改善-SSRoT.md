有同学发帖说 SSRoT 本来工作得好好的，但某天突然就不管用了，排除站点被 GFW 封锁的情况，极大可能是 SSR 服务器软件崩了。

为了让大家尽快帮助作者找到崩溃点，迅速修正错误。就有了这篇小文。

我们可以使用 Linux 的 [`coreDump`](https://en.wikipedia.org/wiki/Core_dump) 组件将崩溃现场转储到一个指定文件里，然后用 `gdb` 打开这个文件查看结果。

- 我们用 `sudo su` 命令将当前账号切换到管理员权限。

```
sudo su
```

- 用 `vi` 编辑软件打开本机的全局配置文件 `/etc/rc.local`

```
vi /etc/rc.local
```

- 按下 `i` 键（是 `i`, `I`, 不是 `L`, 也不是 `1`）让 `vi` 切换到文本编辑模式，按动 `下箭头` 移动文本插入点到 `/etc/rc.local` 文件的末尾的 exit 指令之前，添加如下命令

```
echo "/core-%e-%p-%s-%t" > /proc/sys/kernel/core_pattern

```

像下图这个样子，然后按下 `ESC` 键退出编辑模式，并敲入 `:wq` 和 回车 这 **四个** 键（`冒号` `w` `q` `回车`）保存修改并退出 `vi` 编辑器。

![image](https://user-images.githubusercontent.com/30760636/79629247-93917c80-817a-11ea-8f4f-3c89d7035cf5.png)

- 敲入 `chmod +x /etc/rc.local` 命令为 `rc.local` 加入 `可执行` 属性。

```
chmod +x /etc/rc.local
```

- 用 `vi` 编辑 `/etc/security/limits.conf` 文件, 设置所有用户 core file size soft 和 hard limit 为 unlimited。打开文件的命令是 `vi /etc/security/limits.conf`，像上一步一样，在文件内输入如下文本，并保存退出，像下图这个样子。

```
*        soft        core          unlimited
*        hard        core          unlimited
```
![image](https://user-images.githubusercontent.com/30760636/79629617-a6f21700-817d-11ea-85f8-aeed0c6b4983.png)

> 如果希望对系统 `systemd service` 全局设置，可修改 `/etc/systemd/system.conf` 文件。加入设置 `DefaultLimitCORE=infinity`。当然这一步不是必须的。

- 敲入 `reboot` 命令重启主机，当前的准备工作就完成了。

```
reboot
```

- 当有崩溃事故发生时，再次登入主机，用 `ls / | grep ssr` 命令列出根目录 `/` 文件夹里文件名带 `ssr` 字样的文件，找到具体的 `崩溃转储` 文件名，然后用 `gdb` 命令查看崩溃现场，比如我这里就是像这样

```
gdb /usr/bin/ssr-server /core-dump-ssr-server-3140-11-1587191352
```

![image](https://user-images.githubusercontent.com/30760636/79630202-e458a380-8181-11ea-80b2-d40e7cb25a13.png)

具体到你的情况，其中的 转储 文件名得换成你机器上的实际存在的名字。

- 把你看到的信息复制下来，以提交 issue 的方式发给开发者，不胜感激。

- 经常检查你的根目录，看有没有 `转储文件`。如果有的话请将分析后的结果发送给开发者，大家共同完善这款软件。

> 参考资料: [coredump配置、产生、分析以及分析示例](https://www.cnblogs.com/arnoldlu/p/11160510.html)

> 参考资料: [systemd service 如何开启 core dump](https://zhuanlan.zhihu.com/p/41153588)
