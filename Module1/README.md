# Module **1.**  Understanding Linux Concepts

## Operating system(OS)
* __OS :__   A software program that enables the computer hardware to communicate and operate with the computer software.
  1. OS will be initially loaded into the computer by a boot program.
  2. OS manages all the other programs(called applications or application programs) in a computer.
  3. Application programs comunicate with OS through **API** (**A**pplication **P**rogram **I**nterface).
  4. User interact with OS through **command language** or **GUI** (**G**raphical **U**ser **I**nterface).
  
## Inside Linux
* __Kernel :__
  1. The core of the UNIX system. Loaded at system start up (boot).
  2. Manages the entire resources of the system.
  3. Functions performed by the kernel are:
      * Memory and allocating
      * Scheduling the work done by the CPU
      * Hard disk read/write
  
<div align=center>

![image](http://linux.vbird.org/linux_basic/0320bash/0320bash_1.jpg)
![image](http://linux.vbird.org/linux_basic/0320bash/0320bash_2.jpg)    

</div>

* __Shell :__
  1. The shell is a command interpreter.
  2. Shell takes each command and passes it to the operating system kernel.
  3. Several shells are usually available on any UNIX system. 
  4. Each shell also includes its own programming language. 
  
* __Utility programs :__
  1. UNIX provides several hundred utility programs, often referred to as **Commands**. 
  
* __Conclusions :__
  1. User(command) -> Shell(command interpreter) -> Kernel
  2. Application -> API -> ABI -> Kernel
  3. User -> GUI -> Kernel

<div align=center>

![image](http://docplayer.net/docs-images/26/9385319/images/70-0.png)

</div>

* __Refs :__ 
  * [__Vbird linux__][0]
  * [__Shell & API__][1]
  * [__API & ABI__][2]

[0]: http://linux.vbird.org/linux_basic/0320bash/0320bash-fc4.php
[1]: https://www.quora.com/What-is-the-difference-between-a-kernel-and-shell
[2]: http://gaocegege.com/Blog/csp/xen-kvm


## Hard-Disk-and-Disk-Cache
* __Hard Disk :__
  1. A set of stacked "disks"
  2. Two heads, one on each side of a disk, read or write the data as the disk spins. 
  3. Rotation speed varying from 4500 to 7200 rpm. 
  4. Disk access time is measured in milliseconds.
  5. Although the physical location can be identified with cylinder, track, and sector locations, these are actually mapped to a logical block address (LBA)
  
* __Disk Cache :__
  1. A disk cache is a mechanism for improving the time it takes to read from or write to a hard disk.
  2. The disk cache holds data that has recently been read and datas that are likely to be accessed next.
  3. Also called [**Disk Buffer**][3].

<div align=center>

![image](https://upload.wikimedia.org/wikipedia/commons/thumb/3/3d/WD5000AAKX_16MB_Buffer.jpg/220px-WD5000AAKX_16MB_Buffer.jpg)

</div>

[3]: https://zh.wikipedia.org/wiki/%E7%A3%81%E7%9B%98%E7%BC%93%E5%AD%98
