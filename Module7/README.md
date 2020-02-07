# Module **7.**  Networking, Services, and System Updates

Change VM Oracle network settings to Bridge Adapter to allow internet. 

## Network Components
* __Network interface :__  A network interface is the point of interconnection between a computer and a private or public
network.
* __Network interface controller (NIC) :__ 
  * Aka network interface card, network adapter.
  * It is a computer hardware component that connects a computer to a computer network.
  * 每一個網路卡都有一個被稱為MAC位址的獨一無二的48位元序列號，它被寫在卡上的一塊ROM中。
  * 在網路上的每一個電腦都必須擁有一個獨一無二的MAC位址。沒有任何兩塊被生產出來的網路卡擁有同樣的位址。
* __Internet protocol address (IP address) :__  A computer needs and IP address to connect to another computer and the other
computer must also have an IP to listen to our communication.
* __Subnet mask :__ 
A 32-bit number that masks an IP address, 
and dividides the IP address into network address and host address.
Subnet Mask is made by setting network bits to all "1"s
and setting Host bits to all "0"s.
* Gateway
* Static v.s. DHCP
* Interface
* Interface MAC

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
    * "0" means LAN range doesn't have to match our ip. Can be any number.
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
<img src="https://github.com/alonzo3569/Linux/blob/master/Img/M7_Subnet_mask_1_0.PNG"/><br></br>
<b>Fig2. M7_Subnet_mask_flags</b><br></br><br></br>

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
