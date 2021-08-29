### Tutorial

1. First check if your DNS is a remote one or a local one `cat /etc/resolv.conf`. If it's a local one like `192.168.1.1`, it does not a matter, but if the DNS is remote for example `208.67.222.222`, you need to add a route for it(see step 7).

2. Find out your `Default Route` (Gateway), it's `192.168.28.2` in my ubuntu machine.

![image](https://user-images.githubusercontent.com/30760636/129740880-b428b6da-1afd-46d1-ab8d-a8b27ce329ee.png)

3. Run your SSRoT client to connect to your server, assuming that your remote server IP is `123.45.67.89`, and local listen port is `1080`.
```
./ssr-client -c <your_config_file_full_path>
```
> If you want to proxy SSH, you can replace the command with `ssh -N -C -D 1080 user@123.45.67.89`.

4. Add tun interface
```
sudo ip tuntap add dev tun0 mode tun user <your_account_name>
```

5. Setup the tun interface
```
sudo ifconfig tun0 10.0.0.1 netmask 255.255.255.0
```

6. run `tun2socks` of [badvpn](https://github.com/ambrop72/badvpn)
```
badvpn-tun2socks --tundev tun0 --netif-ipaddr 10.0.0.2 --netif-netmask 255.255.255.0 --socks-server-addr 127.0.0.1:1080 &
```
> It's very easy to build `tun2socks` from source code under Linux. Here are the steps
> ```
> rm -rf badvpn
> git clone https://github.com/ambrop72/badvpn.git
> mkdir badvpn/build && cd badvpn/build
> cmake -DBUILD_NOTHING_BY_DEFAULT=1 -DBUILD_TUN2SOCKS=1 .. && make
> sudo rm -rf /usr/local/bin/badvpn-tun2socks
> sudo cp tun2socks/badvpn-tun2socks /usr/local/bin/
> cd ../..
> rm -rf badvpn
> badvpn-tun2socks --help
> ```

7. **If your DNS is a remote one**, add a route to it with a lower metric than the tun one (lower than metric on step 9)
```
sudo route add 208.67.222.222 gw 192.168.28.2 metric 4
```

8. Add a route for your SSRoT server or your SSH server **(not 127.0.0.1)**
```
sudo route add 123.45.67.89 gw 192.168.28.2 metric 4
```

9. Add a default route to forward everything to the tun
```
sudo route add default gw 10.0.0.2 metric 6
```

Done.

[Here](https://gist.github.com/ssrlive/dfb3b0b51c65bb7034727c5bd5267053) is the full bash script.

### References

[Unix & Linux: tun2socks (badvpn)](https://www.youtube.com/watch?v=wBz2Ndwsbpw)

[Linux全局智能分流方案](https://steemit.com/circumvention/@ryyan/linux)

[漫谈各种黑科技式 DNS 技术在代理环境中的应用](https://tachyondevel.medium.com/%E6%BC%AB%E8%B0%88%E5%90%84%E7%A7%8D%E9%BB%91%E7%A7%91%E6%8A%80%E5%BC%8F-dns-%E6%8A%80%E6%9C%AF%E5%9C%A8%E4%BB%A3%E7%90%86%E7%8E%AF%E5%A2%83%E4%B8%AD%E7%9A%84%E5%BA%94%E7%94%A8-62c50e58cbd0)
