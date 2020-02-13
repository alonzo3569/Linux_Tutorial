# Module **7.**  Networking, Services, and System Updates

## Network Components
* __Network interface :__  A network interface is the point of interconnection between a computer and a private or public network.
* __Network interface controller (NIC) :__ 
  * Aka network interface card, network adapter.
  * It is a computer hardware component that connects a computer to a computer network.
  * 每一個網路卡都有一個被稱為MAC位址的獨一無二的48位元序列號，它被寫在卡上的一塊ROM中。
  * 在網路上的每一個電腦都必須擁有一個獨一無二的MAC位址。沒有任何兩塊被生產出來的網路卡擁有同樣的位址。
* __Internet protocol address (IP address) :__  A computer needs and IP address to connect to another computer and the other computer must also have an IP to listen to our communication.

## Network Devices
* __Modem :__
  * __Modulating :__ outgoing signal : digital to analog.
  * __Demodulating :__ incoming signal : analog to digital.
  * If we only need one device to connect to the internet, modem will be enough to connect to the internet. (Don't need router)
  * A.k.a. 數據機
* __Router :__ 
  * Fowards data from one network to another based on their IP address.
  * Router is the __gateway__ of network.
  * Decides whether the data should be recieved or it should be passed to the next router.
  * Assign IP address to every device in local network.
  * A.k.a. 路由器
* __Local network devices :__ 
  * **Hub** and **Switch** are used to exchange data within **local area network**.
  * They're __NOT__ used to exchange data outside their local network. 
  * __Hub :__ When a data arrive at one port, it sends to every port.
  * __Switch :__ 
    * Unlike hub, it will store MAC address of every device.
    * Data will only be sent to the correct port. 
    * Nowadays, router are integrated with switch.
    * A.k.a. 網路交換器
* __Conclusions :__
  * __Hubs__ and __Switches__ are used to create network.
  * __Routers__ are used to connect networks.
* __Ref :__
  * [__Switch, Hub and Router__][0]
  * [__Modem vs Router__][1]
  
<div align=center>

<img src="https://github.com/alonzo3569/Linux/blob/master/Img/M7_Network_device_connection.PNG"/><br></br>

</div> 

[0]: https://www.youtube.com/watch?v=1z0ULvg_pW8
[1]: https://www.youtube.com/watch?v=Mad4kQ5835Y

## LAN, WAN, Subnet Mask, Gateway, DNS
* __LAN :__ Local Area Network
* __WAN :__ Wide Area Network
* __Gateway :__
  * Router is the gateway between LAN and WAN.
  * The gateway is the IP address of the router.
* __DNS :__ Domain Name System
  * Resolves domain name to IP address.
* __Subnet mask :__
  * Subnet Mask can tell us where can we find the device/IP (at LAN or through the gateway/router to WAN). 
  * Changing Subnet Mask will change the size(ip range) of local area network.
  * Subnet Mask consists of 32 bits (4 fields * 8 bits). ex. 255.255.255.0
  * __In Fig1. :__
    * "1" indicates that LAN range must match our local ip in this bit.
    * "0" means LAN range doesn't have to match our ip. It can be any number.
    * Red : Network ID (IP = Network ID + Host ID)
    * Green : Host ID
    * This subnet mask can also be written as `192.168.1.0/24`
    * `/24` indicates that there are 24 "1" in this subnet mask.
  * __Subnet rules :__ A "good" subnet mask can only change from 1 to 0.(by convention) See Fig2.
  * __Note :__
    * We'll set subnet network bits to all "1" and setting Host bits to all "0".
    * For Class C, the IP of the first three field (24 bits) will be Network ID and the last field (8 bits) will be Host ID.
    * Thus, in class C, we'll have (256-2) IPs for our local device, since we have 8 bits for host ID.
    * We can increase the LAN range by changing 1 to 0 in the 3rd field of our subnet mask. This will increase the host ID range.
    * Subnet mask 0.0.0.0 means every IP is local IP. You'll never reach the gateway.
  
<div align=center>

<img src="https://github.com/alonzo3569/Linux/blob/master/Img/M7_Subnet_mask_1_0.PNG"/><br></br>
<b>Fig1. Subnet Mask example</b><br></br><br></br>
<img src="https://github.com/alonzo3569/Linux/blob/master/Img/M7_Subnet_mask_flags.PNG"/><br></br>
<b>Fig2. M7 Subnet mask flags</b><br></br><br></br>

</div>

## Reserved LAN Ranges and Reserved IP Addresses
* If my LAN range is 192.168.0.x. Could I reach WAN website at 192.168.0.251
* __Reserved local IP ranges :__ The IP below are __NOT__ used in __WAN__.
  1. 192.168.x.x
  2. 10.x.x.x
  3. 172.16-31.x.x
  4. 169.254.x.x
* __Reserved IP addresses :__ These IP tend to have meaning to network infrastructer. Should avoid using it.
  1. Network identifier x.x.x.0
  2. Broadcast address x.x.x.255
  

## Service in Routers
* __NAT :__ Network Address Translation.
  * __NAT__ tranlsate __private (local)__ IP address to __public__ IP address. Or __public__ IP to __private__ IP.
  * There are 4,294,967,296 public IPv4 address available. However, it's still not enough.
  * Thus, engineers develope __LAN__ (local/private IP address) and __NAT__.
  * Two types of __IPv4__ address :
    * __Public :__ 66.94.234.13
      * Must have public IP to access internet. 
      * Costly.
    * __Private :__ 10.0.0.1,    192.168.1.225
      * Only used internally.
  * __IPv6 :__ 2001:4860:0:0:0:0:0:8844
    * 8 fields * 16 bits = 128 bits total.
    * Every device will have its own public IP address. 
    * We don't need __NAT__ in the future.
* __DHCP :__ Dynamic Hose Configuration Protocol
  * __DHCP__ will automatically assign __Dynamic IP, Subnet mask, Default Gateway, DNS server IP__.
  * User can decide the scope of Dynamic IP addresses. ex. start: 10.0.0.1 ~ 10.0.0.3
  * __Lease :__
    * Lease is a specific amount of time that DNS server will check if every device is still online.
    * If the device gets offline, the server will retrive its IP.
    * Each device will ask DNS server to renew their lease if they are still online.
    * Lease is used to make sure that we make full use of every IP and prevent us from running out of IP.
  * Address Reservation : 
    * A reservation ensures that the same device will always be given the some IP address.
    * Address Reservation will identify device by its __MAC__ address.
    * Reservations usually given to special device such as printer, server or router.


## Linux 常用網路指令
* __ifconfig :__ 可以即時的手動的啟動、觀察與修改網路介面的相關參數
  * __Syntax :__ `ifconfig [interface] [options]`
  * __Interface :__ enp0s3, eth0, eth1, lo 等等。
  * __Option :__ 
    1. up, down ：啟動 (up) 或關閉 (down) 該網路介面
    2. mtu      ：設定不同的 MTU 數值, 單位為 byte
    3. netmask  ：就是子遮罩網路
  * __查詢所有的網路介面 :__
    ```console
    [root@www ~]# ifconfig
    eth0      Link encap:Ethernet  HWaddr 08:00:27:71:85:BD
              inet addr:192.168.1.100  Bcast:192.168.1.255  Mask:255.255.255.0
              inet6 addr: fe80::a00:27ff:fe71:85bd/64 Scope:Link
              UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
              RX packets:2555 errors:0 dropped:0 overruns:0 frame:0
              TX packets:70 errors:0 dropped:0 overruns:0 carrier:0
              collisions:0 txqueuelen:1000
              RX bytes:239892 (234.2 KiB)  TX bytes:11153 (10.8 KiB)

     * eth0：就是網路卡的代號
     * HWaddr：MAC
     * inet addr：IPv4 的 IP 位址，後續的 Bcast 是 Broadcast, Mask 是 netmask
     * inet6 addr：是 IPv6 的版本的 IP
     * MTU：最大傳輸單位 MTU 數值(Maximum Transmission Unit), 單位為 byte
     * RX：網路由啟動到目前為止的封包"接收"情況
           packets 代表封包數
           errors 代表封包發生錯誤的數量
           dropped 代表封包由於有問題而遭丟棄的數量等等
     * TX：網路由啟動到目前為止的"傳送"情況
     * collisions：代表封包碰撞的情況，如果發生太多次， 表示你的網路狀況不太好
     * txqueuelen：代表用來傳輸資料的緩衝區的儲存長度
     * RX bytes, TX bytes：總接收、傳送的位元組總量

    ```

  * __修改網路介面設定 :__
    ```console
    1. 設定 eth0 IP, 若不指定IP, 系統會自動算出
    [root@www ~]# ifconfig eth0 192.168.100.100
    
    2. 設定 eth0 介面其他參數
    [root@www ~]# ifconfig eth0 192.168.100.100 netmask 255.255.255.128 mtu 8000 
    
    3. 關閉 eth0 介面
    [root@www ~]# ifconfig eth0:0 down  # 反之 up 啟用
    
    4. 將使用 ifconfig 所做的改動都取消
    [root@www ~]# /etc/init.d/network restart
    ```
* __ifup / ifdown :__ 
  * 若想透過設定檔 `/etc/sysconfig/network-scripts/ifcfg-eth0` 啟動某個介面網路, 可透過 ifdown 或 ifup
  * 使用 ifdown 關閉前，會檢驗IP參數這並是否與 ifcfg-eth0 相同，不同則無法關閉
  * 因此使用 ifconfig 修改完畢後，應該要以 ifconfig eth0 down 才能夠關閉
    
* __route :__ 
  * __查詢路由表 :__ 顯示出所有網域
  
  ```console
  [root@www ~]# route -n    # -n ： 將主機名稱以 IP 的方式顯示(較快)
  
  Kernel IP routing table
  Destination       Gateway         Genmask         Flags Metric Ref  Use Iface
  192.168.0.0       */0.0.0.0       255.255.255.0   U     0      0      0 eth0
  127.0.0.0         */0.0.0.0       255.0.0.0       U     0      0      0 lo
  default/0.0.0.0   192.168.0.254   0.0.0.0         UG    0      0      0 eth0

  # Destination ：想將封包傳遞到的目標網域
  # Gateway     ：就是該介面的 Gateway 那個 IP 啦！若為 0.0.0.0 表示不需要額外的 IP
  # Genmask     ：Subnet mask
  # Flags       ：共有多個旗標可以來表示該網域或主機代表的意義：
  #               U：代表該路由可用；
  #               G：代表該網域需要經由 Gateway 來幫忙轉遞；
  #               H：代表該行路由為一部主機，而非一整個網域；
  # Iface       ：這個路由傳遞封包的介面
  
  * 傳遞封包時，系統會查詢路由表來決定該傳送至哪一個網域
  * 系統由上至下尋找合適的網域，因此會先從 LAN 檢查，若目標網域不再 LAN 內，
    才會通過最後一行的 Gateway:192.168.0.254 傳送到 WAN
  * 內網傳送不須經過 Gateway(利用 switch + MAC 即可)，因此 Gateway 表示為 */0.0.0.0
  * default/0.0.0.0 代表不屬於上排 LAN 的其他 IP，可解釋為 WAN
  * 簡言之，若目標 IP 為 192.168 開頭者，則以 eth0 介面傳送封包
    若為 127 開頭者，則以 lo 介面傳送封包
    若皆不為 192.168 與 127 開頭者，則透過 Gateway 192.168.0.254 傳送封包到 WAN 
  * 路由表從小網域到大網域排列，192.168(Class C) -> 127(Class B)    
  ```
  
  * __路由的增加與刪除 :__
  ```console
  1. 新增 route，一定要提供 LAN identifier, Netmask, Device(Interface)
  [root@www ~]# route add -net 192.168.100.0 netmask 255.255.255.0 dev eth0
  
  2. 刪除 route
  [root@www ~]# route del -net 169.254.0.0 netmask 255.255.0.0 dev eth0
  
  3. 新增 Gateway (Gateway IP 必須在 LAN 內，否則錯誤 SIOCADDRT: Network is unreachable)
  [root@www ~]# route add default gw 192.168.1.250
  
  4. 刪除 Gateway
  [root@www ~]# route el default gw 192.168.1.250
  
  5. 還原(取消變更) (x?)
  [root@www ~]# /etc/init.d/network restart
  
  * 192.168.x.x 皆為可使用的 Network identifier
  * 一個 route 所建立的子網路可以有多個 Gateway ex. 192.168.100.0/24 gw: 192.168.100.1, 192.168.100.2
  * 192.168.100.0 route 刪除後 gw 192.168.100.1 不會消失(? gw 已死)
  * 
  ```
* __iwconfig :__ 設定無線網卡的相關參數
* __iwlist :__ 利用無線網卡進行無線 AP 的偵測
* __dhclient :__ 
  * 不用編輯 ifcfg-eth0 內的 BOOTPROTO 的方法 
  * __Syntax :__ `dhclient [interface]`
  * __Ex.__ `[root@www ~]# dhclient eth0`
* __ping :__ 偵測一下此 IP 是否存在
  ```console
  選項與參數：
  -c 數值：後面接的是執行 ping 的次數，例如 -c 5 ；
  -W 數值：等待回應對方主機的秒數。

  # 範例一：偵測一下 168.95.1.1 這部 DNS 主機是否存在？
  [root@www ~]# ping -c 3 168.95.1.1
  PING 168.95.1.1 (168.95.1.1) 56(84) bytes of data.
  64 bytes from 168.95.1.1: icmp_seq=1 ttl=245 time=15.4 ms
  64 bytes from 168.95.1.1: icmp_seq=2 ttl=245 time=10.0 ms
  64 bytes from 168.95.1.1: icmp_seq=3 ttl=245 time=10.2 ms

  --- 168.95.1.1 ping statistics ---
  3 packets transmitted, 3 received, 0% packet loss, time 2047ms
  rtt min/avg/max/mdev = 10.056/11.910/15.453/2.506 ms
  ```
* __traceroute :__ 顯示到目標 IP 的所有節點(IP)
  [root@www ~]# traceroute [選項與參數] IP
  # 範例一：偵測本機到 yahoo 去的各節點連線狀態
[root@www ~]# traceroute -n tw.yahoo.com
traceroute to tw.yahoo.com (119.160.246.241), 30 hops max, 40 byte packets
 1  192.168.1.254  0.279 ms  0.156 ms  0.169 ms
 2  172.20.168.254  0.430 ms  0.513 ms  0.409 ms
 3  10.40.1.1  0.996 ms  0.890 ms  1.042 ms
 4  203.72.191.85  0.942 ms  0.969 ms  0.951 ms
 5  211.20.206.58  1.360 ms  1.379 ms  1.355 ms
 6  203.75.72.90  1.123 ms  0.988 ms  1.086 ms
 7  220.128.24.22  11.238 ms  11.179 ms  11.128 ms
 8  220.128.1.82  12.456 ms  12.327 ms  12.221 ms
 9  220.128.3.149  8.062 ms  8.058 ms  7.990 ms
10  * * *
11  119.160.240.1  10.688 ms  10.590 ms 119.160.240.3  10.047 ms
12  * * * <==可能有防火牆裝置等情況發生所致


* __netstat :__ 可查詢 IP:port 的狀態，以及啟動該 port 的程式
  ```console
  1. 觀察本機上頭所有的網路連線狀態 (LISTEN + ESTABLISH)
  [root@www ~]# netstat -atunp
  Active Internet connections (servers and established)
  Proto Recv-Q Send-Q Local Address     Foreign Address     State       PID/Program
  tcp        0      0 0.0.0.0:111       0.0.0.0:*           LISTEN      969/rpcbind
  tcp        0      0 0.0.0.0:22        0.0.0.0:*           LISTEN      1155/sshd
  tcp        0      0 127.0.0.1:25      0.0.0.0:*           LISTEN      1231/master
  tcp        0     52 192.168.1.100:22  192.168.1.101:1937  ESTABLISHED 4716/0

  ....(底下省略)....
  # State : LISTEN => 等待連線的 port
          : ESTABLISH => 已建立連線
  # netstat -tulnp : 僅列出有在 Listen(尚未連線) 的 port (無 ESTABLISH)
  * 查以連接用'netstat -atunp' 未連接用 'netstat -tlunp'
  * 由上圖可知，僅建立一條網路連線
  * 由遠端主機 192.168.1.101 啟動一個大於 1024 的埠口向本地端主機 192.168.1.100 的 port 22 
  * kill -9 4716 取消連線
  * /etc/services 有服務名稱與 port number 的對應
  ```
* __host :__ 
  * 查出某個主機名稱的 IP，類似 DNS 的效果
  * __Syntax :__ `host google.com`
  * 預設會向 local 的 DNS server (server IP 寫在 /etc/resolv.conf 內) 詢問 IP
  * 不想用本地端 DNS 的話，可以指定 WAN 的 DNS
    * Google Public DNS 8.8.8.8 
    * 中華電信 Hinet 168.95.1.1
    ```console
    [alonzo@www ~]$ host facebook.com 8.8.8.8
    ```
* __nslookup/dig :__ 
  * 與 host 雷同，但可由 IP 查主機名稱 (dig 有更多資訊)
    ```console
    [alonzo@www ~]$ nslookup/dig facebook.com
    [alonzo@www ~]$ nslookup/dig 8.8.8.8
    ```
* __telnet :__ 
  * 連結到伺服器以及連結到 PTT BBS 站 (ptt.cc) 所用的指令，還可以連結到某個 port (服務) 
    ```console 
    [root@www ~]# telnet [host|IP [port]]

    # 範例一：連結到 PTT BBS 站
    [root@www ~]# yum install telnet  <==預設沒有安裝這軟體
    [root@www ~]# telnet ptt.cc

    1. 查詢 LISTEN 中的 port
    [alonzo@www ~]$ netstat -atunp

    2. 連上 port
    [alonzo@www ~]$ telnet localhost 631
    Trying ::1...
    Connected to localhost.    # netstat -atunp will show port 631 "Establish"
    Escape character is '^['
    Connection close by foreign host.
    ```
* __ftp :__ 讓使用者可以到 FTP 伺服器上下載資料
```console
1. 安裝 ftp
[root@www ~]# yum install ftp

2. 登入崑山科大 anonymous 登入
[alonzo@www ~]$  ftp ftp.ksu.edu.tw # ftp.ntu.edu.tw 可用 anonymous 登入 無下載權限
ftp> help  # 查指令
ftp> get filename   <==下載單一檔案，檔名為 filename 
ftp> mget filename* <==下載多個檔案，可使用萬用字元 *
ftp> put filename   <==上傳 filename 這個檔案到伺服器上
ftp> delete file    <==刪除主機上的 file 這個檔案
ftp> bye            <==結束 ftp 軟體的使用

3. 若對方主機的 ftp 服務開啟在 318 port
[alonzo@www ~]$ ftp 對方FTP伺服器IP 318
* ftp 通常使用 port21 用 'netstat -atunp' 查詢
```

* __lftp :__ ftp script
```console
1. ftp script
[alonzo@www ~]$ vim lftp.ksu.sh   # ftp 的 script 非 sh3ll script
> open ftp.ksu.edu.tw             # 因此不用 #!/bin/bash
> cd /pub/CentOS/                 # 直接使用 FTP 伺服的指令
> mget -c -d RPM-GPG*
> bye
[root@www lftp]# lftp -f lftp.ksu.sh  # 執行

2. 操作 FTP 的 "shell script"
[alonzo@www ~]$ vim lftp.ksu.sh
> lftp -c "open ftp.ksu.edu.tw   # 加入 lftp -c "" 而已
> cd /pub/CentOS/
> mget -c -d RPM-GPG*
> bye"
[alonzo@www ~]$ sh lftp.ksu.sh   # 無標頭 #!/bin/bash
```

* __wget :__ wget 支援 HTTP 和 FTP 協定，可下載這兩種伺服器上的檔案
```console
[alonzo@www ~]$ wget http://www.kernel.org/pub/linux/kernel/v2.6/linux-2.6.39.tar.bz2
```

* __tcpdump :__ 監聽封包的流向與內容
```console
1. 以 IP 與 port number 捉下 eth0 這個網路卡上的封包， ctrl+c 退出
[root@www ~]# tcpdump -i eth0 -nn
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), capture size 65535 bytes
17:01:47.360523 IP 192.168.1.101.1937 > 192.168.1.100.22: Flags [.], ack 196, win 65219, 
17:01:47.362139 IP 192.168.1.100.22 > 192.168.1.101.1937: Flags [P.], seq 196:472, ack 1,

* -nn：直接以 IP 及 port number 顯示，而非主機名與服務名稱
* -i ：後面接要監聽的網路介面，例如 eth0, lo, ppp0
* -X ：列出十六進位 (hex) 以及 ASCII 的封包內容

# 17:01:47.362139：這個是此封包被擷取的時間，『時:分:秒』
# (>) 的符號指的是封包的傳輸方向
# seq 196:472 傳輸的資料為整體資料的 196~472 byte
# 2 IP 相互溝通(一去一回) 三向交握

2. 監聽網路介面 enp0s3 port 21 的 tcp 傳輸
[root@www ~]# tcpdump -i eth0 -nn port 21
[alonzo@www ~]$ cd /lftp/              # 開另一 terminal
[alonzo@www ~]$ ./lftp_shell_script.sh # 執行 ftp 下載 (ftp 也是用 tcp)
                                     # 回 tcpdump terminal 看結果
3. 監聽封包內容
[root@www ~]# tcpdump -i enp0s3 -nn -X 'port 21'
[alonzo@www ~]$ ftp ftp.ksu.edu.tw   # 開另一 terminal
                                     # 回 tcpdump terminal 看結果
                                     # 沒看到 anonymous(?) encrypted(?)
    0x0000:  4500 0048 2a28 4000 4006 1286 7f00 0001  E..H*(@.@.......
    0x0010:  7f00 0001 0015 80ab 8355 2149 835c d825  .........U!I.\.%
    0x0020:  8018 2000 fe3c 0000 0101 080a 0e2e 0b67  .....<.........g
    0x0030:  0e2e 0b61 3232 3020 2876 7346 5450 6420  ...a220.(Login.
    0x0040:  322e 302e 3129 0d0a                      2.0.1)..

```

* __nc :__ 與 telnet 相似，但 nc 可以輸入訊息給 port 
  * __telnet__ 與 __nc__ 皆可與 listening port 連線
  * 但 __nc__ 可以啟動一個新的 listening port
  * __telnet__ 要透過 `netstat -tlunp` 查詢可連接 port
```console
1. 安裝 nc
[root@www ~]# yum install nc

2. 連上 port 25 
[root@www ~]# nc localhost 25

3. 啟動一個 port 20000 來監聽使用者的連線要求 (2000以上為使用者自訂port?才可打字傳輸?)
[root@www ~]# nc -l localhost 20000 &   # 若顯示 stopped(背景執行) 則 `fg` 啟用
                                        # `netstat -tlunp` 查看 PID 
                                        # 可用 kill PID 刪除背景執行
[root@www ~]# netstat -tlunp | grep nc  # 開新 terminal 查看 20000 是否 LISTEN
[root@www ~]# nc localhost 20000        # 連接 port 20000 即可打字溝通
[root@www ~]# netstat -atunp | grep nc  # 再開一 terminal 觀察連接情況
                                        # 應有 2 nc 和一 telnet 服務
                            
4. 用 nc 啟用 port 再用 telnet 連接打字溝通
[root@www ~]# nc -l localhost 20000 &
[root@www ~]# telnet localhost 20000
[root@www ~]# netstat -atunp | grep nc      # 再開一 terminal 觀察連接情況
[root@www ~]# netstat -atunp | grep telnet  # 應有一 nc 和一 telnet 服務
```
## Network Files and Commands (ping, ifconfig, ifup, ifdown, netstat, tcpdump)
Interface Detection
* Assigning an IP address
* Interface configuration files
  * /etc/nsswitch.conf
  * /etc/hosts
  * /etc/sysconfig/network (CentOS version6) /etc/hostname (CentOS version7)
  * /etc/sysconfig/network-scripts/ifcfg-nic(enp03s?)
  * /etc/resolv.conf
* Network Commands
  * ping
  * ifconfig
  * ifup or ifdown
  * netstat
  * tcpdump
  
```console
cat /etc/nsswitch.conf
more /etc/hosts  => where you define your DNS
sudo vim /etc/hosts
> 192.168.1.7  mydevice
:wq
ping mydevice

more /etc/sysconfig/network

cd /etc/sysconfig/network-scripts/
ls -ltr
vi ifcfg-nic
> BOOTPROTO=dhcp  => Can change dhcp to static ip
:wq

more /etc/resolv.conf => define DNS server

ping www.hotmail.com
# ifdown/ifup ro turn on/off the Network interface
netstat -rnv => internet information
tcpdump -i enp0s3
```
## NIC Information
NIC = network interface card
NIC can have multiple ports
Most of the time port is incorrectly referred as NIC in IT

```console
ethtool enp0s3 # or eth0, eth1...

ifconfig
ethtool virbr0
ethtool lo
ethtool enp0s3
# link modes # 1000base => support 1G of traffic
# Duplex #?
# Link detected # yes or no
```
lo : Loopabck device. Use to communicate with itself.
vrb0 : Virtual is used for NAT(Network Address). Sometimes connect to outside network.


## NIC Bonding

NIC(Network Interface Card) bonding is
also known as Network bonding. It can
be defined as the aggregation or
combination of multiple NIC into a
single bond interface.

First, setup Oracle. 
Setting -> network -> Adapter2 (Your device has to have at least 2 RJ45 port) -> Bridge Apdapter -> start Linux

```console
ifconfig    # will see enp0s3 and enp0s8 => new
su -
modinfo bonding | more
# If u see Ethernet Channel Bonding Driver v3.7.1 => means bonding driver already install
vi /etc/systemconfig/network-script/ifcfg-bond0
> DEVICE=bond0
> .
> .
> .
> BOOTPROTO=none # or static
> ONBOOT=yes     # yes means enable when system boot
> IPADDR=192.168.1.80 # Find an IP that is available. Use ping to check
> NETMASK=255.255.255.0
> GATEWAY=192.168.1.1 ip address of your modem?
:wq

cd /etv/sysconfig/
cd network-scripts/
vi ifcfg-enp0s3
> delete every line
> TYPE=Ethernet....
> HWADDR=... # ifconfig to checkout (numbers after "ether")
:wq
cp ifcfg-enp0s3 ifcfg-enp0s8
vi ifcfg-enp0s8
> # Change MAC address and DEVICE=ifcfg-enp0s8
systemctl restart network # restart network service
ifconfig | more # 新增"bond0", enp0s3 enps08 become "SLAVE"
cat /proc/net/bonding/bond0  # Just take a look

```
Poweroff
Go back to Oracle 
settings -> disable Apdapter2
Snapshot -> restore -> don't need currently state -> 系統回復到原本狀態


## Downloading Files or Apps (wget)
Why don't use yum? (apt-get)
When the things we want to download isn't in yum repo(yum can't fild their package, 3rd party website), we use wget to download
```console
su -
yum install putty
=> No package available

# google "download putty, find linux version link and copy link location"
wget "right click to paste"
ls => will see putty.tar.gz

```

## curl and ping commands
```console
curl www.google.com
curl -o http-link  => will download file 
```

## FTP
* FTP port 21 / ssh,scp 22 / dns 53 
* Setup FTP Server!  alonzo server hanzo client
```console
[alonzo@server ~]$
1. download
su -
rpm -qa | grep vsftpd # check if the FTP server package is installed
yum install vsftpg => server package

2. configure
cd /etc/vsftpd
cp vsftpd.conf vsftpd.conf .orig => backup
vim vsftpd/conf
> /anonymous_enable=NO
> uncomment ascii_upload_enable=YES & ascii_download_enable=YES
> uncomment ftpd_banner
EOF add
> # Local Time
> use_localtime=YES
> :wq

3. start server
systemctl start vsftpd
systemctl status vsftpd
systemctl enable vsftpd => starts every time system reboot
systemctl stop firewalld => not recommend , stop only
systemctl disable firewalld => won't start every time system reboot
exit root


[hanzo@client ~]$
su - 
yum install ftp => client package
su - hanzo
touch 1.txt
ls -ltr > 1.txt
ftp 192.168.1.58 # ip address of the server
enter
enter
bin => to binary mode
hash => show me download progress using #
put 1.txt upload file
bye

[alonzo@server ~]$
ls => will see 1.txt
```

## SCP secure copy protocol
* Similiar to FTP, but it adds security and authentication
* Port 22 (Same as SSH) (need sshd runnning)
```console
touch jack
echo Hello world > jack
ifconfig enp0s3 => at remote's terminal(find remote ip)
scp jack hanzo@remoteIP:/ho,e/hanzo
hanzo's passwd
ls => check at remote's terminal
```

## Remote Sychronization
* rsync is a lot faster than ftp or scp
* Mostly used to backup files and dirs from one server to another(run in crontab)
* rsync Prot 22 (need sshd runnning)
```console
yum install rsync (CentOS/Redhat)
apt-get install rsync (Ubuntu/Debian)

# sync a file on local machine
tar cvf backup.tar .(= /home/alonzo)
mkdir /tmp/backups
rsync -zvh backup.tar /tmp/backups/

# sync a dir on local machine
rsync -azvh /home/alonzo /tmp/backups

# sync a file to remote machine
mkdir /tmp/backups => on remote
rsync -azv backup.tar hanzo@remoteIP:/tmp/backups
ls => check on remote

# sync a file from remote machine
rsync -azv hanzo@remoteIP:remotefilepath localfilepath
```

## System Update and Repos
* yum (CentOS link source /etc/yum.repos.d) apt-get (Ubuntu) 
* rpm (Redhat Package Manager)
* yum : download + install
* rpm : install
```console
yum install bind
yum install httpd
rpm -qa => query all
rpm -qa | wc -l

rpm -hiv /tmp/packagename.rpm => install pkg

rpm -qa | grep bind
rpm -e pkg_full_name => remove package 
rpm -qa | grep bind => check

yum remove bind
```

## System Upgrade/Patch Management
* 2 types of upgrades
  * Major version = 5,6,7  => No command
  * Minor version = 7.3 to 7.4 => yum update -y (say yes to every question)
* yum update (preserve old pkg) v.s. yum upgrade (delete old pkg)
```console
cat /etc/redhat-release => check version
yum update
```

## Create Local repo from DVD
take all the pkg from DVD
when you can't access internet
and you want to install pkg
this is when createrepo come to rescue
```console
# create snapshot in Oracle
su -
c
# Oracle => Optical Drives(VM terminal top bar) => CentOS DVD => open
# DVD is not monted on pc
df -h => /dev/sr0 Mounted on /run/media/alonzo/CentOS 7 x86_64
cd /
mkdir localrepo
cd /run/media/alonzo/CentOS 7 x86_64  => No spac in Linux
cd /run/media/alonzo => press tab, will see /run/media/alonzo/CentOS\ 7\ x86_64/
cd /run/media/alonzo/CentOS\ 7\ x86_64/
cd Packages
ls -ltr | wc -l => check how many pkgs
du -sh . => check the size of /run/media/alonzo/CentOS\ 7\ x86_64/
df -h => /dev/mapper/centos-root is our device's space, check if it's big enough
cp -rv /run/media/alonzo/CentOS\ 7\ x86_64/Packages/* ~/localrepo
cd /etc/yum.repos.d/ # remove all source link for yum
ls -ltr
rm -rf /etc/yum.repos.d/* => make sure you hav snapshot
vi local.repo
> [centos7]
> name=centos7
baseurl=file///localrepo/enable=1
gpgcheck=0
:wq!
createrepo /localrepo/
yum clean all => clean all repo in cache
yum repolist all
yum install tomcat => test
```

## Advance Package Managment
```console
# method 1
rpm -qa | grep ksh => chek if ksh is already installed
yum install  ksh* => anything related to ksh
yum remove ksh*

# method 2
google: download ksh for centOS 7
find rpm file copy link
wget pkg_link 
rpm -hiv pkg_name => install pkg
rpm -e pkg_name => run 'rpm -qa | grep ksh' for pkg name
rpm -qc pkg_name => list all the pkg configuration file
which ksh  => find out path of the command
rp -qf pathofcommand(/usr/bin/ksh) => show this command belongs to which pkg
```

## Rollback Update and Patches
* yum update can rollback
* yum grade can't roolback (since it delete old pkgs)
* Rollbak a pkg
  * yum history undo [id] (id of the yum install pkg)
* Rollback an "update" => 7.1 to 7.0 => lots of pkgs are involved
  * yum history undo [id]
```console
take a snapshot first
yum install screen
rpm -qa | grep screen
yum history  => find install id
yum history undo 17
rpm -qa | grep screen

yum update
yum history => I,U install and Update. Take snapshot
yum history
yum history undo 19
```

## SSH and Telnet
* Telnet : Unsecured connection between pcs (Only use for trobleshooting now)
* SSH : Secure
```console
ps -ef | grep sshd => sshd process name '/usr/sbin/sshd -D'
# sshd is the service for listening (server) 
# d for deamon
# type 'systemctl stop sshd' => to stop service /usr/sbin/sshd -D => can't ssh anymore
# systemctl start sshd => restart
```
## DNS Domain Name System
* Purpose :
  1. A Record : Hostname to IP
  2. PRT Record : IP to Hostname 
  3. CNAME Record : Hostname to Hostname 
* Service(Daemon) for DNS server : `system restart named`
```console
```

## NTP Network time protocol
* NTP port 123
```console
yum install ntpd -y

rpm -qa | grep ntp

vi /etc/ntp.conf
> server 8.8.8.8 => google DNS server, ususally used as ntp server

systemctl start ntpd
systemctl enable ntpd => start at boot
systemctl status ntpd => Chk if it is running or run ps -ef |grep ntp

ntpq => see google.public
quit
systemctl stop ntpd
```

## chronyd
* Same as ntp, more advance. Time synchronization. 
```console
su -
yum install chrony
rpm -qa | grep chrony
vi /etc/chrony.conf
> server 8.8.8.8 => google DNS server
> :wq

systemctl status chronyd => Don't run ntp and chrony at th same time
systemctl disable ntpd => disable ntp if active
systemctl enable chronyd
chronyc
> help
> sources => show server for time synchronizing
> quit

vi /etc/chrony.conf
> remove server 8.8.8.8
systemctl restart chronyd
```

## Sendmail
```console
rpm -qa | grep sendmail
yum install sendmail
yum install sendmail-cf

cd /etc/mail/
ls -ltr => find sendmail.md => mail relayer : a server that send or recieve you eamil
vim sendmail.md
> 

systemctl restart sendmail
systemctl status sendmail

mail -s "mailsetup" alonzo@hotmail.com => send email
hello world!
Ctrl+d to quit 
```

## Web server (httpd)
```console
su -
rpm -qa | grep httpd
yum install httpd

cd /stc/httpd/conf/
vi httpd.conf
> Listen 80 => listen on port 80
> DocumentRoot "/var/www/html"

cd /var/www/html/
ls -ltr => find index.html
# If u cannot find index.html
# Create one
vi index.html => write html

systemctl stop firewalld (?)
systemctl status firewalld (?)
systemctl start httpd
systemctl enable httpd
systemctl status httpd

ifconfig => find ip
google your ip => u can see your website
```

## Central Logger (rsyslog)
* Assume that we have a hundreds of devices in the network, they all have its own system logfile
* It will be much easier to manage if we send all the logs to one drvice(server).
* Send every device's(client) log to a server. 
```console
yum install rsyslog
rpm -qa | rsyslog.conf

vi /etc/rsyslog.conf
> remote-host:514 => central logger IP
systemctl status rsyslog
systemctl start rsyslog
```

## Linux OS Hardening
```console
chage –l username => check passwd status

# Remove un-wanted packages
rpm -qa | wc -l => How many pkg do I have
rpm -e => remove pkg. look out for dependencies
systemctl -a

# Check on listening port
netstat -tlunp

# Firewall (By default won't alloow WAN communicate to port in LAN)
firewall-config => GUI firewall
> Add Port...

iptable --help
pdf
```

## OpenLDAP Instllation
```console
su -
yum install *openldap* => any patches that related to openldap

systemctl start slapd
systemctl status slapd
cd /etc/openldap/slapd.d/

```
## Tracing Network Traffic (traceroute)
```console
traceroute www.google.com => openrg.home(192.168.1.1) => modem = gateway(netstat -rnv to check)
```

## Open an Image File
```console
# Don't work on putty or ssh
cd ~/Desktop
su -
yum install ImageMagick -y
rpm -qa | grep ImageM => check install
display ~/Desktop/image.png # Don't need to start service (systemctl start). Since it's a program?
```

## SSH-Keys - Access Remote Server without Password (SSH-Keys)
* Server : heron
* Client : my pc

```console
Client : my laptop
ssh-keygen  # passphrase??  # Create key
ssh-copy-id root@serverIP # copy key to server
# input root@serverIP passwd

Server : heron
cat /root/.ssh/authorized_keys # the client's key will be stored here

Client : my laptop
ssh root@serverIP # Don't have to input passwd anymore
```


## TCP/IP framework
  * File Transfer Protocal
  * The protocal that is used to trans
proxy?
port summarize

## Setup Personal Webpage on NTU Server via FTP
1. [__FTP Client Download__][]
2. __NTU personal web server :__ `homepage.ntu.edu.tw (140.112.2.140)`
  ```console
  [alonzo@www ~]$ nslookup homepage.ntu.edu.tw
  Server: 192.168.1.1        => Comes from local DNS server(Our modem)
  Address: 192.168.1.1#53    => Expecting NTU DNS, cool =)

  Non-authoritative answer:  => if you see this, it means our local dns doesn't 
  Name: homepage.ntu.edu.tw     have the information. So it lookup at the internet.
  Address: 140.112.2.140
  ```
3. [__Login NTU server via Filezilla__][]

4. __Upload index.html to NTU server__
5. __Checkout your webpage :__ 
  * `http://homepage.ntu.edu.tw/~r07525074/`
  * `http://140.112.2.140/~r07525074/`
__Note :__ 
  * FTP server : port 21 (by convention) 
  * [Webpage login FTP server failed][]


[]: https://filezilla-project.org/
[]: https://oper.cc.ntu.edu.tw/docs/student_homepage.html
[]: https://www.ptt.cc/man/NTU-Aikido/D5B5/M.1169717432.A.6F2.html


* 路由"器" => 建立起獨立內網LAN(拒絕外在SSH SCP等權限)，防止IP重複使用
* route table 是屬於該裝置的 route table
* route table 理論上 各裝置應相同 GW(唯一對外IP)也要相同
