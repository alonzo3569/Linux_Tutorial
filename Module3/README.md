# Module **3.**  System Access and File System

## WildCards
* __Main wildcards in Linux :__
  * An asterisk (*) – matches one or more occurrences of any character, including no character.
  * Question mark (?) – represents or matches a single occurrence of any character.
  * Bracketed characters ([ ]) – matches any occurrence of character enclosed in the square brackets. It is possible to use different types of characters (alphanumeric characters): numbers, letters, other special characters etc.
  * Exclamation mark (!) – means __Not__ in Linux.
* __Examples :__
  * `mv -v users-0* users-info/` 	=> Option -v flag enables verbose output
  * `ls l?st.sh` => ex. last.sh, lbst.sh, lcst.sh... will match
  * `ls l[abdcio]st.sh` => ex. last.sh, lb]st.sh, lcst.sh, list.sh, lost.sh will match
  * `ls ??st*` => ex. 12st.txt, Abst.abc, okst... will match
  * `ls [clst]*` => Any file begins with c,l,s,t will match
  * `ls [clst][io]?t*` => ex. to1t.txt
  * `ls users-[0-9][a-z0-9][0-9]*`
  * `ls users-[0-9][a-zA-Z0-9][0-9]*`
  * `ls users-[0-9][a-zA-Z0-9][a-zA-Z]*`
  * `ls users-[0-9][!0-9][a-zA-Z]*` => Lists all filenames starting with users-, followed by a number, any valid file naming character __apart from a Number__, then a lower or upper case letter and ends with one or more occurrences of any character.<br></br>
  
  <div align=center>

  <img src="https://www.tecmint.com/wp-content/uploads/2017/10/Match-Characters-in-Filenames.png"/><br></br>
  
  </div>

* __Reference :__
  * [__Wildcards__][1]
  * [__10 Useful Chaining Operators in Linux with Practical Examples__][2]
  * [__10 Useful Tips for Writing Effective Bash Scripts in Linux__][3]
  
[1]: https://www.tecmint.com/use-wildcards-to-match-filenames-in-linux/
[2]: https://www.tecmint.com/chaining-operators-in-linux-with-practical-examples/
[3]: https://www.tecmint.com/useful-tips-for-writing-bash-scripts-in-linux/

## Hard and Soft Links in Linux
* __Inode :__
  * Every file in Linux has a inode __(Index Node)__.
  * Contains all file information __except for file contents and name__.
  * It contains:
    1. Inode number
    2. File size
    3. Owner information
    4. Permissions
    5. File type
    6. Number of links
* __Soft link :__
  * Aka __Symbolic link__
  * Just like shortcut in windows. Original file is deleted, the soft link is useless.
  * The inode number of a soft link is different from the original file. 
  * Has Smaller file size comapare to the original file.
  
* __Hard link :__
  * Has different name of the same file.
  * Has same file size.
  * Has same inode number.
  * If the original file is deleted, the hard link will still contain the data that were in the original file.
  * Sort of like a copy of a file.
  
* __Commands :__
  * Hard links: 
      Under target directory, type ln [source file path] [link name]. ex, `ln /home/alonzo/hulk.txt hanzo`
  * Soft links: 
      Under target directory, type ln -s [source file path] [link name]. ex, `ln -s /home/alonzo/hulk.txt`
  * Check inode information:
      Under target directory, `ls -li`
* __Trobleshooting :__
  * __Invalind cross-device link :__ This means your Hard link directory and your origianl file directory isn't on the same partition. __Hard links only work within the same partition!__

* __Reference :__
  * [__Hard vs Soft Links in Linux (video)__][4]
  * [__The difference between hard links and soft (or symbolic) links__][5]
  
[4]: https://www.youtube.com/watch?v=4-vye3QFTFo
[5]: https://medium.com/@krisbredemeier/the-difference-between-hard-links-and-soft-or-symbolic-links-780149244f7d

## Lecture Notes
* Download [__Putty__][0]
* Connect LinuxVM via Putty:
   * __Host-only Adapter :__ Allows communication between _PC_ and __Virtual Machine__.
   * __Bridge Adapter :__ Allows  communication between _PC_ and __VM__ plus __allow communication to the internet__.
   * Host-only Adapter's IP(192.168.56.101) will be different from Bridge Adapter's IP(192.168.1.101x).
* Linux is a __case-sensitive__ system. (Windows isn't)

[0]: https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html

<div align=center>

<img width="630" height="350" src="https://github.com/alonzo3569/Linux/blob/master/Img/M3_Linux_file_structure.PNG"/><br></br>
<img width="1000" height="400" src="https://github.com/alonzo3569/Linux/blob/master/Img/M3_Linux_file_structure_description.PNG" alt="Linux_file_structure_description"/>

</div>

* Linux file types:
  * When you type `ls -l`, the meaning of the first character are as below.  
  
<div align=center>

![image](https://github.com/alonzo3569/Linux/blob/master/Img/M3_Linux_file_type.PNG)

</div>

* Log in as __Root__ : `su -` 
* Log out __Root__ : `exit`
* `find [search path]` -name [filename]`
* `locate [filename]`
* __locate__ command installation :   
    * `sudo updatedb`  
    * `yum install mlocate`
    * __locate__ is faster and doesn't require any search path and filename. However, the only drawback is you have to update the database so that the new files you just created will be added to locate's DB(can be seen as cache). 
* For more command information: `man [command]` ex. `man find`
* Change user password : `passwd`
* __WildCards (*, ?, ^, [], {}) :__
  * * represents zero or more characters.
  * ? represents a single character.
  * [] represents a range of characters.
  * __Generate total 9 files__ from abc1-xyz to abc9-xyz : `touch abc{1..9}-xyz`
  * List files that start with one character and has bcd in the middle : `ls -l ?bcd*`
  * List everything that has __c OR d__ in __filename__(Name only. Doesn't include file type(.txt)) : `ls -l *[cd]*`  `ls -l *[cd]* | more`
