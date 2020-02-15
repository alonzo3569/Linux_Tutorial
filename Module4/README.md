# Module **4.**  Linux Fundamentals

## Linux Command Line Structure
  * __What is a command :__ A __program__ that tells the Linux system to do something.
  * __Form :__ `command [options] [arguments]` __e.g.__ `ls -altri`
  * __Options and syntax for a command :__ Type `man [command]` for more information. e.g. `man chmod`
  
## Files and Directory Permissions (chmod)
  * __3 type of permissions :__
    * r - read
    * w - write
    * x - execute = running a program
  * __Each permission (rwx) can be controlled at three levels:  :__
    * u - user = yourself
    * g - group = can be people in the same project
    * o - other = Permissions used by all users other than the file owner and members of the group
    * a - all = everyone on the system
  * __Operators :__
    * (+) - Adds a permission to a file or directory
    * (-) - Removes the permission
  * __Display File or Directory permission command :__ `ls -l`
  * __Syntax :__ 
    * __chmod__ (Change mode) : `chmod [account]±[permissions] [filename]`<br></br>
  
<div align=center>

<img src="https://www.guru99.com/images/permission(1).png"/><br></br>
<b>Fig1. Symbolic Mode</b><br></br><br></br>
<img src="https://www.guru99.com/images/FilePermissions(1).png"/><br></br>
<b>Fig2. Numeric Mode</b><br></br><br></br>
<img src="https://github.com/alonzo3569/Linux/blob/master/Img/M4_File_permission_numeric_cheatsheet.PNG"/><br></br>
<b>Fig3. Numeric Mode Cheatsheet</b><br></br>

</div>

  * __Example :__
    ```console
    [foo@bar ~]$ chmod ugo+r 1.txt
    [foo@bar ~]$ chmod a+r 1.txt
    [foo@bar ~]$ chmod ugo-rwx 1.txt
    [foo@bar ~]$ chmod 777 1.txt   # r = 4, w = 2, x = 1 thus, rwx = 4+2+1 =7
    [foo@bar ~]$ chmod 000 1.txt
    [foo@bar ~]$ chmod 764 1.txt
    [foo@bar ~]$ chmod u=rwx,go=rx 1.txt
    [foo@bar ~]$ chmod u=rw,+x .   # equivalent to chmod 711 . or chmod u=rwx,go=x . 
                                   # the dot (.) indicates this directory.
    [foo@bar ~]$ chmod -R u+w src/ # give w permission to every sub directory and file
                                   # Without -R permission of files and dir under src won't change
    ```
    
<div align=center>

<br></br>
<img src="https://github.com/alonzo3569/Linux/blob/master/Img/M4_File_permission.PNG"/><br></br>
<b>Fig4. Terminal Example</b>

</div>

  * __Reference :__
    * [__File Permissions in Linux__][0]
    
[0]: https://www.guru99.com/file-permissions.html

## Files Ownership and Group Ownership (chown, chgrp)
 * __File Ownership :__ **chown**
 * Login as **root** before changing ownership `su -`
 * __Syntax :__ 
   * `chown [user]:[group] [filename]`
   * `chown [user].[group] [filename]`
   * `chown [user] [filename]`
 * __Group Ownership :__ **chgrp**
   * __Syntax :__ `chgrp group filename`

 * __Reference :__
    * [__Understanding Basic File Permissions and ownership in Linux__][1]
    * [__Vbird Group Concept__][2]
    
[1]: https://www.thegeekdiary.com/understanding-basic-file-permissions-and-ownership-in-linux/
[2]: http://linux.vbird.org/linux_basic/0210filepermission.php#UserandGroup

## Access Control List (ACL)
  * __ACL :__ 
    * A more flexible permission mechanism for file systems.
    * Give permission to an user without making user a member of group.
  * __getfacl :__   
    * **getfacl** is used to show file's ACL.
    * Usage : `getfacl [filename]` 
  * __setfacl :__ 
    * Add permission for a __user__ : `setfacl -m u:user:rwx /path/to/file` (-m for modify)
    * Add permission for a __group__ : `setfacl -m g:group:rw /path/to/file`
    * To allow all files or directories to _inherit **Default** ACL_ from the directory it is within
      * `setfacl -dm [entries] /path/to/file`<br></br>
      ```console
      [root@bar ~]$ setfacl -m u::rwx,g::---,o::--- /home/alonzo/test
      [root@bar ~]$ setfacl -dm u::rx,g::rwx /home/alonzo/test
      
                      # The command above will setup "Default" ACL for sub-directory inheritance
                      # The sub directory created later will inherit "Default" ACL
                      # "u:user:rwx" are called 'Entries'
                      # "u::rwx" 2nd colon(:) represent current user(type whoami to check)
                      # Only sub directory(Not file) will inherit default ACL(completely same as default)
                      # File's ACL which inherit from parent dir will be slightly different from default ACL
                      
      [root@bar ~]$ getfacl /home/alonzo/test
      # file: home/alonzo/test
      # owner: root
      # group: root
      user::rwx
      group::---
      other::---
      default:user::r-x      # This "default" section will be inherit by sub dir
      default:group::rwx
      default:other::---
      
      [root@bar ~]$ mkdir /home/alonzo/test/test_sub_dir       # test sub dir inheritance
      [root@bar ~]$ getfacl /home/alonzo/test/test_sub_dir 
      # file: home/alonzo/test/test_sub_dir 
      # owner: root
      # group: root
      user::r-x              # same as parent dir's ACL
      group::rwx
      other::---
      default:user::r-x      
      default:group::rwx
      default:other::---
      
      [root@bar ~]$ touch /home/alonzo/test/test_sub_file       # test sub 'file' inheritance
      [root@bar ~]$ getfacl /home/alonzo/test/test_sub_file
      # file: home/alonzo/test/test_sub_file
      # owner: root
      # group: root
      user::r--              # parent dir's ACL but without execution permission
      group::rw-             # no default ACL
      other::---
      ```
    * Effective permission __"mask"__ : 
      * `setfacl -m m:[user]:[rwx] /path/to/file`
      * Can also setup default mask for inheritance
      * Mask is used for double checking. Preventing user for setting wrong permission for file or directory
      * Usually Mask will be set to "rwx"
      
      ```console
      [root@study ~]# setfacl -m m:r acl_test1  # Mask 充當"最大允許權限", 所有usr或group權限皆不可多於'r--'
      [root@study ~]# getfacl acl_test1
      # file: acl_test1
      # owner: root
      # group: root
      user::rwx
      user:vbird1:r-x        # effective:r-- <==vbird1+mask均存在者，僅有 r 而已，x 不會生效
      group::r--             
      group:mygroup1:r-x     # effective:r-- 
      mask::r--
      other::r--
      ```
    * Remove a specific entry : `setfacl -x u:alonzo /path/to/file`
      * 'u:alonzo': Just pick an user. Don't need to decide permission[rwx]
    * Remove a specific entry : `setfacl -b path/to/file` 
      * Default ACL, Addition ACL(user:alonzo:r-x), anything other than current account(::) will be remove<br></br>

      ```console
      # file: acl_test1         # result after removing additional ACL
      # owner: root
      # group: root
      user::rwx
      group::r--             
      mask::r--
      other::r--
      ```
 * __Note :__
   * You can check if there are any extra permissions set through ACL using `ls -l` command.
   * There will be an extra “+” sign after the permissions, such as `-rw-rwxr–+`
   * '+' sign indicates there are extra ACL permissions set which you can check by getfacl command

 * __Reference :__
    * [__Vbird ACL__][3]
    
[3]: http://linux.vbird.org/linux_basic/0410accountmanager.php#acl_talk_cmd
    
## Help Command
 * __whatis :__ `whatis ls`
 * __--help :__ `chmod --help`
 * __man :__ `man pwd`
 
## Adding Text to Files
 * __vim/vi__
 * __echo "..." >/>> [filename] __
   * __> :__ Will overwrite
   * __>> :__ Append text to the end of the file
   * __>/>>__ are called "Redirecting"
   * ex. `ls -ltr > 1.txt`, `date >> 1.txt`
   
## Input Output Redirection
* __Standard I/O__:
  * Standard Input(__STDIN__)      : File Descriptor 0, using (<) or (<<) 
  * Standard Output(__STDOUT__)    : File Descriptor 1, using (>) or (>>) 
  * Stand Error Output(__STDERR__) : File Descriptor 2, using (2>) or (2>>)  _Note: No spaces between 2 and >_
    <br></br>
    ```console
    [dmtsai@study ~]$ find /home -name .bashrc
    find: '/home/arod': Permission denied    <== Standard error output
                                             ### current user(dmtsai) doesn't have permission to access other user's "~" directory
    find: '/home/alex': Permission denied    <== Standard error output
    /home/dmtsai/.bashrc                     <== Standard output
    ```
* __Standard "Output" Redirection__:
  * __Redirecting STDOUT & STDERR to different files :__
  <br></br>
    ```console
    [dmtsai@study ~]$ find /home -name .bashrc > list_right 2> list_error  <== STDOUT to list_right file and STDERR to list_error file
    ```
  * __Discard STDOUT & STDERR to blackhole :__
    <br></br>
    ```console
    [dmtsai@study ~]$ find /home -name .bashrc 2> /dev/null
    /home/dmtsai/.bashrc  <= Only STDOUT is displayed on screen while STDERR is dumped
    ```
  * __Writing STDOUT & STDERR to same file :__
    ```console
    [dmtsai@study ~]$ find /home -name .bashrc > list 2> list  <==錯誤
    [dmtsai@study ~]$ find /home -name .bashrc > list 2>&1     <==正確
    [dmtsai@study ~]$ find /home -name .bashrc &> list         <==正確
    ```
* __Standard "Input" Redirection__:
  * __(<) :__ Basically means file input.
  * __(<<) :__ Allow user to setup EOF using keyboard input. 
    <br></br>
    ```console
    [dmtsai@study ~]$ cat > catfile                    # 1. Take cat combine with > for example 
    testing
    cat file test
    <==type [ctrl]+d to exit

    [dmtsai@study ~]$ cat catfile
    testing
    cat file test

    [dmtsai@study ~]$ cat > catfile < ~/.bashrc       # 2. "<" for file input
    [dmtsai@study ~]$ ll catfile ~/.bashrc
    -rw-r--r--. 1 dmtsai dmtsai 231 Mar  6 06:06 /home/dmtsai/.bashrc
    -rw-rw-r--. 1 dmtsai dmtsai 231 Jul  9 18:58 catfile

    [dmtsai@study ~]$ cat > catfile << "alonzo"       # 3. "<<" is used for setting up EOF
    > This is a test.
    > OK now stop
    > alonzo  <== Enter "alonzo" to exit instead of using [ctrl]+d

    [dmtsai@study ~]$ cat catfile
    This is a test.
    OK now stop     <== EOF won't be written into the file
    ```
* __Reference __:
  * [__Vbird Redirection__][4]
  * [__Input Output Redirection in Linux__][5]
  
[4]: http://linux.vbird.org/linux_basic/0320bash.php#redirect
[5]: https://www.guru99.com/linux-redirection.html#5

## $?, && and ||
 * __$? :__ If command runs successfully, it will return `$? = 0`, else `$? ≠ 0`.
 * __command1 && command2 :__
   * 1. If command1 run successfully($? = 0), then run command2.
   * 2. If command1 returns error ($? ≠ 0), then command2 won't be executed.
 * __command1 || command2 :__
   * 1. If command1 run successfully($? = 0), then command2 won't be executed.
   * 2. If command1 returns error ($? ≠ 0), then run command2.
 * 
 ```console
 [dmtsai@study ~]$ ls /tmp/abc && touch /tmp/abc/hehe   # If dir abc exist, touch file hehe. Else, stop.
 [dmtsai@study ~]$ ls /tmp/abc || mkdir /tmp/abc        # If dir abc not exist, make dir abc. Else, stop.
 [dmtsai@study ~]$ ls /tmp/abc || mkdir /tmp/abc && touch /tmp/abc/hehe  
 
                   # 1. If dir abc exist, skip mkdir because of (||) and pass $? = 0 to (&&). Execute touch because of &&.
                   # 2. If dir abc Not exist, execute mkdir and pass $? = 0 to (&&). Execute touch.
                   # ($? = 0) or ($? ≠ 0) will be passed from left to right.
 ```

<div align=center>

<img src="http://linux.vbird.org/linux_basic/0320bash//cmd_1.gif"/><br></br>

</div>

* __Reference :__
  * [__Vbird $?, && and ||__][6]
  
[6]: http://linux.vbird.org/linux_basic/0320bash.php#redirect_com

## Linux Pipe Command
  * __管線符號 (|) :__
    * 能將前一個指令(__不一定為管線指令__)所產生的 __STDOUT__ 輸出給後方的 __管線指令__.
    * 對於 __STDERR__ 會予以忽略.
  * __管線指令(Pipe Command) :__ 
    * 能接受 __STDIN__ 的指令才是 __管線指令__, 並非所有指令皆為管線指令.<br></br>
        ```console
        [dmtsai@study ~]$ ls -al /etc | less
        ```
      * less, more, head, tail 等都是可以接受 standard input
      * 但ls, cp, mv 無法接受 STDIN, 就不是管線命令
    * 管線符號 (|) 後方所接的指令, 必為 __管線指令__(如下圖). 
    * 只接受 __STDOUT__, 對於 __STDERR__ 會予以忽略.
    * 使用`2>&1`即可讓 __STDERR__ 被管線指令鎖運用.

<div align=center>

<img src="http://linux.vbird.org/linux_basic/0320bash//0320bash_3.png"/><br></br>

</div>    
    
  * __管線指令(Pipe Command) 可大致分為以下幾類 :__
    * __擷取命令：__ [__cut, grep__][7]
      * 擷取訊息以"行"為單位(一次處理一行)
      * __cut：__ <br></br>
        ```console
        [dmtsai@study ~]$ echo ${PATH} | cut -d ':' -f 3,5  => -d 以 ':' 為分隔單位, -f 選出分割後第3個'和'第5個部分
        [dmtsai@study ~]$ export | cut -c 12-20             => -c 以'字元'為單位, 取出每行第12-20個字元
        [dmtsai@study ~]$ last | cut -d ' ' -f 1            => -d 以 ':' 為分隔單位, -f 選出分割後第1個部分 last列出 user login 紀錄
        ```
      * __awk：__ 
        * __cut__ 較難處理以空個或tab做區隔的資料, 這種以 __awk__ 處理較佳<br></br>
        ```console
        [dmtsai@study ~]$ awk '{print $1}' file                           => List 1st field from a file
        [dmtsai@study ~]$ ls –l | awk '{print $1,$3}'                     =>  List 1 and 3rd field of ls –l output
        [dmtsai@study ~]$ ls –l | awk '{print $NF}'                       =>  Last field of the output
        [dmtsai@study ~]$ ls –l | awk '/Jerry/ {print}' file              =>  Search for a specific word
        [dmtsai@study ~]$ ls –l | awk -F: '{print $1}' /etc/passwd        =>  Use ':' as field seperator)
        [dmtsai@study ~]$ echo "Hello Tom" | awk '{$2="Adam"; print $0}'  =>   Replace words field words, $0 shows every column
        [dmtsai@study ~]$ cat file | awk '{$2=“Imran"; print $0}'         =>  Replace words field words
        [dmtsai@study ~]$ awk 'length($0) > 15'                           => Get lines that have more than 15 byte size
        [dmtsai@study ~]$ ls -l | awk '{if($9 == "seinfeld") print $0;}'  => Get the field matching seinfeld in /home/iafzal
        [dmtsai@study ~]$ ls -l | awk '{print NF}'                        => Number of fields(cloumns). NF without '$'
        ```
      * __grep：__ <br></br>
        ```console
        [dmtsai@study ~]$ last | grep 'root'     => 將 last 當中，有出現 root 的那一行就取出來
        [dmtsai@study ~]$ last | grep -v 'root'  => 將 last 當中，"沒有" root 的就取出
        [dmtsai@study ~]$ last | grep 'root' |cut -d ' ' -f1  => 取出含關鍵字的行, cut後取第一欄
        [dmtsai@study ~]$ grep --color=auto 'MANPATH' /etc/man_db.conf => --color=auto 關鍵字用特殊顏色顯示
        [dmtsai@study ~]$ egrep –i “keyword|keyword2” file = Search for 2 keywords.
        ```

    * __排序命令：__ [__sort, uniq, wc__][8]
      * __sort：__ 
        * 預設『以第一個』資料來排序
        * 預設是以『文字』型態來排序
        * 預設分隔符號為『tab』<br></br>
        ```console
        [dmtsai@study ~]$ cat /etc/passwd | sort                  => 預設以資料第一個字母排序
        [dmtsai@study ~]$ cat /etc/passwd | sort -t ':' -k 3      => 以第三欄 "含後面" 排序
        [dmtsai@study ~]$ cat /etc/passwd | sort -t ':' -k 3,3 -n => 純以第三欄來排序, 並用數字大小排序
        ```
      * __uniq：__ 
        * __sort__ 完才可用 __uniq__
        * -i  ：忽略大小寫字元的不同
        * -c  ：進行計數<br></br>
        ```console
        [dmtsai@study ~]$ last | cut -d ' ' -f1 | sort | uniq     => 顯示登入的有誰
        [dmtsai@study ~]$ last | cut -d ' ' -f1 | sort | uniq -c  => 顯示每個人的登入總次數
        ```   
      * __wc：__ 
        * -l  ：列出多少行
        * -w  ：列出多少單字(word)
        * -m  ：列出多少字元(char)
        * -c  ：列出多少byte<br></br>
        ```console
        [dmtsai@study ~]$ cat /etc/man_db.conf | wc   => 顯示 '行', 'word', 'char'
        [dmtsai@study ~]$ last | grep [a-zA-Z] | grep -v 'wtmp' | grep -v 'reboot' | \
        > grep -v 'unknown' |wc -l 
        ```
        
    * __雙向重導向：__ [__tee__][9]
      * __tee：__ 
        * 讓 __STDOUT__ 可以輸出到螢幕, 也可以將 __STDOUT__ 繼續傳給下一個管線命令
        * -a  ：以累加 (append) 的方式，將資料加入 file 當中. 否則tee會覆蓋原本文件.<br></br>
        
        ```console
        [dmtsai@study ~]$ last | tee last.list | cut -d " " -f1   
        [dmtsai@study ~]$ ls -l /home | tee ~/homefile | more  
        [dmtsai@study ~]$ ls -l / | tee -a ~/homefile | more
        ```
    
    * __字元轉換命令：__ [__tr, col, join, paste, expand__][10]
    * __分割命令：__ [__split__][11]
    * __參數代換：__ [__xargs__][12]<br></br>

        
<div align=center>

<img src="http://linux.vbird.org/linux_basic/0320bash//0320bash_5.png"/><br></br>

</div>


[7]: http://linux.vbird.org/linux_basic/0320bash.php#pipe_1
[8]: http://linux.vbird.org/linux_basic/0320bash.php#pipe_2
[9]: http://linux.vbird.org/linux_basic/0320bash.php#pipe_3
[10]: http://linux.vbird.org/linux_basic/0320bash.php#pipe_4
[11]: http://linux.vbird.org/linux_basic/0320bash.php#split
[12]: http://linux.vbird.org/linux_basic/0320bash.php#xargs
 
 
## Other Useful Linux Command
* head -1 1.txt => display first line of the file
* tail -1 1.txt => display last line of the file
* less up/down arrow, f,b page up page down
* commamd --help for more information

## Compress and uncompress (tar, gzip, gunzip)
 * __tar :__ 可決定壓縮檔檔名
   * Compress : `tar cvf alonzo.tar /home/alonzo`
   * Uncompress : `tar xvf alonzo.tar` (可直接解壓.tar.gz)
 * __gzip :__ 壓縮檔名為原檔名加上.gz
   * Compress : `gzip alonzo.tar` => alonzo.tar.gz
   * Uncompress : `gzip -d alonzo.tar.gz` or `gunzip alonzo.tar.zip`
   
## Truncate File Size (truncate)
 * __truncate :__ truncate is used to shrink or extend the file size. It will lose content while shrinking.
   * Syntax : truncate -s [ file size after truncate ] [ filename ] 
   * Check file size `ls -l [ file name ]` => 5th column
   * ex. `truncate -s 40 1.txt` 
   * Compress : `tar cvf alonzo.tar /home/alonzo`
   * Uncompress : `tar xvf alonzo.tar` (可直接解壓.tar.gz)
   
## Combining and Splitting Files
  * Syntax : split -l [ seperate by how many lines? ] [ output filename ] 
  * The output file will be like [output filename] + aa,bb,ac.... Depends on how many output files are there.

## Troubleshooting File Issues
* __Permission Denied :__ 
  1. Check source dir/file permission and target dir/file permission
  2. Check Parent directory permission
* __File doesn't exist :__
  1. Check upper lower case of the dir/file
  2. Check absolute path and relative path
* __Hidden files/directory :__
  1. Every file or directory starts with '.' will be hidden
  2. `ls -a`
