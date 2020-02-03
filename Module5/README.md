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
* __su :__
  * `su - [user name]` : 切換成其他使用者。 有加 "-" 會將要login帳戶的環境變數直接覆蓋原帳戶的環境變數(原usr環境變數消失)。 使用 `env | grep 'alonzo'` 檢驗。
  * `su -` : 直接將身份變成 root 即可，但是這個指令卻需要 root 的密碼。
  * 身份變成 root 後, 切換帳號不需輸入密碼。可用此解決忘記帳戶密碼的問題。
  * root 權限強大，不建議將 root 密碼給所有使用者，建議給使用者 sudo 權限即可。
* __sudo :__
  * Setup user sudo permission :
    1. Giving an account the permission to run all the command that root has, including sudo.(Not recommanded)
      ```console
      [root@study ~]# visudo
      root    ALL=(ALL)       ALL  <==找到這一行，大約在 98 行左右
      alonzo  ALL=(ALL)       ALL  <==這一行是你要新增的！
      ```
    2. Add user to group __wheel__
      ```console
      [root@study ~]# visudo
      %wheel     ALL=(ALL)    ALL
      [root@study ~]# usermod -a -G wheel hanzo
      ```
  * Check : 
    * exit root, type `group` to check if your account belongs to group "wheel".
    * If not, close and open a new terminal (not working) or reboot.
  
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
grep wheel /etc/group
su - alonzo (!!)
sudo dmidcode => no permission denied anymore
```

## Monitor Users (who, last, w, id)
* __who :__ Check how many people are currently logged into the system.
  * `useradd [user name]`
* __last :__ Displays a list of last logged in users.
* __finger :__ A user information lookup program. Installation : `yum install finger -y` (Not working)


## Talking to Users (users, wall, write)
* __wall :__ Broadcast to every user.
  ```console 
  [alonzo@study ~]$  wall
  > Please logoff. This system is coming down for maintanance.
  > Alonzo Zhang
  > Ctrl+d
  ```
* __write :__ Private message. (Use putty to login as hanzo to test)
  ```console 
  [alonzo@study ~]$ write hanzo
  > Hi hanzo, your web is getting bigger. Please stop it.
  > Enter
  [hanzo@study ~]$write alonzo
  > Roger that. I'll shut it down. Thanks.
  > Enter
  ```

## System Utility Commands (date, uptime, hostname, uname, which, cal, bc)
* __date__
* __uptime :__ Show time and currently login users.
* __hostname :__ Show device name.
* __uname (-a) :__ Show information about the current system. ex. Linux
* __which__
* __cal :__ Calendar. `cal 9 1977`  `cal 2016`
* __bc :__ binary calculator, type `quit` to exit.


## Processes and Jobs (systemctl, ps, kill, top, crontab, at)
* __Application = Service__ : Program that runs in your computer. Word, NTP, PPT, FireFox 
* __Script :__ Everything is written in a file, amd package it and execute it.  ex. Shell script and command.
* __Process :__ After we run an application, it will generate a process or Process ID. 
* __Daemon :__ It's also process. Runs in the background. Runs until interrupted.
* __Threads :__  Every process can have multiple threads.
* __Job :__ Run a service ot process at a schedule time.

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
  systemctl enable ntpd => every time when my linux start, it will start daemon ntpd
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
  ```<br></br>
   
## Daemon, Service and Systemctl Command
* __Service :__ 常駐在記體體中的程序，且可以提供一些系統或網路功能，那就是 service。
* __Daemon :__ 
  * 達成 service 的程式我們就稱呼他為 daemon。
  * ex. 達成循環型例行性工作排程服務 (service) 的程式為 crond 這個 daemon。
  * Daemon 名稱之後會加上一個 d ，例如例行性命令的建立的	at, 與 cron 這兩個服務， 他的 daemon 程式檔名被取為 atd 與 crond。
* __Systemd :__ 
  * Linux 都用 __Systemd__ 來管理系統服務。
  * Systemd 中每一個系統服務就稱為一個服務單位（unit）。 unit 又可分為幾種類型 ex. '.service', '.target', '.socket', '.path' ...。
  * Systemd 就是僅有 __systemctl__ 這個指令來處理而已。
* __Systemstl :__ 
  * service 的啟動有兩個階段 :
    * 一個是『開機的時候設定要不要啟動這個服務』(enable/disable)
    * 以及『你現在要不要啟動這個服務』(active/inactive)
   
   ```console
   1. 列出所有 service (不論啟動與否)
   [root@study ~]# systemctl
   
   2. 查看特定 service 狀態
   [root@study ~]# systemctl status atd.service

   3. 立刻啟動 service 
   [root@study ~]# systemctl start atd.service
   
   4. 立刻關閉 service (與kill意義相同)
   [root@study ~]# systemctl stop atd.service
   
   5. 立刻重啟 service
   [root@study ~]# systemctl restart atd.service
   
   6. 開機時 啟動/不啟動 service
   [root@study ~]# systemctl enable/disable atd.service
   
   7. 僅列出 .service 類型的 unit
   [root@study ~]# systemctl list-units --type=service --all
   ```
* __Ref :__ [__vbird linux__][3]

[3]: http://linux.vbird.org/linux_basic/0560daemons.php#daemon


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

  
  
