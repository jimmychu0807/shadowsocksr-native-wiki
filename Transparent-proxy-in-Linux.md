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

6. run `tun2socks`
```
badvpn-tun2socks --tundev tun0 --netif-ipaddr 10.0.0.2 --netif-netmask 255.255.255.0 --socks-server-addr 127.0.0.1:1080 &
```

7. **If your DNS is a remote one**, add a route to it with a lower metric than the tun one (lower than metric on step 9)
```
sudo route add 208.67.222.222 gw 192.168.28.2 metric 4
```

8. Add a route for your SSRoT server **(not 127.0.0.1)**
```
sudo route add 123.45.67.89 gw 192.168.28.2 metric 4
```

9. Add a default route to forward everything to the tun
```
sudo route add default gw 10.0.0.2 metric 6
```

Done.
