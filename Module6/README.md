# Module **6.**  Shell Scripting

## What is a shell?
* __Funtionalities :__

```console
echo $0  => checkout what kinda shell u're using
cat /etc/shells => Shells that are installed in your system
cat /etc/passwd
```

## Types of Shells
* GUI: Execute command through mouse
  * Gnome
  * KDE
* sh
* bash
* csh and tcsh
* ksh

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

* __Othe if statements :__
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
