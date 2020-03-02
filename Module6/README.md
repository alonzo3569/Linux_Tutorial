# Module **6.**  Shell Scripting

## What is a shell?
```console
echo $0  => checkout what kind of shell u're using
cat /etc/shells => Shells that are installed in your system
cat /etc/passwd
```

## Types of Shells
* __GUI :__ Execute command through mouse
  * Gnome
  * KDE
* __sh__
* __bash__
* __csh and tcsh__
* __ksh__

## Basic Shell Script
```bash
#!/bin/bash

# Assigning variables
a=alonzo
a1='logan zhang'                      # If u are assigning variables to multiple words, you have to use ''
command=`pwd`                         # For letter, use ('). For command, use (`).
path=$(pwd)

# Output variables
echo "My id is $a"
echo "My name is $a1"

# Output commands 
echo "You're currently at: $command"  # Using variable to output command
echo "Today is `date`"
echo "Number of user login: `who | wc -l `"

# Read command-line input
echo "What's your name?"
read guest_name
echo "Hi $guest_name ! I'll create a file for you."
touch $guest_name.txt
ls -l
rm $guest_name.txt
```

## if-then Scripts
* __Syntax :__
  ```bash
  #!/bin/bash
  if [ statement ]
    then
      # Do something
    elif
      # Do something
    else
      # Do something
  fi
  ```
* __Example :__
  ```bash
  #!/bin/bash
  count=100
  if [ $count -eq 100 ]     # [__] Space is required after square bracket
  then
   echo Count is 100        # Any form of indentation will work. (2 spaces, 1 spaces, no spaces...)
  else
   echo Count is not 100
  fi
  ```

* __Other if statements :__
  ```bash
  #!/bin/bash
  # 1. Read cmd input and compare with letter
  echo "Do you like working in IT? (y/n)"
  read Like
  echo
  if [ "$Like" == y 
  elif [ "$Like" == n ]
  a=`date | awk '{print $1}'`
  if [ "$a" == Mon ]

  # 2. File existence
  if [ -e /home/iafzal/error.txt ]      # Check if the file is here
  if test -s error.txt                  # Test if the error.txt file exist and its size is greater than zero

  # 3. Or/And statement
  if [ "$a" = Monday ] || [ "$a" = Tuesday ] # Or 
  if [ "$a" = Monday ] && [ "$a" = Tuesday ] # And

  # 4. Return value of former command
  if [ $? -eq 0 ]                       # If former command execute successfully it will return $?=0. Else $?=1 (same as c++ by default)
  ```

* __Comparisons :__
  * For letters :
    * == equal to for letters
    * !== not equal to for letters
  * For numbers: 
    * -eq equal to for numbers
    * -ne not equal to (or use !=)
    * -lt less than
    * -le less than or equal to
    * -gt greater than
    * -ge greater than or equal to

* __File Operations :__
  * -s file exists and is not empty
  * -f file exists and is not a directory
  * -d directory exists
  * -x file is executable
  * -w file is writable
  * -r file is readable

## For Scripts
* __Syntax :__
  ```bash
  #!/bin/bash
  for i in [list of content]
    do 
    # Do things here
  done
  ```
* __Other for statements :__
  ```bash
  for i in 1 2 3 4 5
  for i in {1..5}
  for day in Mon Tue Wed Thu Fri
  for username in `awk -F: '{print $1}' /etc/passwd`
  ```
* __Example :__
  ```bash
  #!/bin/bash

  # Setup initial value
  a=1

  # For loop
  for username in `awk -F: '{print $1}' /etc/passwd`
    do 
    echo "Username $((a++)) : $username"
  done
  ```
  
## do-while Scripts
```bash
#!/bin/bash
count=0
num=10
while [ $count -lt 10 ]                         # less than
do
 echo
 echo $num seconds left to stop this process $1 # $1 refers to PID
 echo                                           # execute script by running `./test.sh 100` to assign PID
 sleep 1
 ((num--))                                      # Special syntax in bash
 ((count++))
done
echo $1 process is stopped!!!
```
  
## case Scripts 
```bash
#!/bin/bash
echo
echo Please chose one of the options below
echo
echo 'a = Display Date and Time'
echo 'b = List file and directories'
echo 'c = List users logged in'
echo 'd = Check System uptime'
echo

  read choices
  case $choices in
  
a) date;;
b) ls;;
c) who;;
d) uptime;;
*) echo Invalid choice - Bye.;;   # For last case, (;;) is not necessary.

  esac
```

```bash
#!/bin/bash
NOW=$(date +"%a")       # +"%a" : date command special syntax. Don't put space after "+"
                        # help for more details
  case $NOW in
  
Mon)                    echo "Full backup";;
Tue|Wed|Thu|Fri)        echo "Partial backup";;
Sat|Sun)                echo "No backup";;
*)                      ;;

esac
```

## A Script for Checking Remote Servers Connectivity
1. __Ping one IP__
```bash
#!/bin/bash
host="192.168.56.101"
ping -c $host  &> /dev/null # -c1: ping one time
                            # &>: dump both output
if [ $? -eq 0 ]
then 
  echo $host OK
else
  echo $host is NOT OK
fi

ping 192.168.56.102     # Check if this ip is avaliable
```

2. __Ping Multiple IP__
vi myhost.txt
```
192.168.56.101    # Or 192.168.56.101 [space] 192.168.1.102
192.168.56.102    # Both works
```

```bash
#!/bin/bash
hosts=$(find ~/ -name "mthost.txt")

for ip in $(cat $hosts)     # No $ before ip
do
  ping -c $ip  &> /dev/null # -c1: ping one time
                            # &>: dump both output
  if [ $? -eq 0 ]
  then 
    echo $ip OK
  else
    echo $ip is NOT OK
  fi
done
```

## Aliases (alias)
```console
[alonzo@study ~]$ alias l="ls -al"
[alonzo@study ~]$ alias pl="pwd; ls"
[alonzo@study ~]$ alias dir="ls -l | grep ^d"            # ^: every thing starts with "d"
[alonzo@study ~]$ alias d="df -h | awk '{print \$6}'"    # $ means ouput variables in Linux
                                                         # However, we aren't outputing a var here.
                                                         # Hence, add backslash "/" in front of it.
[alonzo@study ~]$ alias                                  # Check all alias
[alonzo@study ~]$ unalias d                              # Remove alias
```

## User and Global Aliases
After logging out or closing the terminal, all alias will disappear.
* User alias : Applies to specific user. Modify `/home/user/.bashrc`
* Global alias : Applies to every user on the system. Modify `/etc/bashrc`. Logout or close the terminal to apply new setting.

## Shell History
* __Command :__ `history`
* This will list all the command since you logged in.
* __Rerun a comand :__ ![command number in history] ex. `!406`
* `history | grep awk` `history | grep chmod`
* __Sorurce file :__ /home/alonzo/.bash_history
* View other users' command history : login as root and checkout their .bash_history under their home directory.


## References
* [__BASH SHELL ALL__][0]
* [__正規表示法__][1]
* [__BASH comparison__][2]
* [__linux source, sh, bash, ./ difference__][3]
* [__set/env/export/source/exec__][4]
* [__BASH data type (declare/typeset)__][5]
* [__BASH SHELL basic__][6]

[0]: http://linux.vbird.org/linux_basic/0320bash.php#settings_bashrc 
[1]: http://linux.vbird.org/linux_basic/0330regularex.php
[2]: https://tldp.org/LDP/abs/html/comparison-ops.html
[3]: https://www.itread01.com/content/1548311242.html
[4]: https://www.itread01.com/content/1547145183.html
[5]: http://linux.vbird.org/linux_basic/0320bash.php#variable_var
[6]: http://linux.vbird.org/linux_basic_train/unit07.php
