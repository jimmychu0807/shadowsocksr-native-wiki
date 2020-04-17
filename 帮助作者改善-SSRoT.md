有同学发帖说 SSRoT 本来工作得好好的，但某天突然就不管用了，排除站点被 GFW 封锁的情况，极大可能是 SSR 服务器软件崩了。

为了让大家尽快帮助作者找到崩溃点，迅速修正错误。就有了这篇小文。

我们可以使用 Linux 的 `coreDump` 组件将崩溃现场转储到一个指定文件里，然后用 `gdb` 打开这个文件查看结果。

- 我们用 `sudo su` 命令将当前账号切换到管理员权限。

- 用 `vi` 编辑软件打开本机的全局配置文件 `/etc/profile`

```
vi /etc/profile
```

- 按下 `i` 键（是 `i`, `I`, 不是 `L`, 也不是 `1`）让 `vi` 切换到文本编辑模式，按动 `下箭头` 移动文本插入点到 `/etc/profile` 文件的末尾，添加如下命令

```
sysctl -p -q -e
ulimit -c unlimited
mkdir /var/crash
echo "/var/crash/core-%e-%p-%s-%t" > /proc/sys/kernel/core_pattern

```

像下图这个样子，然后按下 `ESC` 键退出编辑模式，并敲入 `:wq` 和 回车 这 **四个** 键（`冒号` `w` `q` `回车`）保存修改并退出 `vi` 编辑器。

![image](https://user-images.githubusercontent.com/30760636/79530236-5ad1a480-80a1-11ea-84af-0c8c0567ff26.png) 

