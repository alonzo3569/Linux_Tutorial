# Module **8.**  Disk Management and Run Levels

## System Run Levels (0 thru 6
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

## Linux Boot Process (Newer Versions)
1. BIOS : Basic Input and Output Seeting (Firmware Inteface) Nothing to do with OS
  Post = Power-On Self-Test started (Check if every hardware is OK)
2. MBR : Master Boot Record
  MBS is saved in the 1st sector of the hard disk.
  MBR indicates where GRUB2 is located
  GRUB2 will be loaded to RAM later
3. GRUB2 : Grand Unified Boot Loader v2
  GRUB2 loads Linux Kernel
  /boot/grub2/grub.cfg
4. Kernel : Core of Operating system
  Loads drivers from initrd.img (Kernel -> driver -> device)
  Starts first process systemd
5. Systemd = System Daemon (PID #1)
  Start all the required process
  Read /etc/systemd/default.target to decide run level (0~6)

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

## Storage
lOCAL STORAGE
SAN (Storage area network)
NAS (Network attach Storage)
google it

## Disk Partition (df, fdisk)
```console
df 
df -h
fdisk
fdisk -l
```

## Disk Management
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
[root@disk ~]# pvcreate /dev/sdc1         => create physical volume
[root@disk ~]# pvdisplay                  => PV size /dev/sdc1 1023MB

# create Volume Group
[root@disk ~]# vgcreate oracle_vg /dev/sdc1 => oracle_vg : volume group name
[root@disk ~]# vgdisplay oracle_vg          => VG size oracle_vg 1020MB

# create Logical Volume
[root@disk ~]# lvcreate -n oracle_lv --size 1000M oracle_vg => n for new
[root@disk ~]# lvdisplay                                    => LV size lvm 1000MB

# format the disk
[root@disk ~]# mkfs.xfs /dev/oracle_vg/oracle_lv => `lvdisplay` to checkout LV path
[root@disk ~]# mkdir /oracle
[root@disk ~]# mount /dev/oracle_vg/oracle_lv /oracle
[root@disk ~]# df -h                             => check if sdc1 is mounted

# mount the partition at boot time
[root@disk ~]# vi /etc/fstab
> /dev/oracle_vg-oracle_lv tab /oracle tab xfs tab defaults tab 0 tab 0
> :wq
[root@disk ~]# init 6           => reboot
[root@disk ~]# df -h            => will see /data mounted
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
pvdisplay => show wich group is associate with which group
pvs=> same as pvdisplay
vgdisplay oracle_vg => show volume group 
[root@disk ~]# pvcreate /dev/sdd1 => create physical volume
[root@disk ~]# vgextend oracle_vg dev/sdd1 => extend volume group (vg 無法 tab 正常 手key)
[root@disk ~]# lvextend -L+1024 /dev/mapper/oracle_vg-oracle_lv => logical volume extend
[root@disk ~]# xfs_growfs /dev/mapper/oracle_vg-oracle_lv => extend file system
* 不用 mount !! 
* `df -h` 查看，/dev/mapper/oracle_vg-oracle_lv(或是 /data) 已變 2G !!
```

## Adding Swap Space
Swap space in Linux is used when the amount of RAM is full
If M < 2
then S = M *2
Else S = M + 2

```console
df -h => devtmpfs, tmpfs add all = 2G = 1G(we allocate) + 1G(system allocate)
Total:10G, / : 8G, /boot : 1G,  10G-8G-1G=1G => swap by system during install 
free -m =>  Mem => physical memory(we allocate) Swap => system allocate

```
























## LVM Configuration During Installation
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

```console
df -h => everything related to LVM will start with /dev/mapper
```
   
