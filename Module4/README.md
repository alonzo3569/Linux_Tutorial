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
    * o - other = others on the system
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
    [foo@bar ~]$ chmod 777 1.txt
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
    * [__Linux檔案權限指令__][1]
    
[0]: https://www.guru99.com/file-permissions.html
[1]: https://ithelp.ithome.com.tw/articles/10202579
