---
layout:     post
title:      RHCSA Study Guide 
date:       2021-11-25
summary:    Based on Red Hat Certified System Administrator (RHCSA), 3/e By Sander van Vugt
permalink: /rhcsa-study-guide/
toc: true
tags:
  - rhcsa
---

![RHCSA](https://richardbright.mehttps://richardbright.me/rhcsa.jpeg)


# Red Hat Certified System Administrator (RHCSA), 3/e
 by: Sander Van Vugt
 year: 2020


## Module 1: Performing Basic System Managment Tasks 

### Lesson 1: Installing RHEL Server

#### Learning Objectives

* 1.1 Understanding Server Requirements 
* 1.2 Performing a Basic Installation
* 1.3 Installing with Custom Partitions
* 1.4 Logging into the Server
* 1.5 Deploying RHEL in Cloud

**1.1 Understanding Server Requirements** 

* On physical server 
* In a virtual machine 
* As a container
* As an instance in cloud

Requirements depend on the type of installation

* Physical, cloud or virtual?
* With or without GUI
* What are you going to do with the server 
* For installation of a virtual machine in this class
  * 2 GiB of RAM
  * 20 GiB of disk space
  * Wired Network Connection
  * Optical Drive or access to DVD ISO


**1.3 Installing Custom Partitions** 

* Able to install LVM partitions during install process via GUI
* Use xfs file system  

**1.4 Logging into the server** 

* Use `su -`, to open root shell
  * `su -` ensures all the environment variables are loaded with the shell prompt 
  * `exit` will close out the current shell session 


### Lesson 2: Using Essential Tools 

#### Learning Objectives 

* 2.1 Getting started with Linux Commands
* 2.2 Working with the bash shell
* 2.3 Understanding I/O redirection and piping
* 2.4 Using I/O redirection and piping
* 2.5 Understanding the linux file system hierarchy
* 2.6 Using man
* 2.7 Finding the right man page
* 2.8 Understanding vim
* 2.9 Using vim
* 2.10 Using globbing and wildcards
* 2.11 Using Cockpit

**2.1 Getting started with Linux Commands**

`pwd` print working directory 

`whoami` shows current user name

`ls` list files and directories within the current directory 

Options/flags change the behavior of a command 

`ls -l` the option shows a long list of files and directories 

`ip addr show` three part command, shows current ip address 

`free -m` available memory, `-m` option shows in MiB

`df` disk free, shows storage devices 

`-h` for human readable format 

`cat` shows the contents of files 

`findmnt` shows mount points and devices 


**2.2 Working with the bash shell**

is the default shell interpreter

`tab command completion` - start typing a command and select tab to finish the command 

`history` shows previous commands run 
 
 `!1` will run the prior command 

`!f` will run the last command that started with an `f` - you can use any letter, the letter will correspond the the last command run beginning with the letter 

`!+r` reverse-1-search will search recent commands based on string

`piping |` uses the output from command as the input to the next command 

`wc` word count 

`redirection < >` sends output to another file 

`> or >>`single redirection will write over whereas a double redirect appends 

`2>` to redirect error messages 

common to redirect errors to `/dev/null` for example, `ls richard 2> * /dev/null` will write the error from the first command to the `/dev/null` directory (basically nowhere)

`env` environment variables shape the environment you are working in within the bash shell 

changing an environment variable will change the behavior of your bash shell

environment variables must be in all caps

assign environment variables by `HOME=/home/richard` 

`aliases` create custom names for commands 

create new `alias h=history`, but this is limited to the current runtime, you will need to set in config file to persist sessions

`scripts` add one to many commands in a file. for example, `script.sh`

**2.3 Understanding I/O redirection and piping**

![input output](https://richardbright.me/2.3-1.png)

`stdin` information coming in, keyboard, mouse 

`stdout` what gets posted after processing, most stdout will readout to the terminal 

`stderr` where the command will send any error messages 

![input output](https://richardbright.me/2.3-2.png)

**2.4 Using I/O redirection and piping**

Examples 

* `>` output redirection 
* `>>` output redirection
* `2> /dev/null` redirect `stderr`
* `<` redirect `stnin`

**2.5 Understanding the linux file system hierarchy**

FHS is maintained by the Linux Foundation 

`/` is the starting point in any FHS

![image](https://richardbright.me/2.5-1.png)

`cd` change directory

`cd ..` take me up one directory 

most important directories 

`/boot` used to boot the system

image below shows the content of `/boot`, the highlighted file is the linux kernel 

![image](https://richardbright.me/2.5-2.png)

`/dev` devices are interface files that allow you to connect hardware devices 

`/etc` etcetera directory is for configuration files 

`/home/` user account directories 

`usr` similar to program files on windows, where all the binaries are stored 

`/use/bin` ordinary binaries that can be used by normal users 

`/usr/sbin` system binaries, need to be the root user 

`var` variable directory, used to write dynamic data, such as log files 

`man hier` will open the FHS man page of the full Linux FHS - see below

![image](https://richardbright.me/2.5-3.png)

![image](https://richardbright.me/2.5-4.png)

![image](https://richardbright.me/2.5-5.png)

![image](https://richardbright.me/2.5-6.png)

![image](https://richardbright.me/2.5-7.png)

![image](https://richardbright.me/2.5-8.png)

![image](https://richardbright.me/2.5-9.png)

![image](https://richardbright.me/2.5-10.png)

![image](https://richardbright.me/2.5-11.png)

![image](https://richardbright.me/2.5-12.png)

![image](https://richardbright.me/2.5-13.png)

![image](https://richardbright.me/2.5-14.png)

![image](https://richardbright.me/2.5-15.png)

**2.6 Using man**

`man` best source to get extensive usage information 

Sections define command types 

* 1   Executable programs or shell commands
* 2   System calls (functions provided by the kernel)
* 3   Library calls (functions within program libraries)
* 4   Special files (usually found in /dev)
* 5   File formats and conventions eg /etc/passwd
* 6   Games
* 7   Miscellaneous (including macro packages and conventions), e.g. man(7), groff(7)
* 8   System administration commands (usually only for root)
* 9   Kernel routines [Non standard]

`shift + G` will bring you to the bottom of the page 

`/` to search for text

Synopsis is the summary of how to use the `man` page 
`[OPTION]` upper case means it needs to be substituted, `brackets []` means it's optional, `...` means it can be repeated ore than once

`man 8 ls` will take you straight to the system admin commands section of `man pages`


**2.7 Finding the right man page**

`man` pages are indexed in the `mandb` 

use `man -k` or `apropos` to search the mandb based on keywords

Built through a `cron` scheduled task - if nothing shows you need to trigger a manual rebuild 

`nothing appropriate` typically means you need to rebuild mandb

* Must be `root`
* see `man mandb`
* `mandb`


**2.8 Understanding vim**

`vim` is the default editor 

`vim` is the enhanced version of `vi`

![image](https://richardbright.me/2.8-1.png)


**2.9 Using vim**

`esc` takes you back to command mode

`i` insert 

`a` append

`o` open a new line

`:wq!` write and quit from command mode 

`:w!` write from command mode and stay in the file

`:q!` quit from command mode without saving

`2 + dd` deletes 2 lines 

`yy` copies current line, or `2yy` copies current and lower line 

`p` will paste copied lines 

`d$` will delete until the end of the line 

`v` visual mode, can use `gg` to highlight from the top of the document

`u` undo 

`r` redo 

`G` takes you to the end of the document 

`/text` search from current text down

`?text` search from current text going up 

`^` bring cursor to the beginning of current line 

`$` bring cursor to the end of current line 

`:%s/old/new/g` global substitute / old text / new text / global 



**2.10 Using Globbing and Wildcards**

is a shell feature that helps matching filenames 

not to be confused with regular expressions 

`ls host*` search for any file that starts with host 

`ls ?ost` searches for any file that matches the patter after the first letter 

`ls [hm]ost` matches either h or m for the leading letter 

`ls script[0-9[0-9]` show all files that match name and any trailing two digit number 

`touch filename{0..10}` will create ten files as `filename0`, `filename1`, etc...


**2.11 Using Cockpit**

`yum install -y cockpit cockpit-networkmanager cockpit-dashboard cockpit-storaged cockpit-packagekit`

`systemctl enable --now cockpit.socket`

 `systemctl start cockpit`

 `systemctl status cockpit`
 
 `firewall-cmd --permanent --add-service=cockpit`
 
 `firewall-cmd --reload`

go to https://hostname:9090 to access the UI


### Lesson 3: Essential File Management Tools

#### Learning objectives

* 3.1 Essential file management tasks 
* 3.2 Finding files
* 3.3 Understanding mounts
* 3.4 Understanding links
* 3.5 Working with links 
* 3.6 Working with tar
* 3.7 Working with compressed files 


**3.1 Essential file management tasks** 

`ls` list files 

`mkdir` create a directory 

`-p` will create full directory path 

`cp` copy command 

`-r` recursive 

`mv` move command 

`rmdir` remove empty directory 

`rm` can remove all directories 


**3.2 Finding Files**

`which` looks for binaries in $PATH

`locate` uses a database, built by `updatedb` to find file sin a database 

`find` most flexible tool 

`find / -type -f -size +100M` searches for files from root that are 100M or greater

`find / -user student` find all files owned by student from /

`find /etc -exec grep -l student {} \;` will execute the grep command on all files in the /etc directory 

can also redirect error messages to `2>/dev/null`

`-exec` allows you to run almost any command based on the results of the `find` command 

**3.3 Understanding Mounts**

to access a devices, it must be connected to a directory 

in Linux, there are multiple mounts used 

different types of data are on different devices 


**3.4 Understanding Links**

a link is a pointer to a fil ein another location 

similar to a shortcut on a Windows machine 

Two types of links, hard `ln` and symbolic `ln -s`

every file on linux is it's own `inode`

every `inode` has a number 

`inode` are kept in the `inode table` and every file system has it's own table 

each `inode` has a hard link counter 

a symbolic link links to a hard link and not the `inode` 

a symbolic link can lunk across devices and directories, hard links cannot 

![image](https://richardbright.me/3.4-1.png)


**3.5 Working with Links** 

`ls -il` lists files with the unique `inode` number 


**3.6 Working with tar**

`tar` is a tape archive 

by default, it does not compress data 

basic use, compress, extract, or list 

`tar -cvf my_archive.tar /home/etc` will create an archive of /home/etc

`tar -tvf` will show contents of an archive 

`tar -xvf my_archive` extracts to the current directory 

`-C` will redirect output to another directory 

to add compression use `-z -j -J` 

`file` will tell you the kind of file 

**3.7 Working with Compressed Files**

`gzip` most common used compression 

`bzip2` is an alternative utility 

`zip` is also available and has Windows-compatible syntax

`xz` 

### Lesson 4: Working with text files

#### Learning objectives

* 4.1 Using common text tools
* 4.2 Using grep
* 4.3 Understanding regular expressions 
* 4.4 Using awk
* 4.5 Using sed


**4.1 Using common text tools**

Linux is all about working with text files 

`more` original file pager 

`less` more advanced features 

to do more, use `less` 

`head` to show first 10 lines of a text file

`tail` to show last 10 lines of a text file 

`-n nn` to specify another number of lines 

`tail -f` refresh, to view changes to a file 

`cat` dumps text file contents on the screen

>options
>* `-A` shows all non-printable characters
>* `-b` number of lines 
>* `-s` suppresses repeated empty lines 

`tac` is doing the same, but is reversed order 

`cut` filter output

`sort` sort output

`tr` translates output 

>options
>* tr [:lower:] [:upper:], will change text from lower to all upper case 


 **4.2 Using grep**

stands for `general regular expression parser`

`grep` is an excellent utility to find text in files or in and output

`ps aux | grep ssh` will search for and filter for all ssh processes 

`grep linda *` search for files with the name linda in the current directory 

`grep -i linda *` the `-i` flag will ensure it's case insensitive  

`grep -A5 linda /etc/passwd` print files after, use `-B5` to print before

`grep -R root /etc` the `-R` is recursive

**4.3 Understanding Regular Expressions**

a regular expression is a text pattern that are used by tools like `grep` and others

Don't confuse regular expressions with globbing 

Globbing only applies to file names whereas regular expressions looks at files and the contents within a file 

`grep 'a*` is a regular expression 

good idea to put the regular expression search pattern between single quotes to ensure the shell will not interpret the regular expression 

`grep a*` is globbing and searching for files starting with an 'a'

regular expressions are not generic and must be used with tools such as `grep, vim, awk, and sed`

Extended regular expressions enhance basic regex features 

Regular expressions are built around `atoms`l an atom specifies what text is to be matched

* Atoms can be single characters, a range of characters, or a dot
* Atoms can also be a class, such as [[:alpha:]], [[:upper:]], [[:digit:]] or [[:alnum:]]

Second is the repetition operator, specifying how often a character occurs 

The third element is indicating where to find the next character 

`^` beginning of the line

`$` end of line

`\<` beginning of word

`\>` end of word

`*` zero or more times 

`+` one or more times 

`?` zero or one time 

`{n}` exactly n times 

**4.4 Using awk** 

`awk` is a powerful text processing utility that is specialized in data extraction and reporting 

It can perform actions based on selectors 

`awk -F : '/linda/ {print $4}' /etc/passwd`

` awk -F : '{print $NF}' /etc/passwd` print number of fields 

`ls -al /etc | awk '/pass/ { print }'` 


**4.5 Using sed** 

stream editor 

is the old version of vi

`sed` is a stream editor, used to search and transform text

It can be used to search for text, and perform an operation on matching text 

>options
>* `-i` will write directly to the file 
>* `-n` number 
>* `-e` passinf an edit command to set, `2d`, line two, delete 



### Lesson 5: Connecting to a rhel server

#### Learning objectives

* 5.1 Understanding the root user
* 5.2 Logging in to the GUI
* 5.3 Logging in to the console
* 5.4 Understanding virtual terminals
* 5.5 Switching between virtual terminals 
* 5.6 Using `su` to work as another user
* 5.7 Using `sudo` to perform administrator tasks
* 5.8 Using `ssh` to log in remotely


**5.1 Understanding the root user**


![image](https://richardbright.me/5.1-1.png)

`root` users have unlimited access to the hardware, it's an unrestricted account, different than an admin on a windoes machine

user space, above the lines, where users run processes and make system calls 

root user, or below the line, where only root has unrestricted access to the machine resources


**5.2 Logging in to the GUI**

no notes

**5.3 Logging in to the console** 

95% of all linux servers do not have a GUI

**5.4 Understanding Virtual Terminals**

`tty` another term for a terminal 

terminals were connected to `/dev/tty*`

![image](https://richardbright.me/5.4-1.png)

**5.5 Switching Between Virtual Terminals**

tty1-tty6 are available for login

If installed and active, the GUI is on tty1

Use `chvt` to switch between terminals 

for example, use `chvt 4` to access the tty4 terminal 

**5.6 Using su to Work as Another User**

`su` switch user

always use `su -` when switching accounts so you open all the environment variables 

**5.7 Using sudo to Perform Administrator Tasks**

`sudo` used to run tasks as the root user

prompts for the password of the current user 

must be prompted for users password 

authorization through `/etc/sudoers` and `/etc/sudoers.d/*` 

do not edit directly, use `visudo` to edit the file 

you can configure the `sudoers` file to grant access to run all commands to one to many commands

`%wheel      ALL=(ALL)      ALL` anyone in the wheel group can run all commands with a password

`%wheel      ALL=(ALL)      NOPASSWD: ALL` anyone in the wheel group can run all commands without being prompted for a password 

`student     ALL=/user/sbin/useradd, ALL=/user/sbin/passwd` allows user student to create users and change passwords 

`id` to see a users groups 

`usermod -aG wheel student` to add user to the wheel group 


**5.8 Using ssh to Log in Remotely**

`ssh` secure shell is used to establish a secured connection 

after initial connection, host key is stored in `~/.ssh/known_hosts`

`ssh -X or ssh -Y` to display graphical screen locally

### Lesson 6: Managing users and groups

#### Learning objectives

* 6.1 Understanding the need for user accounts
* 6.2 Understanding user properties 
* 6.3 Creating and managing users
* 6.4 Managing users default settings 
* 6.5 Understanding `/etc/passwd` and `/etc/shadow`
* 6.6 Understanding group membership
* 6.7 Creating and managing groups
* 6.8 Managing password properties 

**6.1 Understanding the need for user accounts**

A user is a security principle, user accounts are used to provide people or processes access to system resources 

processes are using system accounts, typically have a low user id

people are using regular accounts, usually a high user id

**6.2 Understanding User Properties**

looking at `/etc/passwd`

`student:x:1000:1000:student:/home/student:/bin/bash`

`student` is the user account name

`x` is the password as a placeholder 

`1000` uid, unique identifier for users 

`1000` gid, primary group identifier for groups (all users must be in at least one group)

`student` gecos, additional non-mandatory information about the user 

`/home/student` home directory 

`/bin/bash` default shell 

**6.3 Creating and Managing Users**

`useradd` allows you to create user accounts 

>options
>* `-m` create home directory 

`usermod` used to modify user account

>options
>* `-aG` append group to a user 

`userdel` used to delete user accounts 

>options
>* `-f` will force the removal of the user
>* `-r` remove home directory 

`passwd` used to set usr passwords 

>options 
>* `-l` locks a users account 
>* `-u` to unlock 


**6.4 Managing User Default Settings**

`useradd -D` to specify default settings 

files in `/etc/default/useradd` apply to `useradd` only 

write default settings to `/etc/login.defs` 

>**this is an important configuration file for the exam**

`/etc/skel` copies files to the users home directory upon creation 

`.bashrc` configuration file that creates the users shell session 

`.bash_history` logs history of shell commands the user ran

`.bash_profile` configuration file that is processes when the user logs in 

**6.5 Understanding /etc/passwd and /etc/shadow**

`/etc/passwd` used to store main user properties 

linux does not care about user ids, it's based on UIDs 

>user accounts
>* `root` has user id 0
>* `bin` is a system account
>* `sync` system account that can run one account only 
>* `nobody` user with no permissions 

`/etc/shadow` stored the password properties 

stores the hash of the encrypted password 

`student` user account

`long string` is the hash of the user password

`17912` is the number of days since  Jan 1, 1970 - this day represents the begining of Linux and all time is calculated since that date 

`0` min amount of days the password must be used 

`999999` max number of days the password must be used

`7` is the number of days the user will get a warning until their password expires 

`!!` in the password field means the password is disabled 

`!` in the beginning of the password field means the user account is locked 

`/etc/group` used ofr group policies 

`root:x:0:student`

`root` group

`x` group password placeholder

`0` group id

`student` name of user account who are secondary members of the group 

**6.6 Understanding group membership**

every user must be a member of at least one group

Primary group membership is managed through `/etc/passwd`

The user primary group becomes group-owner if a user creates a file 

Additional secondary groups can be defined as well 

Additional secondary group membership is managed through `/etc/groups` 

`id` will show what groups a user if a member of 

**6.7 Creating and Managing Groups**

`groupadd` used to add groups 

`groupdel` delete a group 

`groupmod` modify a group 

`lid` kist all users tha are member of a group 

`lig -g wheel` show all who are a member fo group wheel 

**6.8 Managing Password Properties** 

`/etc/logins.defs` hold basic password requirements 

`pam` pluggable authentication modules, for advanced password properties 

look for the pam_tally2 module 

`chage` manage password aging 

`passwd` for managing password properties 

`chage` writes to the `/etc/shadows` file

### Lesson 7: Managing permissions

#### Learning objectives

* 7.1 Understanding ownership
* 7.2 Changing file ownership
* 7.3 Understanding basic permissions 
* 7.4 Managing basic permissions 
* 7.5 Understanding `umask`
* 7.6 Understanding special permissions 
* 7.7 Managing special permissions 
* 7.8 Understanding acls
* 7.9 Troubleshooting permissions 

**7.1 Understanding ownership**

To determine which permissions a user has, linux uses ownership 

Every file has a user-owner, a group-owner, and the others entity that is also granted permissions (ugo) user, group, other -- also know as global permissions 

Linux permissions are not additive, if you're the owner, permissions are applied and that's all 

Linux first checks user permissions, then group permissions, then defaults to ugo if not in the first two 

`ls -al` to see file and directory ownership settings 

**7.2 Changing file ownership**

`chown user:group` to change ownership 

`chgrp group file` to update group permission setting 

**7.3 Understanding basic permissions**

![image](https://richardbright.me/7.3-1.png)


**7.4 Managing Basic Permissions**

`chmod` change mode is used to manage permissions 

It can be used in absolute or relative mode

`chmod 750 file` to apply rwxr-x--x

`chmod +x file` to add execute

**7.5 Understanding umask**

`umank` is a shell setting that subtracts the `umask` from the default permissions 

Default file permissions are `666`

Default directory permissions are `777`

`umask 027` will result in a default permission setting on 640 for files and 750 for directories 

`/etc/profiles` is where `umask` is set 

can also directly edit `~/.bash_profile` 


**7.6 Understanding Special Permissions**

![image](https://richardbright.me/7.6-1.png)


**7.7 Managing special permissions**

SUID - allows you to run files as the file owner rwsrwx---

`chmod 4770 file` 

`chmod u+s file` 

SGUI - allows anyone in the same group to manage and updates all files within a directory rwxrws---

`chmod 2770 dir`

`chmod g+s dir`

`lid` displays user's group 


Sticky bit - prevents a user with rwx from deleting a file rwxrwx---T 

`chmod 1770 dir`

`chmod =t dir` 

**7.8 Understanding ACLs**

ACLs are used to grant permissions to additional users and groups 

Normal ACLs apply to existing files only 

Us e default ACL on a directory if you want to apply to a new file 

`getfacl` gets settings 

`setfacl -R -m g:somegroup:rx /data/groups` 

`setfacl -m d:g:somegroup:rx /data/groups` 


**7.9 Managing ACLs**

**7.10 Troubleshooting Permissions**

write permission at the directory level will allow you to delete files within the directory, even if you are not the file owner or group owner 

`vim` will delete and create a new file when you open a file that you are not the owner or in the group but have directory permissions 



