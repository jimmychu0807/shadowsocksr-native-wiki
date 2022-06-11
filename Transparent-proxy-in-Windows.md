To use it in windows, download [wintun](https://www.wintun.net/) and [tun2socks](https://github.com/xjasonlyu/tun2socks/releases/latest) in the same folder or the system PATH and start the program. like this
```
C:\a>dir
 Directory of C:\a

06/11/2022  20:30    <DIR>          .
06/11/2022  20:30    <DIR>          ..
06/11/2022  20:30         7,976,448 tun2socks-windows-amd64.exe
06/11/2022  15:31           427,552 wintun.dll
               2 File(s)      8,404,000 bytes
               2 Dir(s)  13,173,481,472 bytes free
```
Run `tun2socks` with `administrator` privilage.
```
start cmd /k tun2socks-windows-amd64.exe -device tun://tun00 -proxy socks5://127.0.0.1:1080
```
We need to assign an IP address to it.

```
netsh interface ip set address name="tun00" source=static address=10.10.10.2 mask=255.255.255.0 gateway=10.10.10.1
netsh interface ip set dns name="tun00" static 8.8.8.8
```
Then route default traffic to TUN interface and make proxy server ip as an exception. format likes `route add $ip $DefaultGateway metric 5`

```
route add 66.123.45.67 192.168.43.1 metric 5
route add 0.0.0.0 mask 0.0.0.0 10.10.10.1
```

![image](https://user-images.githubusercontent.com/30760636/173194502-27be6778-b831-4664-a60f-68a1fa44047e.png)
