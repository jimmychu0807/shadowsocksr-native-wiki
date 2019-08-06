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

创建我们的假站点文件夹 `/fakesite`, 并把样本主页文件复制到这里.
```
mkdir /fakesite
cp /var/www/html/*.* /fakesite
```

在 `/etc/nginx/conf.d/` 文件夹内创建 子 配置文件 `ssr.conf`, 并用 `vi` 软件进行编辑
```
touch /etc/nginx/conf.d/ssr.conf
vi /etc/nginx/conf.d/ssr.conf
```
通过 `vi` 输入如下内容
```
    server {
        listen 443 ssl;
        ssl on;
        ssl_certificate       /tls_files/file.crt;
        ssl_certificate_key   /tls_files/file.key;
        ssl_protocols         TLSv1 TLSv1.1 TLSv1.2;
        ssl_ciphers           HIGH:!aNULL:!MD5;
        server_name           mygoodsite.com;
        index index.html index.htm;
        root  /fakesite;
        error_page 400 = /400.html;

        location /5mhk8LPOzXvjlAut/ {
            proxy_redirect off;
            proxy_pass http://127.0.0.1:10000;
            proxy_http_version 1.1;
            proxy_set_header Upgrade \$http_upgrade;
            proxy_set_header Connection "upgrade";
            proxy_set_header Host \$http_host;
        }
    }

    server {
        listen 80;
        server_name mygoodsite.com;

        location / {
            rewrite ^/(.*)$ https://mygoodsite.com/$1 permanent;
        }
    }

```
注意
> 其中的三处 `mygoodsite.com` 字串, 替换成你自己的 `域名`. 
> `5mhk8LPOzXvjlAut` 字串就是 `反向代理` 的入口, 必须替换成你自己随机生成的字串, 不能偷懒, 否则 `GFW` 分分钟将你拿下.

随机字串 的生成很简单, 如下命令足矣, 注意查看生成的字串, 如果有斜杠 `/` 或者等号 `=`, 就替换成其它字符.
```
head -c 12 /dev/random | base64
```

