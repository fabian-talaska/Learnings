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

### User default umask 007

```
su - dbuser1
cd
echo "umask 007" >> .bash_profile
echo "umask 007" >> .bashrc
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
