# Module **8.**  Disk Management and Run Levels

## System Run Levels
* __Main Run level :__
  * 0 Shut down (or halt) the system
  * 1 Single-user mode; usually aliased as s or S
  * 6 Reboot the system
* __Other Run levels :__
  * 2 Multiuser mode without networking
  * 3 Multiuser mode with networking
  * 5 Multiuser mode with networking and GUI.
```console
su -
init 0 => shot down = halt
init 1 => reboot and go to Single user mode rarely used
who -r => see which level am i
init 3 => reoobt and Multiple users command line (without GUI)
```

## Linux Boot Process
figure
```console
cd /etc/rc.d/
ls -ltr => see all the run level
cd rc5.d/
ls -ltr
# google how system bootup
```

## Linux Boot Process
* __BIOS -> MBR -> GRUB2 -> Kernel -> Systemd__
1. __BIOS :__ 
    * Basic Input and Output Seeting (Firmware Inteface) 
    * Nothing to do with OS.
    * Post = Power-On Self-Test started (Check if every hardware is OK)
2. __MBR :__ 
    * Master Boot Record
    * MBR is saved in the 1st sector of the hard disk.
    * MBR indicates where GRUB2 is located.
    * GRUB2 will be loaded to RAM later
3. __GRUB2 :__ 
    * Grand Unified Boot Loader v2
    * GRUB2 loads Linux Kernel
    * /boot/grub2/grub.cfg
4. __Kernel :__  
    * Core of Operating system
    * Loads drivers from initrd.img (Kernel -> driver -> device)
    * Starts first process systemd
5. __Systemd :__ 
    * System Daemon (PID #1)
    * Start all the required process
    * Read /etc/systemd/default.target to decide run level (0~6)

<div align=center>

<img src="https://static.thegeekstuff.com/wp-content/uploads/2011/02/linux-boot-process.png"/><br></br>

</div> 

## Message of the Day
```console
vi /etc/motd
> This is our first linux machine
> Practice linux commands
> :wq
ifconfig # and login from putty session
         # You'll see the message
```

## Customize Message of the Day
* The difference is this a script instead of a file
* The message will change based on different user
* Modify ssh config so it will show the message once a user login
```console
su -
vi /etc/profile.d/motd.sh
> #!/bin/bash
> #
> echo -e "
> ##################################
> #
> # Welcome to `hostname`
> # This system is running `cat /etc/redhat-release`
> # kernel is `uname -r`
> #
> # You are logged in as `whoami`
> #
> ##################################
> "
> :wq

chmod a+x motd.sh 
vi /etc/ssh/sshd_config  # Better backup first
> Remove #
> PrintMotd no
:wq
systemctl restart sshd.service
ifconfig # login from putty session
```

## Disk Management
* __Disk management commands:__
  * `df -h` : Show mounted partition.
  * `fdisk -l` : Checkout all the un-mount device (未配置空間) or partition (已配置空間).
* __VM Setup :__ 
  * HDD : MyLinuxVM2.di 8G
  * RAM : 1G (1024MB)
  
<div align=center>

<img src="https://github.com/alonzo3569/Linux/blob/master/Img/M8_disk_partition_after_install.PNG"/><br></br>

</div> 

* __Disk partition :__
  * devtmpfs + tmpfs = 484 + 4967*3 = 2072MB(2G) = RAM(1G) + Swap(1G)
  * RAM(1G) : VM 使用者設定
  * Swap(1G) : CentOS安裝無指定，系統自動切割 8G 中 1G 給 Swap
  * HDD 8G = /(6G) + /boot(1G) + Swap(1G) 
  * `df -h` 中 RAM 和 HDD 容量會一起顯示 (未配置空間不會顯示)
  * `free -m` 顯示 RAM + HDD Swap
  * tmpfs 可使用 RAM 和 HDD Swap 空間
  * HDD 8G = Disk /dev/sda
  * /dev/sda HDD 中以配置的空間有兩個 /dev/sda1 和 /dev/sda2  partition
  * /dev/sda1 mount 在 / 而 /dev/sda2 mount 在 /boot
  * /dev/sda1 (/) 為 LVM (mapper)
  * `fdisk -l` (未配置空間會顯示)
    * Part 1.1 : HDD 硬體裝置 `Disk /dev/sda`
    * Part 1.2 : 硬體已配置空間表 `/dev/sda1 (/)` 和 `/dev/sda2 (/boot)`
    * Part 2   : 顯示 LVM 空間(??) 有 Swap(??) 和 /
* __Add Disk and Create Standard Partition :__
  * Add a HDD on VM :
    1. Power off VM
    2. Snapshot
    3. Setting -> storage -> Controller -> Add Hard Disk -> create new one   
       -> VDI -> dynamic allocate -> 2GB -> create -> ok

<div align=center>

<img src="https://github.com/alonzo3569/Linux/blob/master/Img/M8_disk_partition_add_HDD2.PNG"/><br></br>

</div> 


```console
[root@disk ~]# fdisk -l               => 查看新插上的硬碟 Disk /dev/sdb
[root@disk ~]# fdisk /dev/sdb         => 編輯硬碟
> n => new partition
> p => primary partition
> enter * 3                           => 新配置空間為整個 HDD 不需修改
> p                                   => 查看新配置空間狀態、名稱
> w                                   => write table to disk
[root@disk ~]# fdisk -l               => 下圖 sdb 的 table 中 (/dev/sdb1)
[root@disk ~]# mkfs.xfs /dev/sdb1     => 給新配置空間指定格式 (XFS, FAT, NFTS...)
[root@disk ~]# mkdir data             => access /dev/sdb1 的資料夾
[root@disk ~]# mount /dev/sdb1 /data  => mounted sdb1 to /data
[root@disk ~]# df -h                  => 查看已配置空間是否有 /sdb1 和 /data
[root@disk ~]# vi /etc/fstab          => mount automatically after reboot
> /dev/sdb1 tab /data tab xfs tab defaults tab 0 tab 0
> :wq
[root@disk ~]# init 6
[root@disk ~]# df -h                  => will see /data mounted
[root@disk ~]# umount /data           => unmont
[root@disk ~]# mount -a               => mount all the device in /etc/fstab
```

<div align=center>

<img src="https://github.com/alonzo3569/Linux/blob/master/Img/M8_disk_partition_unmounted_sdb1.PNG"/><br></br>

</div>

## Logical Volume Managment (LVM)
* LVM allow disks to be combined together

<div align=center>

<img src="https://github.com/alonzo3569/Linux/blob/master/Img/M8_LVM_structure.PNG"/><br></br>

</div>
   
* __Create New HDD and setup LVM Partition__ 
  * Add a HDD on VM :
    1. Power off VM
    2. Snapshot
    3. Setting -> storage -> Controller SATA -> Add Hard Disk -> create new one   
               -> VDI -> dynamic allocate -> 1GB -> create -> ok
    4. Power on
  * Setup LVM Partition (pvcreate, vgcreate, lvcreate)
```console
[root@disk ~]# fdisk -l     => find /dev/sdc 1073MB (1G)
[root@disk ~]# fdisk /dev/sdc
> n
> enter * 3
> p                         => show that your partition has been created
> l                         => list known partition types
> 8e                        => LVM 8e
> t                         => change partition's system id (default 83 Linux)
> p                         => check your partition
> w                         => write table to the disk
[root@disk ~]# fdisk -l => check /dev/sdc1

# create physical volume
[root@disk ~]# pvcreate /dev/sdc1                           => create physical volume
[root@disk ~]# pvdisplay                                    => PV size /dev/sdc1 1023MB

# create Volume Group
[root@disk ~]# vgcreate oracle_vg /dev/sdc1                 => oracle_vg : volume group name
[root@disk ~]# vgdisplay oracle_vg                          => VG size oracle_vg 1020MB

# create Logical Volume
[root@disk ~]# lvcreate -n oracle_lv --size 1000M oracle_vg => n for new
[root@disk ~]# lvdisplay                                    => LV size lvm 1000MB

# format the disk
[root@disk ~]# mkfs.xfs /dev/oracle_vg/oracle_lv            => `lvdisplay` to checkout LV path
[root@disk ~]# mkdir /oracle
[root@disk ~]# mount /dev/oracle_vg/oracle_lv /oracle
[root@disk ~]# df -h                                        => check if sdc1 is mounted

# mount the partition at boot time
[root@disk ~]# vi /etc/fstab
> /dev/oracle_vg-oracle_lv tab /oracle tab xfs tab defaults tab 0 tab 0
> :wq
[root@disk ~]# init 6                                       => reboot
[root@disk ~]# df -h                                        => will see /data mounted
```

* __Extend Disk using LVM__
```console
[root@disk ~]# df -h 
[root@disk ~]# fdisk -l
[root@disk ~]# fdisk /dev/sdd
> n
> enter*3
> enter
> enter
> p
> l
> 8e
> p
> w 
[root@disk ~]# fdisk -l => /dev/sdd1
reboot
fdisk -l /dev/sdd1
# mount LVM partition to /oracle
pvdisplay                                                       => show wich group is associate with which group
pvs=> same as pvdisplay
vgdisplay oracle_vg => show volume group 
[root@disk ~]# pvcreate /dev/sdd1                               => create physical volume
[root@disk ~]# vgextend oracle_vg dev/sdd1                      => extend volume group (vg 無法 tab 正常 手key)
[root@disk ~]# lvextend -L+1024 /dev/mapper/oracle_vg-oracle_lv => logical volume extend
[root@disk ~]# xfs_growfs /dev/mapper/oracle_vg-oracle_lv       => extend file system

* 不用 mount !! 
* `df -h` 查看，/dev/mapper/oracle_vg-oracle_lv(或是 /data) 已變 2G !!
```

## Adding Swap Space
* If RAM + Swap is full, then we have to add swap space
```console
# Add 1G to swap
[root@disk ~]# dd if=/dev/zero of=/newswap bs=1M count=1024 => bb : create a 1G empty file
                                                            => if : read from a file 
                                                            => of : write from a file 
                                                            => bs : read and wirte bite size at a time
[root@disk ~]# cd /
[root@disk ~]# ls -ltr                                      => see /newswap
[root@disk ~]# chmod go-r /newswap
[root@disk ~]# mkswap /newswap                              => Make this file as a swap file
[root@disk ~]# swapon /newswap                              => enable swap file
[root@disk ~]# free -m                                      => Swap become 2G (1G before)
[root@disk ~]# vi /etc/fstab                                => enable during boot time
> /newswap swap swap 0 0
> :wq
[root@disk ~]# init 6
[root@disk ~]# free -m

# Delete swap space
[root@disk ~]# swapoff -a             => remove all swap space `free -m swap = 0`
[root@disk ~]# rm /newswap
[root@disk ~]# swapon -a              => restore all swap space
[root@disk ~]# free -m
[root@disk ~]# vi /etc/fstab
> remove /newswap swap swap 0 0
> :wq
[root@disk ~]# init 6
```

## Redundant Array of Independent Disk (RAID)
* __容錯式廉價磁碟陣列__
* __RAID__ 可以透過一個技術(軟體或硬體)，將多個較小的磁碟整合成為一個較大的磁碟裝置，提供儲存和資料保護的功能
  1. __資料安全與可靠性 :__ 指的並非資訊安全，而是當硬體 (指磁碟) 損毀時，資料是否還能夠安全的救援或使用之意
  2. __讀寫效能 :__ 例如 RAID 0 可以加強讀寫效能，讓你的系統 I/O 部分得以改善
  3. __容量 :__ 可以讓多顆磁碟組合起來，故單一檔案系統可以有相當大的容量 
* __硬體磁碟陣列 (hardware RAID)__ 是透過磁碟陣列卡來達成陣列的目的。 磁碟陣列卡上面有一塊專門的晶片在處理 RAID 的任務，效能較好
* __軟體磁碟陣列 (software RAID)__ 透過軟體來模擬硬體磁碟陣列卡，價格較磁碟陣列卡低
* __RAID 種類 :__
1. RAID-0 (等量模式, stripe)：效能最佳，無法備份
  
<div align=center>

<img src="http://linux.vbird.org/linux_basic/0420quota/raid0.gif"/><br></br>

</div>


2. RAID-1 (映射模式, mirror)：完整備份，容量較小
    
<div align=center>

<img src="http://linux.vbird.org/linux_basic/0420quota/raid1.gif"/><br></br>

</div>

3. RAID 5：效能與資料備份的均衡考量

## Storage
* __NAS :__ 
  * Network attach Storage
  * A storage device that all the device on the same network can access
  * Multiple HDD + RAID configuration + Network interface Card
  * For home or small business
  * One thing goes wrong, the whole network can't access
  * Access it Share network drive
* __SAN :__
  * Storage area network
  * Disk arrays + switches + Servers
  * For large company
  * Fault tolerant
  * Access it as a local drive
  * Interconnected using __fiber channel__



## File System Check (fsck and xfs_repair)
* __fsck__ is for `ext2, ext3, ext4`
* __xfs_repair__ is for `xfs`
* System run fsck during boot time
* System administrator could also run it manually
* Unmounted file systems before file system check

```console
[root@disk ~]# df -T                 => checkout file system type:/dev/sdb1:/data:xfs
[root@disk ~]# umount /data          => unmount dir before file sys check
[root@disk ~]# xfs_repair /dev/sdb1  => choose a xfs file system dir
[root@disk ~]# echo $?               => see exit code
[root@disk ~]# mount /dev/sdb1 /data => mount dir back
```

## System Backup
```console
[root@disk ~]# df -h

#copy /dev/sda1(/boot) to /dev/sdb1(/data)
[root@disk ~]# dd if=/dev/sda1 of=/data/boot.img  => copy partition(sda"1"); copy disk(sda)

# restore partition (if sda1 is empty of  corrupted)
[root@disk ~]# dd if=/data/boot.img /dev/sda1
```

## Network File System (NFS)
* 透過網路，讓不同的機器、不同的作業系統、可以彼此分享個別的檔案
* 遠端主機的目錄就好像是自己的一個磁碟分割槽一樣 (partition) 使用上面相當的便利
```console
#  Install NFS packages
[root@disk ~]# yum install nfs-utils libnfsidmap

# Server end
# Start and enable service
[root@disk ~]# systemctl enable rpcbind     => enable : start at boot time
[root@disk ~]# systemctl enable nfs-server
[root@disk ~]# systemctl start rpcbind
[root@disk ~]# systemctl start nfs-server
[root@disk ~]# systemctl start rpc-statd
[root@disk ~]# systemctl start nfs-idmapd

# Stop firewall
[root@disk ~]# systemctl stop firewalld               => is firewall is running stop it
[root@disk ~]# systemctl stop iptables
[root@disk ~]# systemctl disable firewalld

# Create the directory u want to share and export it
[root@disk ~]# mkdir /mypretzels
[root@disk ~]# chmod a+rwx /mypretzels
[root@disk ~]# cp /etc/exports /etc/exports_20200101  => better backup
[root@disk ~]# vi /etc/exports
> /mypretzels tab *(rw,sync,no_root_squash) => shared file and shared IP(* : to everyone) 
> :wq
[root@disk ~]# exportfs -rv => export file system     => mount.nfs access denied by server while mounting
                                                      => 代表沒有編輯 /etc/exports 或沒有 exportfs -rv

# Client end
[root@disk ~]# yum install nfs-utils rpcbind
[root@disk ~]# systemctl start rpcbind                  => start service
[root@disk ~]# ps -ef | grep rpc                        => check if it's running
[root@disk ~]# ps –ef | egrep "firewall|iptable"
[root@disk ~]# systemctl stop firewall                  => is firewall is running stop it
[root@disk ~]# systemctl stop iptables
[root@disk ~]# systemctl disable firewalld
[root@disk ~]# showmount -e 192.168.1.7 (NFS Server IP) => Create a mount point 
                                                        => no route to host: Firewall isn't off
                                                        => Turn off firewall on both size 
                                                        => sometimes systemctl stop doesn't work
                                                        => ps -ef | grep firewall ro double check
                                                        => kill -9 PID
[root@disk ~]# mkdir /mnt/kramer
[root@disk ~]# mount 192.168.1.5:/mypretzels /mnt/kramer => Mount the NFS filesystem
[root@disk ~]# df –h                                     => /mypretzels is mounted on /mnt/kramer
[root@disk ~]# umount /mnt/kramer                                         
```













## VM Configuration
1. New -> name:LVM-LinuxOS type:linux 64bit -> 1024 -> create new virtual disk -> dynamic -> 8GB -> 
2. launch VM -> Insatll & choose ISO -> ENG -> DATE/KEYBOARD/ -> local/minimal(NO GUI) -> HOSTNAME(host123 apply)  
   -> Partitioning -> i will config partitoning done   
   -> LVM -> + -> /home -> 2g  (- to remove)  
   -> + -> /var -> 2g  
   -> + -> /swap -> 1g  
   -> + -> /boot -> 500m  
   -> + -> / -> all  
   -> Done -> Accept changes  
   -> next -> root passwd and user passwd -> wait for installtion finish -> reboot  

   
## Internet Troubleshooting
```
ip addr => enp0s3 doesn't have an IP
ifconfig => no such command
systemctl status network => active/disable

systemctl restart network 
ip addr => still doesn't work
systemctl stop network 
systemctl start network 
systemctl enable network => network.service is not a native service
                         => cannot enable it via systemctl

vi /etc/sysconfig/network-scripts/ifcfg-enp0s3
> ONBOOT=yes             => no 改成 yes; ONBOOT:是否預設啟動此介面
/etc/init.d/network restart
ip addr                  => Success!
```
