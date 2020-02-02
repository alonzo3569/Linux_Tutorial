# Module **5.**  System Access and File System

## "sed" Command
* __Funtionalities :__
  * Replace a string in a file with a newstring
  * Find and delete a line
  * Remove empty lines
  * Remove the first or n lines in a file
  * To replace tabs with spaces
  
* __Syntax :__ <br></br>
    ```console
    [alonzo@study ~]$ sed 's/Kenny/Lenny/g' 1.txt  => 's' for substitute 'g' for global, replace all the Kenny in the file
    [alonzo@study ~]$ sed -i 's/Kenny/Lenny/g' 1.txt  => '-i' will modify 1.txt
    [alonzo@study ~]$ sed -i 's/Kenny//g' 1.txt  => Will remove every Kenny in 1.txt
    [alonzo@study ~]$ sed -i '/Kenny/d' 1.txt  => 'd' for delete, will delete the every line that has Kenny
    [alonzo@study ~]$ sed -i '/^$/d' 1.txt  => Will remove every empty line. '^' shows everything that starts '$' means everything that ends
    [alonzo@study ~]$ sed -i '1,2d' 1.txt  => Will remove the first and the second line.
    [alonzo@study ~]$ sed -i 's/\t/ /g' 1.txt  => Will remove all the tab and replace it with space.
    [alonzo@study ~]$ sed -n 12,18p 1.txt  => Show line 12 to line 18.
    [alonzo@study ~]$ sed 12,18d 1.txt  => Show everything except line 12 to line 18.
    [alonzo@study ~]$ sed -i G 1.txt  => Insert empty line after every line.
    [alonzo@study ~]$ sed -i '8!s/Kenny/ /  => Remove every Kenny except the 8th Kenny.
    ```
* __Replace in vim :__  `%s/Kenny/K`

## User Account Management (useradd, groupadd, usermod, userdel, groupdel)
* __Log in as root :__ `su -`
* __Add user or group :__
  * `useradd [user name]`
    * 在 /etc/passwd 裡面建立一行與帳號相關的資料，包括建立 UID/GID/家目錄等
    * 在 /etc/shadow 裡面將此帳號的密碼相關參數填入，但是尚未有密碼
    * 在 /etc/group 裡面加入一個與帳號名稱一模一樣的群組名稱
    * 在 /home 底下建立一個與帳號同名的目錄作為使用者家目錄，且權限為 700
  * `groupadd  [group name]`
* __Setup password for user :__
  * `passwd [user name]`
* __Check user :__
  * `id [user name]`
  * `users` => [__users__][0] command without any option will print the users currently logged in.
  * `groups` => Show all the groups that current user belongs to. The first one will be "__effective group__".
    * __Effective group :__ 
      * When the user touch a new file, the file (GID) will belongs to effective group by default.
    * __Initial group :__ When we create a new user account via usermod, system will also create a group using user's name. This group is called "initial group".
    * Effective group will be initial group by default. Unless you change it. Use [__newgrp__][1] to change effective group.
      ```console
      [root@study ~]# grep dmtsai /etc/group /etc/shadow
      /etc/group:wheel:x:10:dmtsai    <==次要群組的設定、安裝時指定的
      /etc/group:users:x:100:dmtsai   <==次要群組的設定
      /etc/group:dmtsai:x:1000:       <==因為是初始群組，所以第四欄位不需要填入帳號
      
      /etc/gshadow:wheel:::dmtsai     <==次要群組的設定
      /etc/gshadow:users:::dmtsai     <==次要群組的設定
      /etc/gshadow:dmtsai:!!::
      
      ```
* __Delete user or group:__
  * `userdel -r [user name]` => -r delete user home directory as well
  * `groupdel [group name]`
* __Add user to group :__
  * `usermod -G [group name] [user name]` 
  * ex. `usermod -G heros hanzo`    # add hanzo to group heros
* __Files :__
  * `cat /etc/passwd`  
    * usr : psswd(encrypted) : usrid : grpid : home dir : shell path
    * Store user account information except for password. Password will be saved in /etc/shadow.
  * `cat /etc/group`   
    * usr : psswd : grpid : other usr in this grp
  * `cat /etc/shadow`  
    * Won't show encrypted psswd if you haven't setup.
    * Encrypted password will be saved in here.
  * `grep [user name] /etc/passwd /etc/group /etc/shadow`

<div align=center>

<img src="http://linux.vbird.org/linux_basic/0410accountmanager//centos7_id_link.jpg"/><br></br>

</div>  

* __All-in-one command :__ (recommended)
  * useradd -g heros -s /bin/bash -c "Ironman Character" -m -d /home/ironman ironman
  * 'g' for grp, 's' for shell 'c' for discription
* __Cascade Permission :__
  * chgrp -R heros hanzo => cascade group heros permission to every folder of hanzo
  * -R 是為了連同家目錄底下的使用者/群組屬性都一起變更的意思 ( chown, chmod中-R意義亦相同)
  * 沒有 -R ，是因為我們僅要修改目錄的權限而非內部檔案的權限<br></br>
    ```console
    su -
    useradd hanzo
    id hanzo
    or check ls -l /home/
    groupadd heros
    check group name : cat /etc/group
    userdel -r hanzo (r to remove dir as well)

    groupadd nonewgroup
    cat /etc/group
    groupdel nonewgroup

    man usermod (after create an usr)
    useradd hanzo
    id hanzo
    usermod -G heros hanzo    # add hanzo to group heros
    grep hanzo /etc/group

    >heros:x:1003:hanzo
    >hanzo:x:1002:

    ls -l => group will still belongs to hanzo
    chgrp -R heros hanzo => cascade group heros permission to every folder of hanzo

    cat /etc/passwd  usr:psswd(crypted):usrid:grpid:home dir:shell path
    cat /etc/group   usr:psswd:grpid:other usr in this grp
    cat /etc/shadow  won't see encrypted psswd for hanzo sice we havn't setup passed for hero or hanzo

    useradd -g heros -s /bin/bash -c "Ironman Character" -m -d /home/ironman ironman
    # 'g' for grp, 's' for shell 'c' for discription
    
    id ironman
    cat /etc/passwd
    passwd ironman
    ```
[0]: https://www.geeksforgeeks.org/users-command-in-linux-with-examples/
[1]: http://linux.vbird.org/linux_basic/0410accountmanager.php#newgrp

## Switch Users and Sudo Access (su, sudo)
* __Add user or group :__
  * `useradd [user name]`
  
``` console 
su - spiderman
exit
su - 
if u log in as root, system won't ask you for password to switch to other account
[root@study:]$ su - spiderman => switch from root to spiderman, system won;t ask for passwd

su - 
visudo
find group wheel
usermod -aG wheel alonzo
grep wheel /etc/wheel(group?)
su - alonzo (!!)
sudo dmidcode => no permission denied anymore
```

## Monitor Users (who, last, w, id)
* __Add user or group :__
  * `useradd [user name]`
```console
who => Check how many people are currently logged into the system 
last
w
finger
yum install finger -y
```

## Talking to Users (users, wall, write)
* __Add user or group :__
  * `useradd [user name]`
  
```console
users
wall
> Pleas logoff. This is a comingg down for maintanance
> Alonzo ZHang
> Ctrl+d
write spider
> Hi Spider, your web is getting bigger. Please stop
> Enter
write alonzo
> Roger that. I'll shut it down. Thanks.
> Enter
```

## System Utility Commands (date, uptime, hostname, uname, which, cal, bc)
* __Add user or group :__
  * `useradd [user name]`
  
```console
date 
uptime
hostname
uname (-a)
which [command]
ls -l /usr/bin/pwd
cal
cal 9 1977
cal 2016
bc (binary calculator)
quit

```

## Processes and Jobs (systemctl, ps, kill, top, crontab, at)
* Application = Service : Program that runs in your computer. Word, NTP, PPT, FireFox 
* Script : Everything is written in a file, amd package it and execute it.  ex. Shell script and command.
* Process : After we run an application, it will generate a process or Process ID. 
* Daemon : It's also process. Runs in the background. Runs until interrupted.
* Threads :  Every process can have multiple threads.
* Job : Run a service ot process at a schedule time.

* Process/Service Command:
  * systemctl(new) and service(old)
  * ps
  * top
  * kill
  * crontab
  * at
  
  ```console
  su -
  systemctl restart ntpd
  ps -ef | grep ntpd
  systemctl status ntpd
  system stop ntpd
  ps -ef | grep ntpd => will get process itself
  systemctl enable ntpd => every time will my linux start, it will start daemon ntpd
  ps -ef | grep rsyslog
  systemctl status rsyslog
  
  ps -ef | grep ntpd
  systmectl restart ntpd
  ps -ef | grep ntpd
  kill 12027
  ps -ef | grep ntpd
  
  date
  su -
  date -s "12 Mar 2018 13:20:00"
  date
  exit
  
  crontab -e 
  > 22(minute) 13(hour) *(everyday) 3 *(day of the week) echo "This is my first crontab-entry" > crontab.txt  => * means every 
  :wq
  After the time we set in crontab, crontab.txt will appear.
  ```

## Additional cronjobs (hourly, daily, weekly, monthly)
  
  ```console
  su -
  cd /etc
  ls -l | grep cron
  cd cron.daily # put your script here to run daily  # put your scripts here if you don;t want to specify crontab -e
  ls -l
  cat /etc/anacrontab  => xheck when will it run daily/monthly/weekly
  cat /etc/cron.d/0hourly => check when will hourly run
  ```
  
## Process Management (bg, fg, nice)
```console
su -
sleep 5 => wait for few second to get your prompt back
ctrl+z
jobs
bg
jobs
ps -ef | grep sleep
fg

sleep 100 (on putty) => usually the process will be closed when the terminal is closed
ps -ef | grep sleep (on putty)

nohup sleep 75 & => will create a file "nohup.out"
jobs
close putty terminal, process won't be closed

nohup sleep 73 > /dev/null 2>&1 & => remove STDOUT
jobs
fg

nice -n 5 sleep 10 => -n -20 will be top priority
```

## System Monitoring Commands (df, dmesg, iostat 1, netstat, free, top)
```console
df => disk information
df -h => easier to understand, if use% is 100 percent, man du for freeing more space in disk

dmesg => system hardware error message
dmesg | more

iostat 1 => refresh every 1 sec, ctrl c to quit

netstat -rnv => find gatway information blah blah blah...
man netstat

free => show physical memory swap,...

cat /proc/cpuinfo => cpu information
cat /proc/meminfo => memory info
```

## System Logs Monitor (/var/log)
  * __Log directory :__ /var/log
  * boot : when system boot
  * messages : system issue
```console
cd /var/log
ls -l | more
ll | more
su - 
cd /var/log
more book.log
ls -l boot.log => rewrite after reboot

more cron => crontab activity
more dmesg => hardware information 
more maillog => send mail deamon log
more secure => records login/logout log
tail -f secure
more messages => alllll the message!
grep -i error messages

```

  
  
