# Born2beroot
Create a virtual machine and set up my own operating system

## Concepts
System administration

## WHAT’S A VIRTUAL MACHINE
A virtual machine is a “guest” computer system created on a physical “host” computer. It has its own CPU, memory, network interface, and storage. It is partitioned from the rest of the system, so that whatever happens inside of the guest computer does not affect the host computer.  
**Pros:**  
* You can have multiple operating system environments on the same server
* host machine won’t be affected if something happens inside of vm environment
* security benefits - if you need to run something that might have malware or has security issues, you can run it in a vm.
   
**Cons:**  
* Less efficient than real machines because they access hardware indirectly
* Can result in too many vms running on one host
* a weak host can impact its vms  

**Uses:**  
* can be used to backup your system and enable rapid disaster recovery
* provides operational flexibility - you can operate multiple displays and systems on the same console. This means you can operate different applications regardless of OS.
* Reduce overhead - you don’t have to buy as much hardware since you can operate multiple OS’s on one host computer. It’s also useful if you want to try out a new OS
* create new environment for developers to run dev-test scenarios or run something that has or might have security issues

**Hypervisor:** (also known as virtual machine monitor, VMM, or virtualizer) a type of computer software, firmware, or hardware that creates and runs virtual machines.  
There are two types:  
Type 1 - bare metal - runs directly on host’s hardware to manage guest operating system  
Type 2 - hosted hypervisor - run as a software (Oracle virtualbox is a type 2)  

## CENTOS VS DEBIAN
Centos has better service environments for servers
Debian has more secure and stable Linux distributions 
Debian has more packages and uses apt-get not yum
![60103c3a61497-debian-vs-centos-infograph](https://github.com/hbuivu/Born2beroot/assets/26291116/22f61126-1a01-4256-85e7-3f8184567b7d)

## APT/APTITUDE/APPARMOR
**APT:**  
* advanced packaging tool 
* command line tool
* need to provide package name
* can be used with higher level package managers
* needs more explicit instructions than Aptitude

**Aptitude:**  
* a higher-level manager than APT
* has an interface
* integrates apt-get, apt-mark, and apt-cache
* will automatically do things like remove unused packages
* has why and why-not commands to help investigate why certain actions are being blocked  

**AppArmor:**  
* mandatory access control framework
* confines programs according to a set of rules that specify what files a give program can access (allows you to define security policies)
* protects systems against known and unknown vulnerabilities  

## System Admin
### CHECK SSH/UFW
- sudo ufw status
- sudo systemctl status ssh
- sudo service ssh status
- head -n 2 /etc/os-release

### USER/GROUPS
- Create new user
- sudo adduser <username>
- getent passwd <username>

### Create group
- sudo addgroup <groupname>

### Add user to group
- sudo adduser <username> <groupname>

### Check user groups
- groups <username>
- getent group <groupname>

### PASSWORD POLICIES
**/etc/login.defs**  
- defines the site-specific configuration for the shadow password suite
- Use this to configure expiration date (every 30 days), set minimum number of days allowed before password is modified (2 days), and send warning message 7 days before password expires
**/etc/pam.d/common-password**  
* this file is included by other files in /etc/pam.d/ to define the behavior of password use in subsystems that grant access to services in the machine (login systems). 
* use this to configure the rest of the password policy
* set password minimum length to 10 characters  
  minlen=10  
* require at least one uppercase character  
  ucredit=-1  
* at least one lowercase character  
  lcredit=-1  
* at least one numeric character  
  dcredit=-1  
* reject password if it contains username  
  reject_username  
  usercheck=1  
* at least 7 changes required in the new password from old password
  difok=7
* implement the same policy on root  
  enforce_for_root
  
**Why have a more secure password policy?**  
Statistically, the more characters required/types of characters required, the more combinations of characters there are. With less character count/character type requirements, it's easier for a hacker to find your password bc they would need to go through less combinations of characters.  
A disadvantage would be that a more complicated password may be easier to forget. And there is human bias when picking out passwords, so while it’s good to add some complexity, commonly used patterns/phrases/letters/numbers will reduce the usefulness of that complexity.  

**To manually change password for previous users before implementation of password policy**
sudo chage -m <username> (min day before password change)  
sudo chage -M <username> (max day before password change)  

**Change passwords**  
passwd  
sudo passwd (for root)  

**View password policies**  
sudo chage -l <username>  

### HOSTNAME
**Check hostname**  
sudo hostnamectl status

**Modify hostname**  
sudo hostnamectl set-hostname <new_hostname>  
sudo vim etc/hosts  
change 127.0.0.1 old_hostname → 127.0.0.1 new_hostname  
sudo reboot  

### PARTITIONS
**View partitions**  
lsblk  

**Why partition hard drive?**  
allow multiple operating systems to run on same device  
can store different types of files on different partitions  
ex: separate user data from system data to prevent system partition from becoming too full that it becomes unusable. 
makes backing up data easier
one disadvantage is that it can lead to inefficient use of the whole disk

**Logical vs primary partition**
Only 4 primary partitions can exist on a single disk.
A logical partition can be split up any number of times. 


**What is LVM and pros of logical partitioning?**
* LVM stands for logical volume manager, used for mapping and managing hard disk memory on Linux systems.  
* offers system admins more flexible approach to managing disk storage than traditional partitioning  
* facilitate managing the sometimes conflicting storage needs of multiple end users - with this approach, the admin doesn't need to allocate all disk storage space at initial setup. some of it can be reserved for later.  
* makes it easier to to resize and move storage as needed
* **MAIN IDEA: LVM looks at all storage on hard disks as one unit of memory. In addition to dividing memory, it can also COMBINE memory from different hard disks and allocate as needed.**


**Why encrypt your hard drive?**  
Data on an encrypted hard drive cannot be read by anyone who doesn’t have the key or password. This is a way to protect your data and information.  


**Extra vocab**
* Buffer - small amt of unusable space on each disk
* /boot  - stores data that is used before kernel begins executing user-made programs...aka it stores everything required for boot process except for configuration files not needed at boot time
* /swap - a swap space serves as a temporary overflow space for ram
* /home - director for a particular user in the system
* / - (root) - uppermost directory that contains all other directories in linux system
* /tmp - contains necessary files that are temporarily required by the system as well as other software and applications running on the machine
* /var - a standard subdirectory of the root directory in Linux and other Unix-like operating systems that contains files to which the system writes data during the course of its operation.
* /var/log - contains linux log files
* ext4 journaling filesystem - designed to ensure that filesystem structures appear consistent on disk at all times. It is a successor of ext3 and ext2. 
journaling file system - keeps track of changes not yet committed to the file system’s main part by recording the goal of such changes in a data structure known as a journal (usually a circular log). In case of a crash/power failure, file systems can be brought back online with lower likelihood of being corrupt
* VDI - virtualbox disk image, the container format for guest hard disks used by VirtualBox.
* ISO - a cd-rom image saved in ISO format. It can be used as a source file to create CD’s.


### SUDO
**Check sudo installation**  
sudo  
dpkg -l | grep sudo  


**Assign new user to sudo**
adduser <username> sudo  


**Verify user groups**  
groups <username>    


**What is sudo and why use it?**  
* Sudo gives chosen users temporary root privileges. (You can validate and refresh the sudo time limit using sudo -v). You can limit admin privileges for users who don’t need it.
* By giving some users admin privileges, you don’t need to give out the root password to everyone. 
* Root is easier to attack. Hackers don’t need to guess the login name (it’s root) and it always has admin privileges. By disabling root, an attacker will have a harder time guessing login and password of an admin user instead. 
* All commands and arguments will be logged as part of the security and compliance protocol. You can audit user behavior in case you need to figure out something that happened on the system. 
alternatively, su has a shell history feature, but its default age is 1000 lines, so there’s no scale.


**Sudo policy file**  
sudo vim /etc/sudoers.d/sudo_config  
Changes made to files in /etc/sudoers.d remain in place if you upgrade the system. This can prevent user lockouts when the system is upgraded.   
Sudoers file is controlled by distribution’s package manager. You will have to go in to manually approve changes to the file to merge it into a new file when upgrades take place.  


**Configuring sudo**
_Format:_  
* Defaults	Rule
* passwd_tries=3 → you can try password 3x
* badpass_message = “oop wrong password” → customizable message
* logfile=”/var/log/sudo/sudo.log” → log all sudo commands to this log file
* log_input, log_output → archive all sudo inputs and outputs to iolog_dir
* iolog_dir=”/var/log/sudo”
* requiretty (see below)
* secure_path=”...” (see below)
_TTY_  
* teletypewriter - command used to provide the file name of the terminal connected to the standard input
* sudo must be run from a logged in terminal session (tty). This prevents sudo from being used from daemons or other detached processes like cronjobs or webserver plugins or directly from an ssh call without setting up a terminal session. 
_secure_path_  
* Path used for every command run from sudo. If you don't trust the people running sudo to have a same PATH environment variable you may want to use this. Another use is if you want to have the ''root path'' be separate from the ''user path''. Users in the group specified by the exempt_group option are not affected by secure_path. This option is not set by default.


### UFW
Uncomplicated firewall - program to manage netfilter firewall


**Check installation**  
dpkg -l | grep ufw


**Check status and rules**  
sudo ufw status


**Add a new open port**  
sudo ufw allow <port_number>


**Delete rules**  
sudo ufw status numbered  
sudo ufw delete <rule_number>  
or   
sudo ufw delete allow <port_number> ← this is faster, will also delete v6 version  


**What’s a firewall and why use it?**  
* network security system to monitor and control incoming and outgoing network traffic based on predetermined security rules
* 2 types: network based and host based
* the default when setting up is that it will deny all incoming traffic and allow all outgoing traffic
* A firewall filters out potentially dangerous or superfluous network traffic. You can specify which port to open where information may come through.


**SSH**  
Check installation and status  
dpkg -l | grep ssh  
sudo systemctl status ssh  


**Verify SSH service rules**  
sudo systemctl status ssh


**Use SSH to log in**   
ssh <user>@127.0.0.1 -p 4242  
ssh root@127.0.0.1 -p 4242


**Check config file**  
/etc/ssh/sshd_config


What is SSH and why use it?
Secure Shell/Secure Socket Shell
network protocol that gives system admins a secure way to access a computer of an unsecured network
attempts to safely communicate encrypted data over two computers using an open network
provides users with strong password authentication and public key authentication


BASH SCRIPT
Directory
/usr/local/bin/monitoring.sh


Make file executable
sudo chmod 755 /usr/local/bin/monitoring.sh
sudo chmod +x <filename>


#!bin/bash
/bin/bash is the most common shell used as default shell for user login of the linux system.


ARCH=$(uname -a)
prints basic info about the operating system name and system hardware. 
-a prints kernel name, network node hostname, kernel release date, kernel version, machine hardware name, hardware platform, operating system


PCPU=$(lscpu | grep "Socket(s)" | awk '{print $NF}')
Physical processors - number of hardware cpus in the system

lscpu gets CPU information about the system
Socket(s) tells you how many physical CPUs there are. (CPU will give you number of logical CPUs)
awk allows users to process and manipulate data and produce formatted reports; NF → last line


VCPU=$(grep "^processor" /proc/cpuinfo | wc -l)
a virtual processor is the portion of the physical cpu that is assigned to the virtual machine
/proc/cpuinfo displays what type of processor your system is running including the number of CPUs present
^ → finds all lines that start with processor
wc -l → counts words and prints only number of lines present in the file


MEM=$(free -m | grep "Mem" | awk '{printf("%s/%sMB (%.2f%%)", $4, $2, 100*$3/$2>
RAM → random access memory. It is hardware that allows computer to perform more than one task at a time efficiently
RAM is where OS, application programs, and data in current use are kept so they can be reached quickly by the processor
volatile → data is retained only when the computer is on. It is lost when computer is off
free → shows total, used, free memory and buffer/cache


DISK=$(df -m --total | grep "total" | awk '{printf("%i/%.0fGB (%s)", $4, $2/100>
disk space refers to the hard drive storage
Hard drive storage is typically used for long-term storage of various types of files.
df → stands for disk free, displays the amount of disk space available on the filesystem with each file name's argument.
-m → show info in megabytes
--total → get grand total of all available storage	
	
CPU=$(mpstat | grep "all" | awk '{printf("%.1f", 100-$NF)}')
processor utilization is the % of time a processor is occupied compared to the total time the processor is available for use
mpstat shows activities for each available processor 


BOOT=$(who -b | awk '{printf($3 " " $4)}')
the who command is used to get info about currently logged in user on system


LVM=$(if [ $(lsblk | grep "lvm" | wc -l) -eq 0 ]; then echo "no"; else echo "ye>
lsblk lists info about all available or specified block devices
If there is no lvm listed, then LVM is not active


TCP=$(ss | grep tcp | wc -l)
transmission control protocol
communication standard that enable application programs and computing devices to exchange messages/info over a network
ss → socket statistics tool used to show network statistics


USERLOG=$(who | cut -d " " -f 1 | sort -u | wc -l)
cut will strip all info except username by displaying only the first field of indo delimited by a space
sort name by alphabetical order
-u option removes duplicates


NETWORK=$(hostname -I)
an IP address is a unique string of characters that identifies each computer using the Internet Protocol to communicate over a network
IPv4 uses 4 1 byte decimal numbers separated by a dot (32 bit) vs IPv6 , which is a hexadecimal separated by a colon
hostname command is used to view or change a system's domain and hostname.It can also be used to check IP address


MAC=$(ip link show | awk '$1=="link/ether"{print $2}')
media access control address
unique identifier assigned to a network interface controller for use as a network address in communications within a network segment
ip link → used to display and modify network interfaces


SUDO=$(journalctl _COMM=sudo -q | grep "COMMAND" | wc -l)
journalctl is used to query the systemd journal
If a file path refers to an executable script, a "_COMM=" match for the script name is added to the query
-q option suppresses informational messages


CRON
What is cron?
Cron is a job scheduler on linux. It runs in the background and schedules jobs like commands or shell scripts to run periodically at fixed times, dates, or intervals.


Check scheduled cron jobs/edit cron table/configure cron as root
sudo crontab -u root -e
cron utility runs based on commands specified in a cron table (crontab)


Start cron job/check status
sudo systemctl start cron
sudo systemctl status cron


Format of a cron job
minute   hour   date    month   day   <file> |< commands>
* any value
, value list separator
- range of values
/ step values
ex: 
*/<number> → run for every <number> min, hours, etc


Run a cron job every 10 minutes
*/10 * * * * sh /usr/local/bin/monitoring.sh | wall
wall → displays message to all logged in users


Stop cron job
sudo systemctl disable cron
Start cron job
sudo systemctl enable cron


COMMANDS GLOSSARY
dpkg -Debian package manager used to install, build, remove, and manage Debian packages
dpkg -l | grep <package_name>
check list of installed packages for the package_name
getent - get entry
getent group <groupname>
check which users have been added to specified group; verify user has been added to group
getent passwd <username>
check data on specified user; verify that user has been created
grep - use to search files for a some given “pattern”, like a word
groups - check groups
sudo reboot -reboot system
sudo -v - validate and refresh time limit on sudo


BONUS


Wordpress is a free and open source content management system that allows you to create a blog and website from a web-based interface.


To open wordpress site, go to browser
localhost
localhost: 80
by default port 80 is used by http connection


To edit the wordpress site
 http://localhost/wp-login.php


What is localhost
localhost is the standard hostname given to the machine itself. Usually it is 127.0.0.1


lighttpd is an open-source secure, fast, compliant, and very flexible web server that has been optimized for high-performance environments
by default it listens on port 80
provides web-based interface to control and manage the application
increases the speed and performance of wordpress
install package, open port 80 on ufw, add new port forwarding rule


MariaDB Server is one of the most popular open source relational databases. It is an alternative to MySQL
relational databases - a database structured to recognize relations between stored items of information
used for data warehousing, ecommerce, enterprise-level features, and logging applications
When a visitor goes to your site, the WordPress core queries the database. The database then returns the requested data. WordPress core combines the retrieved data and executes its PHP code to dynamically generate an HTML page. That page is served to your visitor's browser.


Enter MariaDB
sudo mariadb
Create database
CREATE DATABASE <database_name>;
Create new database user and grant full privileges
GRANT ALL ON <database_name>.* to ‘<username>’@’localhost’ IDENTIFIED BY ‘<password>’ WITH GRANT OPTION;
Flush privileges
FLUSH PRIVILEGES;
clears/reloads various internal caches used by MariaDB
Exit
exit


Login 
mariadb -u <username> -p
Confirm databases
SHOW DATABASES;


PHP (PHP: Hypertext Preprocessor) is a widely-used open source general-purpose scripting language that is especially suited for web development and can be embedded into HTML.
A scripting language is a series of commands that can be executed without the need for compiling
ex: PHP, Python, Perl
Used to create dynamic interactive websites
Wordpress is written using PHP as the scripting language
PHP is a server side language, which means that it runs on your web hosting server. 
Whenever someone visits your website, their browser contacts your server to request the page. The PHP code runs on the server, and generates an HTML page to send to the visitor. The visitor then sees the HTML page in their browser. They can’t see the PHP script because it’s only on the hosting server.
GET SIGNATURE
SAVE A SNAPSHOT OF YOUR VM
pwd
cd goinfre
cd born2beroot (or whatever you named your folder)
shasum born2beroot.vdi




