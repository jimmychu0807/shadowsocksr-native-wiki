使用 `ssh` 软件登录 `VPS`. `VPS` 的操作系统最好是 `ubuntu` 18.04+

提升到 `root` 权限.
```
sudo -i
```

# 安装 `nginx` 软件

安装 `web` 服务器 [nginx](https://nginx.org) 软件.
命令为
```
apt-get install nginx -y
```
完毕以后, 可以敲入 `nginx -v` 查看 `nginx` 的版本号, 也可以通过命令 `which nginx` 查看 `nginx` 到底安装在哪个地方. 

`nginx` 配置文件是 /etc/nginx/nginx.conf, 文件里有如下两行
```
include /etc/nginx/conf.d/*.conf;
include /etc/nginx/sites-enabled/*;
```
第一行表明 `/etc/nginx/conf.d/` 文件夹里所有 `*.conf` 都会被包含进配置里面, 目前这个文件夹是空的.

第二行表明 `/etc/nginx/sites-enabled/` 文件夹内所有文件都会包含进配置, 目前只有一个文件 `default`, 因此该文件全路径即为 `/etc/nginx/sites-enabled/default`, 该文件内指明了 `web` 站点首页文件是 `/var/www/html/index.html`.

为了避免这个文件对我们以后的操作造成困惑、干扰. 把这个文件删了再说
```
rm -rf /etc/nginx/sites-enabled/default
```



head -c 12 /dev/random | base64

