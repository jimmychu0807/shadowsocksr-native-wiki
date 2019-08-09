使用 `ssh` 软件登录 `VPS`. `VPS` 的操作系统最好是 `ubuntu` 18.04+
```
ssh root@123.45.67.89 -p 22
```
上面的 `123.45.67.89` 是你 VPS 的 IP 地址, 如果你的 VPS 的 ssh 端口 不是 22, 请替换成你自己的. 然后 盲打 输入 你的 VPS 登录 密码 登入 VPS.

提升操作权限到 `root` 权限.
```
sudo -i
```
预先安装一些必要的工具
```
apt-get update -y
apt-get install make zlib1g zlib1g-dev build-essential autoconf libtool openssl libssl-dev -y
apt install python3 python python-minimal cmake git -y

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
mkdir -p /fakesite/.well-known/acme-challenge/
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
        listen 80;
        server_name localhost;
        index index.html index.htm index.nginx-debian.html;
        root  /fakesite;
    }
```
然后使用下列命令让 nginx 重新加载配置使其生效
```
nginx -s reload

```

# 获取 数字安全证书

[Let's Encrypt](https://letsencrypt.org/) 是免费、自动化、开放的证书签发服务, 它得到了 Mozilla、Cisco、Akamai、Electronic Frontier Foundation 和 Chrome 等众多公司和机构的支持，发展十分迅猛。

申请 Let's Encrypt 证书不但免费，还非常简单，虽然每次只有 90 天的有效期，但可以通过脚本定期更新，配好之后一劳永逸。

以下命令就是配置过程. 注意, 下列命令不能简单地复制粘贴, 请将这些命令复制到文本编辑器里, 将里边的两处 `mygoodsite.com` 字串替换成你自己的 `域名`, 然后才可以复制粘贴到 `ssh` 的命令行终端控制台里.
```
org_pwd=`pwd`

mkdir /fakesite_cert
cd /fakesite_cert
openssl genrsa 4096 > account.key
openssl genrsa 4096 > domain.key
openssl req -new -sha256 -key domain.key -subj "/" -reqexts SAN -config <(cat /etc/ssl/openssl.cnf <(printf "[SAN]\nsubjectAltName=DNS:mygoodsite.com,DNS:www.mygoodsite.com")) > domain.csr
wget https://raw.githubusercontent.com/diafygi/acme-tiny/master/acme_tiny.py
python acme_tiny.py --account-key ./account.key --csr ./domain.csr --acme-dir /fakesite/.well-known/acme-challenge/ > ./signed.crt
wget -O - https://letsencrypt.org/certs/lets-encrypt-x3-cross-signed.pem > intermediate.pem
cat signed.crt intermediate.pem > chained.pem
wget -O - https://letsencrypt.org/certs/isrgrootx1.pem > root.pem
cat intermediate.pem root.pem > full_chained.pem

cd ${org_pwd}

```
经过这样一通骚操作, 我们就已经在 `/fakesite_cert` 文件夹里创建好了数字证书.

参考资料 [Let's Encrypt，免费好用的 HTTPS 证书](https://imququ.com/post/letsencrypt-certificate.html)

# 将 数字安全证书 部署到 web 服务器

将 原 web 服务器 配置文件 删掉, 再用 `vi` 软件重新生成 配置.
```
rm -rf /etc/nginx/conf.d/ssr.conf
vi /etc/nginx/conf.d/ssr.conf

```
通过 `vi` 输入如下内容, 注意 替换里边的 `mygoodsite.com` 字串为您的 `域名`
```
    server {
        listen 443 ssl;
        ssl on;
        ssl_certificate       /fakesite_cert/chained.pem;
        ssl_certificate_key   /fakesite_cert/domain.key;
        ssl_protocols         TLSv1 TLSv1.1 TLSv1.2;
        ssl_ciphers           HIGH:!aNULL:!MD5;
        server_name           mygoodsite.com;
        index index.html index.htm index.nginx-debian.html;
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
        index index.html index.htm index.nginx-debian.html;
        root  /fakesite;

        location / {
            rewrite ^/(.*)$ https://mygoodsite.com/$1 permanent;
        }
    }

```
注意
- 其中的三处 `mygoodsite.com` 字串, 替换成你自己的 `域名`. 
- `5mhk8LPOzXvjlAut` 字串就是 `反向代理` 的入口, 必须替换成你自己随机生成的字串, 不能偷懒, 否则 `GFW` 的网络爬虫会爬取到本网页, 将 `5mhk8LPOzXvjlAut` 加入破解词库, `GFW` 分分钟将你拿下.
- 由于 `vi` 编辑器非常原始, 也不支持鼠标, 编辑文字极其不便, 建议在本地纯文本编辑器里弄好了以后, 一次性复制粘贴到 `vi` 编辑器里, 然后保存退出.

随机字串 的生成很简单, 如下命令足矣, 注意查看生成的字串, 如果含有斜杠 `/` 加号 `+` 或者等号 `=`, 就再次生成, 直到没有为止.
```
head -c 12 /dev/random | base64

```
最后使用下列命令使配置生效
```
nginx -s reload

```
