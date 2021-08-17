# Lab files-review

### 8. Create multiple folders with one command

```
mkdir -p Documents/my_bestseller/{editor,changes,vacation}
```

### 14. Use square-bracket pattern matching to specify which chapter numbers to match in the filename argument

The authors of chapters 5 and 6 want to experiment with possible changes. Copy both files from the ~/Documents/my_bestseller/chapters directory to the ~/Documents/my_bestseller/changes directory to prevent these changes from modifying original files. Navigate to the ~/Documents/my_bestseller directory. Use square-bracket pattern RH124-RHEL8.2-en-1-20200928Chapter 3 | Managing Files From the Command Line matching to specify which chapter numbers to match in the filename argument of the cp command.

```
cp chapters/mystery_chapter[56].odf changes
```

# Lab help-review start

### 1. Determine how to prepare a man page for printing. Specifically, find what format or rendering language is used for printing.

```
man man
man -t

```

### 3. Using man, learn the commands used for viewing and printing PostScript files.

```
man -k postscript viewer
```

### 4. Learn how to use the evince(1) viewer in preview mode. Also, determine how to open a document starting on a specific page.

```
evince -w
evince -i 3 passwd.ps
```

# LOOK AT PINFO!

### 7. Using pinfo, look for GNU Info documentation about the evince viewer.

```
pinfo evince
```

### 8. Using Firefox, open the system's package documentation directory and browse into the man-db package subdirectory. View the provided manuals.

```
firefox /usr/share/doc
```

# Lab users-review start

### 2. ensure that newly created users have passwords that must be changed every 30 days.

```
sudo vim /etc/login.defs
PASS_MAX_DAYS 30
```

### 4. Configure administrative rights for all members of consultants to be able to execute any command as any user.

```
sudo vim /etc/sudoers.d/consultants
%consultants ALL=(ALL) ALL
```

### 6. Set the consultant1, consultant2, and consultant3 accounts to expire in 90 days from the current day.

```
for i in $(seq 1 3); do sudo chage -E $(date -d "+90 days" +%F) consultant${i}; done
```

### 7. Change the password policy for the consultant2 account to require a new password every 15 days.

```
sudo chage -M 15 consultant2
```

### 8. Additionally, force the consultant1, consultant2, and consultant3 users to change their passwords on the first login.

```
for i in $(seq 1 3); do sudo chage -d 0 consultant${1}; done
```

# Lab perms-review

### 8. Modify the global login scripts. Normal users should have a umask setting that prevents others from viewing or modifying new files and directories.

```
sudo vim /etc/profile.d/local-umask.sh

#!/bin/bash
if [ $UID -gt 199 ] && [ "`id -gn`" = "`id -un`" ]; then
    umask 007
else
    umask 022
fi
```

# Lab processes-review

### 5. in "top" turn off bold and save config for reuse

```
top
SHIFT + B
SHIFT + W
```

### 9. Suspend the process 101. List remaining jobs.

```
ps -elf | grep process
kill -SIGSTOP 3512
jobs -l
```

# Lab ssh-review

### 5. Confirm that production1 can successfully log in to serverb using the SSH keys

```
ssh-keygen
ssh-copy-id production1@serverb
ssh production1@serverb
```

# Lab log-review

### 3. Display the log events recorded in the previous 30 min

```
journalctl --since $(date -d "-30 min" +%T)
```

# Lab net-review:

### Create a new connection with a static network connection using the settings in the table.

```
nmcli connection add ....... ipv4.method manual type ethernet
```

# Lab software-review:

### 2. Install the Apache HTTP Server module from the 2.4 stream and the common profile

```
yum module install httpd:2.4/common
```

### 4. Confirm that the package rhcsa-script-1.0.0-1.noarch.rpm is available on serverb. Install the package.

```
rpm -q -p rhcsa-script-1.0.0-1.noarch.rpm -i
sudo yum localinstall rhcsa-script-1.0.0-1.noarch.rpm
```

# Lab fs-review:

### 1. Generate a disk usage report of the /usr/share

```
du /usr/share
```

### 2. Use the locate command to find all rsyslog.conf

```
updatedb
locate rsyslog.conf
```

# Lab rhcsa-rh124-review1:

### Create multiple files with different endings

```
touch filename{1..4}
```

### Hard- and Softlinks

for hard links

```
ln TARGET LINK_NAME
```

for soft links

```
ln -s TARGET LINK_NAME
```

# Lab rhcsa-rh124-review2:

### Configure force password change

```
chage -p 0 dbuser1
```

### Change password after 10 days since last change

```
chage -m 10 dbuser1
```

### Expire password in 30 days

```
chage -M 30 dbuser1
```

### Configure the user dbuser1 to use sudo to run any command as the superuser

```
sudo vim /etc/sudoers.d/dbuser1

dbuser1 ALL=(ALL) ALL
```

OR

```
sudo usermod -aG wheel dbuser1
```

### User default umask 007

```
su - dbuser1
cd
echo "umask 007" >> .bash_profile
echo "umask 007" >> .bashrc
```

OR

```
sudo vim /etc/profile.d/dbuser1-default-umask.sh

#!/bin/bash
if [ $UID -gt 1003 ] && [ "`id -gn`" = "`id -un`" ]; then
    umask 007
else
    umask 022
fi
```

### user + group access and create permissions for folder. Others only read + execute

```
mkdir folder
chown user:group folder
chmod 775 fodler
```

### Only owners of files in folder can delete

```
chmod g+s folder
chmod o+t folder
```

**_*DIDNT WORK!*_**

# Lab rhcsa-rh124-review3:

### generate ssh key, install to remote server and login without password prompt

```
ssh-keygen
ssh-copy-id .ssh/MY_KEY.pub REMOTE_SERVER
ssh-agent $SHELL
ssh-add .ssh/MY_KEY
ssh REMOTE_SERVER
```

### Disable ssh root login

```
sudo vim /etc/ssh/sshd.conf
PermitRootLogin=no
sudo systemctl reload sshd.service
```

### Copy to file to remote server without password prompt

```
scp -i .ssh/MY_KEY MY_FILE USER@REMOTE_SERVER:/folder
```

### Enable the default module stream for the module python36 and **_*install*_** all provided packages from that stream on serverb.

```
sudo yum module install python36
```

# Lab rhcsa-rh124-review4:

### Add new connection with **_*ifname*_** and **_*without DHCP*_**!

```
sudo nmcli connection add con-name NAME ifname INTERFACE type ETHERNET ipv4.addresses IP-ADDRESS/MASK ipv4.gateway IP-ADDRESS ipv4.dns IP-ADDRESS ip.method manual
```

### nmcli generated files

```
cd /etc/sysconfig/network-scripts/
```

# lab rhcsa-rh124-review5:

### Search for files with a size of 100 **_bytes_**

```
find / -size 100c
```
