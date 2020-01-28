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
    [foo@bar ~]$ chmod 777 1.txt # r = 4, w = 2, x = 1 thus, rwx = 4+2+1 =7
    [foo@bar ~]$ chmod 000 1.txt
    [foo@bar ~]$ chmod 764 1.txt
    [foo@bar ~]$ chmod u=rwx,go=rx 1.txt
    [foo@bar ~]$ chmod u=rw,+x . # equivalent to chmod 711 . or chmod u=rwx,go=x . 
                                 # the dot (.) indicates this directory.
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

## Access Control List(ACL)
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
    
