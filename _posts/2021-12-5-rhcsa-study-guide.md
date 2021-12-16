---
layout:     post
title:      RHCSA Study Guide 
date:       2021-12-5
summary:    Based on Red Hat Certified System Administrator (RHCSA), 3/e By Sander van Vugt
permalink: /rhcsa-study-guide/
toc: true
tags:
  - rhcsa
---

>I decided to take the RHCSA within a few months and decided to prep using Sander's video and books that are available via an [O'Reilly Subscription](https://www.oreilly.com/). I'll provide more details as I go, and how I did on the exam. 


![RHCSA](https://richardbright.me/images/rhcsa.jpeg)

# Red Hat Certified System Administrator (RHCSA), 3/e
 by: Sander Van Vugt
 year: 2020

# Module Table of Contents 
1. [Module 1: Performing Basic System Management Tasks](#module1)
2. [Module 2: Operating running systems](#module2)

## Module1

### Performing Basic System Management Tasks 

# Lesson Table of Contents 
1. [Lesson 1: Installing RHEL Server](#lesson1)
2. [Lesson 2: Using Essential Tools](#lesson2)
3. [Lesson 3: Essential File Management Tools](#lesson3)
4. [Lesson 4: Working with text files](#lesson4)
5. [Lesson 5: Connecting to a rhel server](#lesson5)
6. [Lesson 6: Managing users and groups](#lesson6)
7. [Lesson 7: Managing permissions](#lesson7)
8. [Lesson 8: Configuring networks](#lesson8)
9. [Lesson 9: Managing Processes](#lesson9)
10. [Lesson 10: Managing Software](#lesson10)
11. [Lesson 11: Working with systemd](#lesson11)
12. [Lesson 12: Scheduling Tasks](#lesson12)
13. [Lesson 13: Configuring logging](#lesson13)
13. [Lesson 14: Managing storage](#lesson14)

## Lesson1 

### Installing RHEL Server

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

## Lesson2

### Using Essential Tools

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

![input output](https://richardbright.me/images/2.3-1.png)

`stdin` information coming in, keyboard, mouse 

`stdout` what gets posted after processing, most stdout will readout to the terminal 

`stderr` where the command will send any error messages 

![input output](https://richardbright.me/images/2.3-2.png)

**2.4 Using I/O redirection and piping**

Examples 

* `>` output redirection 
* `>>` output redirection
* `2> /dev/null` redirect `stderr`
* `<` redirect `stnin`

**2.5 Understanding the linux file system hierarchy**

FHS is maintained by the Linux Foundation 

`/` is the starting point in any FHS

![image](https://richardbright.me/images/2.5-1.png)

`cd` change directory

`cd ..` take me up one directory 

most important directories 

`/boot` used to boot the system

image below shows the content of `/boot`, the highlighted file is the linux kernel 

![image](https://richardbright.me/images/2.5-2.png)

`/dev` devices are interface files that allow you to connect hardware devices 

`/etc` etcetera directory is for configuration files 

`/home/` user account directories 

`usr` similar to program files on windows, where all the binaries are stored 

`/use/bin` ordinary binaries that can be used by normal users 

`/usr/sbin` system binaries, need to be the root user 

`var` variable directory, used to write dynamic data, such as log files 

`man hier` will open the FHS man page of the full Linux FHS - see below

![image](https://richardbright.me/images/2.5-3.png)

![image](https://richardbright.me/images/2.5-4.png)

![image](https://richardbright.me/images/2.5-5.png)

![image](https://richardbright.me/images/2.5-6.png)

![image](https://richardbright.me/images/2.5-7.png)

![image](https://richardbright.me/images/2.5-8.png)

![image](https://richardbright.me/images/2.5-9.png)

![image](https://richardbright.me/images/2.5-10.png)

![image](https://richardbright.me/images/2.5-11.png)

![image](https://richardbright.me/images/2.5-12.png)

![image](https://richardbright.me/images/2.5-13.png)

![image](https://richardbright.me/images/2.5-14.png)

![image](https://richardbright.me/images/2.5-15.png)

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

use `man -k` or `apropos` to search the `mandb` based on keywords

Built through a `cron` scheduled task - if nothing shows you need to trigger a manual rebuild 

`nothing appropriate` typically means you need to rebuild `mandb`

* Must be `root`
* see `man mandb`
* `mandb`


**2.8 Understanding vim**

`vim` is the default editor 

`vim` is the enhanced version of `vi`

![image](https://richardbright.me/images/2.8-1.png)


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


## Lesson3 

### Essential File Management Tools

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

![image](https://richardbright.me/images/3.4-1.png)


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

## Lesson4

### Working with text files

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
>* `-e` passing an edit command to set, `2d`, line two, delete 



## Lesson5 

### Connecting to a rhel server

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


![image](https://richardbright.me/images/5.1-1.png)

`root` users have unlimited access to the hardware, it's an unrestricted account, different than an admin on a windows machine

user space, above the lines, where users run processes and make system calls 

root user, or below the line, where only root has unrestricted access to the machine resources


**5.2 Logging in to the GUI**

no notes

**5.3 Logging in to the console** 

95% of all linux servers do not have a GUI

**5.4 Understanding Virtual Terminals**

`tty` another term for a terminal 

terminals were connected to `/dev/tty*`

![image](https://richardbright.me/images/5.4-1.png)

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

## Lesson6 

### Managing users and groups

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

## Lesson7 

### Managing permissions

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

![image](https://richardbright.me/images/7.3-1.png)


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

![image](https://richardbright.me/images/7.6-1.png)


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

`chmod +t dir` 

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

## Lesson8 

### Configuring networks

#### Learning objectives

* 8.1 Understanding piv4 networking
* 8.2 Understanding NIC naming 
* 8.3 Managing runtime configuration with `ip`
* 8.4 Understanding rhel 8 networking
* 8.5 Managing persistent networking with `nmcli`
* 8.6 Managing persistent networking with `nmtui`
* 8.7 Verifying network configuration files 
* 8.8 Testing network connections 

**8.1 Understanding ipv4 networking** 

In ipv4, each node needs its own ip address, written in dotted decimal notation (192.168.4.200/24)

Each ip address must be indicated with the subnet mask behind it 

The default router or gateway specifies which server to forward packers that have an external destination 

The DNS `nameserver` is the ip address of a server that helps resolving to ip addresses and the other way around

ipv4 is still the most common ip version, but ipv6 addresses can be used as well

ipv6 addresses are written in hexadecimal notation (fd01::8eba:210)

ipv4 and ipv6 can co-exist on the same network interface 

![image](https://richardbright.me/images/8.1-1.png)

`192.168.2.200/24` the network is expresses as `192.168.4` and the node as `.200/24`

**8.2 Understanding NIC naming**

ip address configuration needs to be connected to a specific network device  

use `ip link show` to see current devices, and `ip addr show` to check their configuration

every system has an `lo` (loopback) device, which is for internal networking 

apart from that, you'll see the name of the real network device in the BIOS name

classical naming is using device names like `eth0`, `eth1`, etc...

these device names don't reveal any information about the physical device location 

BIOS naming is based on hardware properties to give more specific information in the device name 

* `em[1-N]` for embedded NICs
* `eno[nn]` for embedded NICs
* `p<slot>p<port>` for NICs on the PCI bus

if the driver doesn't reveal network device properties, classical naming is used


**8.3 Managing Runtime Configuration with ip** 

runtime configurations are not persisted across reboots 

the `ip` tool can be used to manage all aspects on ip networking

the only thing it can do is persist across reboots

it replaces the legacy `ifconfig` tool, no **NOT** use `ifconfig` anymore

use `ip addr` to manage address properties 

use `ip link` to show link properties 

use`ip route` to manage rout properties 

`rx` stands for received packets

`tx` stands for transmitted packets

ipv6 inet6 addresses that start with `fe80` means it's a private ipv6 and you cannot get out to the internet 

`scope global` means the ip address is linked to the internet 

`scope link` means the ip is local only, you cannot go out to the internet 

`dynamic` means the ip was assigned via a dhcp server

`valid_lft` valid lifetime is the valid length of time the dhcp server will assign the ip

`ip addr add dev ens33 10.0.0.10/24` to add an ip address 

adding a secondary ip is helpful for container environments, or cloud hosts 

`ifconfig` cannot show secondary ip addresses 

`ip route show` will show the current route table 

`ip route del default via 192.168.4.2` will delete the specified route 

`ip add default via 192.168.4.2` will reset default route 

dns configuration is set in `/etc/resolve.conf`


>options
>* `-s` to show statistics, e.g., `ip -s link show` 

**8.4 Understanding RHEL 8 Networking**

rhel stores the nic configurations in `/etc/sysconfig/networkscripts/ifcfg-ens33`

`nmcli` (network comandline utility) and `nmtui` (network manager text user interface) to interact with the rhel network manager 

![image](https://richardbright.me/images/8.4-1.png)


**8.5 Managing Persistent Networking with nmcli**

an `nmcli` connection is a configuration that is added to a network device 

conections are stored in confoguration files 

the network manager service must be running to manage these files 

ensure the bash-completion RPM package is installed when working with `nmcli`

`man nmcli-examples` to see example configurations 

`nmcli con add con-name my-con-em1- iframe em1 type ethernet \`
`ip4 192,168.100.100/24 gw4 192.168.100.1 ip4 1.2.3.4 ip6 abbe::cafe`

`con` connection

`add` add a connection 

`con-name` connection name 

a connection is just a configuraton file 

`ifname` is the name of the nic you want to configure 

`type ethernet` is the type of nic 

`gw4` gateway 

`ip4 1.2.3.4` secondary ip address 

`ip6 abbe::cafe` apply a ipv6 address as well

**8.6 Managing Persistent Networking with nmtui**

DOS lik einterface, good for use on servers without a GUI

able to set hostname 

**8.7 Verifying Network Configuration Files**

`/etc/sysconfig/network-scripts` is where the network configuration files are stored 

network config files will follow the following naming convention `ifcfg-device`. for example,  `ifcfg-ens33`, `ifcfg-ethernet-ens33` 

>Important parameters
>* `BOOTPROTO` boot protocol, scan set it to dhcp
>* `IDADDR`
>* `GATEWAY`
>* `DNS1|2`
>* `ONBOOT` 

you will need to reload the configuration if any chages are made, run `nmcli connection up ens33` will bring connectivity up on device ens33

**8.8 Testing Network Connections** 

`ping` used to test network connectivity 

`ctl+c` to stop 

>options
>* `-c 1` to send one ping
>* `-f` flood 

`ip addr show`

`ip route show`

`dig` can test DNS nameserver working 

`NXDOMAIN` the service does not exist 

troubleshooting 

`ping google.com` 

if you get a `Name or service not known error`, this is a DNS error 

then see if you can ping the DNS server

`ping 8.8.8.8` 

if you get `Network unreachable`, then there is a connectivity problem

look at your ip address and the routing 

if the server was previously had conenctivity, look in the network config file 

`/etc/sysconfig/network-scripts/ifcfg-ens33` 

check the `GATEWAY`

## Module2

## Lesson9 

### Managing Processes

#### Learning objectives

* 9.1 Understanding jobs and processes
* 9.2 Managing shell jobs
* 9.3 Getting process information with `ps`
* 9.4 Understanding memory usage 
* 9.5 Understanding cpu load
* 9.6 Monitoring system activity with `top`
* 9.7 Sending signals to processes
* 9.8 Managing priorities and niceness 
* 9.9 Using tuned profiles 


**9.1 Understanding jobs and processes** 

all tasks are started as processes 

processes have a PID

common process management tasks include scheduling priority and sending signals 

some processes are starting multi threads, individual threads cannot be managed 

tasks can be started in the foreground and background

**9.2 Managing Shell Jobs**

use `command &` to start a job in the background 

you can move a job to the background

  * first stop the job using `ctrl+z`
  * Then type `bg` to move it to the background 

 `jobs` to view all running jobs 

  `fg [n]` to move the last job back to the foreground 

`ctrl+c` will stop a job in the foreground 

`dd if=/dev/zero of=/dev/null &` infinite loop

`while true; do true; done` infinite loop 

**9.3 Getting Process Information with ps** 

`ps` process 

`ps` command has two different dialects, BSD and SystemV

therefore `ps -L` and `ps -L` are different commands 

`ps` shows an overview of current processes 

`ps aux` for an overview of all processes 

`ps -fax` shows hierarchical relations between processes 

`ps -fU student` shows all processes owned by student 

`ps -f --forest -C sshd` shows a process tree for a specific process

`ps L` shows format specifiers 

`ps -eo pid,ppid,user,cmd` uses some of these specifiers to show a list of processes 

pid 1 should always belong to systemd

pids between `[]` are kernel processes 

`ps aux` will list processes in order, so the latest processes will be at the end of the document 

**9.4 Understanding Memory Usage**

linux places as many files as possible in cache to guarantee fast access to the files 

for that reason, linux memory often shows as saturated 

`swap` is used as an overflow buffer of emulated RAM on disk 

the inux kernel moves inactive memory to swap first 

`free -m` to get details about current memory 

**9.5 Understanding CPU Load** 

![image](https://richardbright.me/images/9.5-1.png)

`uptime` how long system is up and the load average 

load average is by last `[1 minute], [5 minutes], [15 minutes]`

load average is the average amount of runnable processes, which are processes waiting in the runqueue or processes currently running 

use `watch` to monitor command over time, it refreshes every two seconds 

`lscpu` to check cpu specs to help determine load averages 

**9.6 Monitoring System Activity with top**

`top` is a dashboard that allows you to monitor current system activity 

`line 1:` `uptime` command shows how long the system has been up and the load averages 

`line 2:` `Tasks` how many tasks running on system, `running` how many tasks actively running, `sleeping` how many tasks are sleeping and was not processes by the cpu on the prior cycle, `stopped` tasks that were stopped by `ctrl+z`, `zombie` tasks that can no longer communicate with the parent process 

`line 3:` `CPUs` shows what all the system cpus are doing, `us` load for processes running in the user space, `sy` load for processes running in system space, kernel processes, `ni` processes that have been changes by the  `nice` command, `id` percentage of time the cpu is doing nothing, `wa` waiting for IO, `hi` hardware interrupt, amount of time the system is waiting for a hardware device, `si` software interrupt, `st` stolen time 

`line 4:` displays `free -m` results 

you can display and manage your system using `top`

>option keys
> * `f` to show and select from available display fields
> * `M` to filter on memory
> * `W` to save new display settings 
> * `1` to see cpu load for each cpu
> * `k` to send signal to a process 
> * `r` renice a process 

**9.7 Sending Signals to Processes**

a signal allows the operating system to interupt a process from software and ask it to do something 

interputs are comparable to signals but are generated from hardware

a limited amount of signals can be used and is documented in `man 7` signals 

not all signals work in all cases 

the `kill` command is used to send signals to the PIDs 

you can alo use `k` in `top`

different kill-like commands exits, like `pkill` and `killall` 

`man 7 signal` will give you an overview of signals 

`kill -15 pid` will appropriately shutdown process 

`kill -9 pid` will force kill the process 

signal 9 will just stop everything, avoid using it 

`pkill` you can use patterns 

`killall` can kill processes based on a name 

**9.8 Managing Priorities and Niceness**

by default, all processes are started with the same priority 

in kernel-land, real-time processes can be started, which will always be handeled with highest priority 

to change priorities of non-realtime processes, the `nice` and `renice` commands can be used

nice values range from -20 to 19

the higher the number the nicer the process with exist with other processes, meaning it will use less cpu time

negative numbers will use more cpu time 

uers can set their processes to a lower priority level, meaning set their nice level to a higher number. users cannot lower their priority without root access 

`nice --help | less` to see help information 

`renice --help` to see help information 


**9.9 Using tuned profiles**

`tuned` is a service that allows for performance optimization in an easy way

different profiles are provided to match specific server workloads

to use them, ensure the `tuned` service is enabled and started 

`tuned-adm list` will show a list of profiles 

`tuned-adm profile [name]` will set a profile 

`tuned-adm active` will show the current profile 

## Lesson10

### Managing Software

#### Learning objectives

* 10.1 Understanding RPM packages
* 10.2 Setting up repository access
* 10.3 Understanding modules and application streams 
* 10.4 Managing packages with `yum`
* 10.5 Managing modules and application streams 
* 10.6 Using `yum` groups
* 10.7 Managing `yum updates` and `yum history` 
* 10.8 Using RPM
* 10.9 Using red had subscription manager 


**10.1 Understanding RPM packages** 

`RPM` red hat package manager, is a package format to install software, as well as a database of installed packages on the system 

the package contains an archive of files that is compressed with `cpio`, as well ad metadata and a list of package dependencies 

RPM packages may contain scripts as well

to install, repositories are used 

individual packages may be installed, but this should be avoided 

**10.2 Setting up Repository Access**

create a local repository so that we can install packages from the rhel 8 installation disk 

create an iso image: `dd if=/dev/sr0 of=/rhel8.iso bs=1M` 

create directory `/repo`

exit the `/etc/fsab` 

add `/rhel8.iso    /repo    iso9660   defaults   0 0`

run `systemctl daemon-reload` 

then `mount -a` 


create the file `/etc/yum.repos.d/appstream.repo` with the following content 

`[appsream]`
`name=appstream`
`baseurl=file:///repo/AppStream`
`gpgcheck=0`

you also need to create a baseos repo 

`[BaseOS]`
`name=BaseOS`
`baseurl=file:///repo/BaseOS`
`gpgcheck=0`


**10.3 Understanding Modules and Application Streams**

rhel 8 introduces application streams and modules 

application streams are used to separate user space packages from core kernel operations 

using application streams allows for working with different versions of packages

base packages are provided through the baseOS repository

AppSteam is provided as a separate repository 

application streams are delivered in two formats, 1) traditional rpms and 2) new modules

modules can contain streams to make multiple versions application available

enabling a module stream gives access to rpm packages in that stream 

modules can also have profiles, a profile is a list of packages that belong to a specific use-case

the package list of a module can contain packages outside the module stream

use the `yum module` commands to manage modules 

**10.4 Managing Packages with yum**

`yum` wa created to be intuitive 

`yum search` 

`yum install`

`yum remove` 

`yum update` 

`yum provides` goes beyond what `yum search` can provide 

`yum info`

`yum list`

**10.5 Managing Modules and Application Streams**

`yum module list` to view all available modules 

`yum module provides httpd` searches the module that provides a specific package 

`yum module info php`

`yum module info --profile php` shows profiles 

`yum module list php` shows which streams are available 

`yum module install php:7.1` or `yum install @php:7.1` will install and enable a specififc module stream 

`yum module install php:7.1/devel` installs a specific profile 

`yum install httpd` will have yum automatically enable the module stream this package is in before installing this package 

`yum module enable php:7.1` enables the module but doesn't install anything yet 

`yum distro-sync` will update or downgrade packages from a previous module stream 

**10.6 Using yum Groups** 

`yum` groups are provided to give access to specific categories of software 

`yum groups list` gives a list of the most common yum groups

`yum groups list hidden` shows all yum groups

`yum groups info [groupname]` shows which packages are in a group 

`yum groups install [groupname]`   will install a specific yum group 

`yum groups install --with-optional "Directory Client"` will install the default and optional packages in a group

**10.7 Managing yum updates and yum history**

`yum history` gives a list of recently issues commands 

`yum history undo` allows you to undo a specific command, based on the history information 

`yum update` will update all packages on your system

`yum update [packagename]` will update one package only, including it's dependencies 

`yum history undo id#` will undo a prior command 

**10.8 Using RPM Queries**

`rpm` is the legacy command to manage packages 

`rpm` does not install package dependencies very well 

good source of information about packages that have already been installed on the system 

`rpm` is useful for package queries 

`rpm` queries by default are against the database of installed packages, add `-p` to query package files 

`rpm -qf /any/file` 

`rpm -ql mypackage` 

`rpm -qc my package`

`rpm -qp --scripts mypackage-file.rpm` 

**10.9 Using Red Hat Subscription Manager**

to work with rhel repos you need a subscription 

`subscription-manager register` will register yur system 

`subscription-manager attach --auto` to connect your subscription 

does not work on CentOS 


## Lesson11

### Working with systemd

#### Learning objectives

* 11.1 Understanding systemd
* 11.2 Managing systemd services
* 11.3 Modifying systemd service configuration 

**11.1 Understanding systemd** 

Systemd is the manager of everything after the start of the linux kernel 

Managed items ae called units 

Different unit types are available 

* services
* mounts
* timers
* ...

`systemctl` is the management interface to work with systemd

Managing services is the most important systemd related task for an admin 

`systemctl -t help` to see the types of available units of the system 

`systemctl list-unit-files` to see a list of services and their status 

`systemctl list-units` to see a list of units 


**11.2 Managing Systemd Services** 

system admins must be able to manage the state of modules

disabled / enabled determine if a module should be automatically started while booting 

start / stop is managing runtime state of a service 

`systemctl status|start|stop|reload [service]` to manage service runtime 

`systemctl enable [service]` will automatically start when the system restarts - a symbolic link is created in `/etc/systemd/*` 


**11.3 Modifying Systemd Service Configuration**

default system-provided systemd unit files ar ein `/usr/lib/systemd/system`

custom unit files are in `/etc/systemd/system` 

run-time automatically generated unit files are in `/run/systemd`

while modifying a unit file, do not edit the file in `/usr/lib/systemd/system` but create a custom file in `/etc/systemd/system` that is used as an overlay file 

better, use `systemctl edit unit.service` to edit unit files 

use `systemctl show [service]` to show available parameters 

using `systemcl-reload` may be required 

`systemctl cat httpd` will show the contents of the unit file 

`systemctl edit [service]` to create an over ride file 

`systemctl daemon-reload` will read new settings into the runtime 

## Lesson12 

### Scheduling Tasks

#### Learning objectives

* 12.1 Understanding `cron` and `at`
* 12.2 Understanding `cron` scheduling options 
* 12.3 Understanding `anacron` 
* 12.4 Scheduling with `cron` 
* 12.5 Scheduling tasks with systemd timers 
* 12.6 Using `at`
* 12.7 Managing temporary files 


**12.1 Understanding `cron` and `at`**

`cron` is a daemon that triggers jobs on a regular basis 

It works with different configuration files that specify when a job should be started 

Use it for regular re-occurring jobs, like backup jobs

`at` is used for tasks that need to be started once

systemd timers provide a new alternative to `cron` 

**12.2 Understanding cron Scheduling Options**

`crontab -e` will create a cron job for the current user 

stored in `/etc/cron.d`

scripts, executed on an hourly, daily, weekly, monthly basis -- do not have a specific time indication 

generic time-specific cron jobs in `/etc/crontab` deprecated 

**12.3 Understanding anacron** 

is a service behind cron that takes care of jobs that are executed on a regular basis but do not specify a time 

it takes care of jobs in `/etc/cron.hourly|daily|weekly|monthly` 

configuration is in `/etc/anacron` 

**12.4 Scheduling with cron**


`crontab -e` as a specific user 

create a cron tim ein `/etc/cron.d`

>`cron` time specification: `*/10 4 11 12 1-5`
> * `*/10` minute, every ten minutes 
> * `4` hour
> * `11` day of month
> * `12` month, 
> * `1-5` day of week, specifies weekdays 

cron does not have stdout, so any command that writes to stdout will fail 

use `*` to specify every instance of the time specification 

>**Exam Tip** 
> * `man 5 cron` is useful 

**12.5 Scheduling Tasks with Systemd Timers** 

systemd timers also allows for scheduling jobs at a regular basis, cron is still the standard

read `man 7 systemd-timer` for more information about systemd timers 

read `man 7 systemd-time` for specification of the time format to be used 

**12.6 Using at** 

`at` developed to run a job at a specific time 

the `atd` service must be running to run once-only jobs 

use `at [time]` to schedule a job 

* type one or more specifications in the `at` interactive shell

* use `ctrl+d` to close this shell 

use `atq` for a list of jobs currently scheduled

use `atrm` to remove jobs from the list 

**12.7 Managing Temporary Files** 

the `/usr/lib/tmpfiles.d` directory manages settings for creating, deleting and cleaning up of temporaty files 

the `systemd-tmpfiles-clean.timer` unit can be configured to automatically clean up temporary files 

* it triggers the `systemd-tmpfiles-clean.service` 

* this service runs `systemd-tmpfiles --clean` 

the `/usr/lib/tmpfiles.d/tmp.conf` file contains settings for the automatic tmp file cleanup 

when making modifications, copy the file to `/etc/tmpfiles.d` 

after making modifications to the file, use `systemd-tmpfiles --clean /etc/tmpfiles.d/tmp.conf` to ensure the file does not caontain any errors

in `tmp.conf` you'll find lines specifying which directory to monitor, which permissions are set on what directory, which owners, and after how many days of not being used will the tmp file be removed 

different actions can be performed on the directories and files that are managed 

consult `man tmpfiles.d` for more detais

## Lesson13

### Configuring logging

#### Learning objectives

* 13.1 Understanding rhel 8 logging options 
* 13.2 Configuring rsyslog logging
* 13.3 Working with `systemd-journald` 
* 13.4 Preserving the systemd journal 
* 13.5 Configuring logrotate 

**13.1 Understanding rhel 8 logging options**

`rsyslogd` has been around since the late 1970s

`systemd-journald` is not peristent by default and it's in memory only 

can make it persistent by creating `/var/log/journald` and rebooting systsem 

![image](https://richardbright.me/images/13.1-1.png)


**13.2 Configuring Rsyslog Logging**

rsyslogd needs the `rsyslogd` service running 

the main configuration file in `/etc/rsyslog.conf`, specifies the rules for what and where services are logged

snap-in files can be placed in `/etc/rsyslog.d`

each logger line contains three items

* `facility` the specific facility that the log is created for 

* `severity` the severity from which should be logged 

* `destination` the file or other destination the log should be written 

log files normally are in `/var/log`

use the `logger` command to write messages to rsyslog manually 

`rsyslogd` is and must be backward compatible with the archaic syslog service 

in syslog, a fixed number of facilities was defined, like kern, authpriv, crom, and more

to work with services that don't have their own facility, local{0...7} can be used

becasue of the lack of facilities, some services take care of their own logging and don't use rsyslog

**13.3 Working with systemd-journald**

`systemd-journald` is the log service that is part of systemd

logs everyting from the start of the system 

it integrates well with `systemctl status [unit]`

`journalctl` can be used to read log entries 

messages are logged also to rsyslogd using the rsyslogd imjournal module 

to make journal persistent, use `mkdir /var/log/journal` 

**13.4 Preserving the Systemd Journal**

the journal is written to `/run/log/journal` which is automatically cleared on system reboot

edit `/etc/systemd/journald.conf` to make the journal persistent across reboots 

set the storage parameter in the file to one of three values 

* `persistent` will store the journal in the `/var/log/journal` directory. the directory will be created if it doesn't exist 

`volatile` stores the journal only in `/rin/log/journal` 

`auto` will store the journal in `/var/log/journal` if that directory exists, and in `/run/log/journal` if no `/var/log/journal` exists 

built-in log rotation runs monthly 

journal cannot grow beyond 10% of the size of the filesystem it is on 

the journal will also make sure at least 15% of its file system wil remain available as free space 

these settings can be changed through `/etc/systemd/journald.conf`


`systemctl status systemd-journald` to check service status 

use `-l` option to show file size 

**13.5 Configuring Logrotate**

logrotate is started through cron.daily to ensure that log files don't grow too big

main configuration is in `/etc/logrotate.conf` snap-in files can be provided through `/etc/logrotate.d/`

deafult logrotate is every 4 weeks 

## Lesson14

### Managing storage

#### Learning objectives

* 14.1 Understanding disk layout 
* 14.2 Understanding linux storage options 
* 14.3 Understanding gpt and mbr partitions 
* 14.4 Creating partitons with `parted` 
* 14.5 Creating mbr partitions with `fdisk` 
* 14.6 Understanding file system differences
* 14.7 Masking and mounting file systems 
* 14.8 Mounting partitions through `/etc/fstab`
* 14.9 Managing persistent naming attributes 
* 14.10 Managing systemd mounts
* 14.11 Managing xfs file system
* 14.12 Creating a swap partition 

**14.1 Understanding disk layout** 

![image](https://richardbright.me/images/14.1-1.png)

`lsblk` lists all disks attached to the computer 

`parted` is the preferred partitioning utility in rhel 

`cat /proc/partitions` to show a list of partitions on the system 

**14.2 Understanding Linux Storage Options**

partitions are the classic solution, us ein all cases

used to allocate dedicated storage to specific types of data 

partitions are used to segment the systems data 

lvm logical volumes

used as the default installation in rhel 

add flexibility to storage (resize, snapshots and more)

stratis is the next generation volume managing filesystem that used thin provisioning by default 

implimented is user space, which makes api access possible 

vdo - virtual data optimizer

focused on storing files in the most efficient way 

manages deduplicated and compresses storage pools

**14.3 Understanding GPT and MBR Partitions**

mbr - master boot record is part of the 1981 pc specification 

* 512 bytes to store boot information 
* 64 bytes to store partitions 
* place for 4 partitions only with a max size of 2TB
* to use more partitions, extended and logical partitions must be used 

gpt - guid partition table 

* more space to store partitions 
* used to overcome mbr limitations 
* 128 partitions max 
* developed to work with uefi - universal extendable firmware interface 

**4.4 Creating Partitions with parted**

while creating a partition, you do not automatically create a filesystem 

the `parted` file system attribute only writes some unimportant file system metadata 

in rhel 8, `parted` is the default utility 

alternatively, use `fdisk` to work with mbr and `gdisk` to use guid partitions 

`parted /dev/sdb` 

`print` wil show isf there is a current partition table 

`mklabel msdos|gpt` 

`mkpart part-type name fs-type start end` 

* `part-type` applies to the mbr only and sets primary, logical, or extended partition
* `name` arbitrary name, required for gpt
* `fs-type` does not modify the filesystem but sets some irrelevant system dependent metadata 
* `start end` specify start and end, counting from the begining of the disk 
* for example, `mkpart primary 1024 MiB 2048MiB` will create a 1 GiB primary partition 
* you can use `mkpart` in interactive mode 

rhel defaults to KiB, MiB, GiB, etc.. and not KB, MB, GB

KiB is expressed as 1024 
KB is expressed as 1000

`print` to verify creation of new partition 

`quit` to exit parted she;; 

`udevadm settle` to ensure that the new partition device is created 

`cat /proc/partitons` to verify the creation of the partition

**14.5 Creating MBR Partitions with fdisk**

`fdisk /dev/nvme0n[n]` to start shell 

`partprobe` recent changes to disk are updated to the kernel partition table 

**14.6 Understanding File System Differences**

you will need to apply a filesystem after making a partition 

`xfs` is the default rhel filesystem 

* fast and scalable 
* used copy on write (CoW) to guatentee data integrity 
* size canbe increased but not decreased 

`ext4`

* default in rhel 6 and is still used 
* backward compatible to ext3
* uses jornal to guarantee data integrity 
* size can be increased and decreases 

`btrfs` rhel decided not to move forward with this file sysytem 


**14.7 Making and Mounting File Systems**

`mkfs.xfs` creates an xfs file system 

`mkfs.ext4` creates an ext4 file system 

use `nkfs.tab.tab` to show full list 

do not use `mkfs` as it will default to creating an ext2 file system 

after making the files system, you can mount it in runtime using the `mount` command 

use `umount` before disconnecting a device 

`mkfs.xfs /dev/nvme0n1p1` to make an xfs file system on partition one on disk nvmen1

`mount /dev/nvme0n1p1 /mnt` to mount the partition on the /mnt directory 

you can run `mount` to see all the mounted filesystems 

`mount | grep '^/'` to show only mounts that start with a `/`

`umount /dev/nvme0n1p1` to disconnect the partition 

`lsof /mnt ` will show open files that will need to be closed if you ar eunable to `umount` 

can also use `umount /mnt` 

`mkfs.vfat` will create a windows compatible 32 bit filesystem 

**14.8 Mounting Partitions through /etc/fstab**

`/etc/fstab` is the main configuration file to persist mount partitions 

`/etc/fstab` content is used to generate systemd mounts by the `systemd-fstab-generator  utility  

|**name of device** | **mount point** | **file system type**  | **default mount options**  | **dump** | **filesystem check** |
| --- | --- | --- | --- | --- | --- | --- | 
| /dev/nvme0n1p1 | /mnt | xfs | defaults | 0 | 0 | 
| LABEL=mnt | /mnt | xfs | defaults | 0 | 0 | 
| UUID="856916af-0e43-44d5-a280-090ba8470970" | /mnt | xfs | defaults | 0 | 0 | 

to update systemd, make sure to use `systemd demon-reload` after editing `/etc/fstab`

`mount -a` to mount all file systems specified in /`etc/fstab`

**14.9 Managing Persistent Naming Attributes** 

why do we need persistent nameing? 

in datcenter ebvironments, block device names may chnage. Different solutions exist for persistent naming 

uuid - universal unique id, and is automatically generated for each device that contains a file system or anything similar 

label - while creating the file system, the option `-L` can be used to set an arbitraty name that can be used for mounting the file system 

unique device names are created in `/dev/disk` 

`blkid` block id, provides the uuid 

`tune2fs` to set  label 

`tune2fs -L articles /dev/nvme0n191` to set a label 

`ll /dev/disk/by-uuid/` to also see disk names, uuids, etc...

**14.10 Managing Systemd Mounts**

`/etc/fstab` mounts already are systemd mounts

mounts can be created using systemd .mount files 

using .mount files allows you to be more specific in defining dependecenies 

use `systemctl cat tmp.mount` for an example 

| **what** | **where** | **type** | **options**  | 
| --- | --- | --- | --- | --- | --- | --- | 
| tmpfs | /tmp | tmpfs | Options=mode=1777,strictatime,nosuid,nodev |

copy file as a template and chnage settings 

`cp /usr/lib/systemd/system/tmp.mount /etc/systemd/system/example.mount`

run `systemd demon-reload` after configuring the file 

`systemctl enable --now example.mount` 


**14.11 Managing XFS File Systems**

utilities for managin gxfs file system 

`xfsdump` used for creating backups of xfs formatted devices and considers specific xfs attributes 

* `xfsdump` only works on a complete xfs device 
* `xfsdump` can make full backups (-l O) or different levels of incremental backups
* `xfsdump -l ) -f /backupfiles/data.xfsdump /data` creates a full backup of the contents of the /data directory 

the `xfsrestore` command is used to restore a backup that was made with `xfsdump` 

* `xfsrestore -f /backupfiles/data.xfsdump /data` 

the `xfsrepair` command cna be manually started to repair broken xfs file systems 

**14.12 Creating a Swap Partition**

swap is ram emulated on disk 

linux kernel is smart with dealing with swap 

all linux systems should have at least some swap 

the amount of swap depends on the use of the server 

the amount of swap is related to the amount of ram you have on the server 

264GB of ram, 64gb of swap 

swap can be created on any block device, including swap files 

while creating swap with `parted`, set file system to linux-swap 

after creating the swap partition, use `mkswap` to create the swap file system 

activate using `swapon /dev/nvme0n2p2`

add a line to `/ect/fstab` to make it persistent 

swap is not mounted on a directory, it is mounted on the swap kernel interface 