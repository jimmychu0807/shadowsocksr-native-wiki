有同学发帖说 SSRoT 本来工作得好好的，但某天突然就不管用了，排除被 GFW 封锁的情况，极大可能是 SSR 服务器软件崩了。

为了让大家尽快帮助作者找到崩溃点，迅速修正错误。就有了这篇小文。

我们可以使用 Linux 的 `coreDump` 组件将崩溃现场转储到一个指定文件里，然后用 `gdb` 打开这个文件查看结果。

- 我们用 `sudo su` 命令将当前账号切换到管理员权限。

- 用 `vi` 编辑软件打开本机的全局配置文件 `/etc/profile`

```
vi /etc/profile
```

- 在 `/etc/profile` 文件的末尾，添加如下命令

```
sysctl -p -q -e
ulimit -c unlimited
mkdir /var/crash
echo "/var/crash/core-%e-%p-%s-%t" > /proc/sys/kernel/core_pattern

```

