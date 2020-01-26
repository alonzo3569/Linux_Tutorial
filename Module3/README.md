# Module **3.**  System Access and File System

## Lecture Notes
* Download [__Putty__][0]
* Connect LinuxVM via Putty:
   * __Host-only Adapter :__ Allows communication between _PC_ and __Virtual Machine__.
   * __Bridge Adapter :__ Allows  communication between _PC_ and __VM__ plus __allow communication to the internet__.
   * Host-only Adapter's IP(192.168.56.101) will be different from Bridge Adapter's IP(192.168.1.101x).
* Linux is a __case-sensitive__ system. (Windows isn't)

[0]: https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html

<div align=center>

<img width="900" height="500" src="https://github.com/alonzo3569/Linux/blob/master/Img/M3_Linux_file_structure.PNG"/><br></br>
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
