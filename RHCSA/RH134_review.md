# lab console-review

### Loop with 2 server names

```
for SERVER in servera serverb
do
ssh $SERVER hostname > /home/student/myFile.txt
done
```

# Lab acl-review

### Use setfacl to recursively update the existing cases directory and its contents. Grant the contractors group read, write, and conditional execute permissions.

```
setfacl -Rm g:contractors:rwX /shares/cases
```

### Use setfacl to recursively update the existing cases directory and its contents. Grant the contractor3 user read and conditional execute permissions.

```
setfacl -Rm u:contractor3:rX /shares/cases
```

# Lab selinux-review

### Launch a web browser on workstation and browse to http://serverb/lab.html. You will see the error message: You do not have permission to access /lab.html on this server.

```
sudo grep sealert /var/log/messages
sealert -l a55b...
ls -dZ /lab-content /var/www/html
sudo semanage fcontext -a -t httpd_sys_content_t /lab-content
sudo semanage fcontext -a -t httpd_sys_content_t /lab-content/lab.html
sudo restorecon -R -v /lab-content
```

# Lab storage-review

### Set the swap space on the swap2 partition to be preferred over the other.

```
sudo vim /etc/fstab

UUID=... swap swap pri=20 0 0
UUID=... swap swap pri=10 0 0

sudo systemctl daemon-reload
sudo swapon -a
sudo swapon --show
```

# Lab lvm-review

### Extend vg

```
pvcreate /dev/vdb2
vgextend serverb_01_vg /dev/vdb2
```

### Extend lv

Don't forget the **-r** option to resize the file system!

```
parted -s /dev/vdb mkpart primary xxx xxx
parted -s /dev/vdb set 2 lvm on
pvcreate /dev/vdb2
vgextend serverb_01_vg /dev/vdb2
lvextend -L +100M -r /dev/serverb_01_vg/lv_01
```

# Lab advstorage-review

### Persistently add stratis filesystem on boot. Don't forget x-systemd.requires=stratisd.service

```
sudo yum install stratisd stratis-cli
sudo systemctl start stratisd.service
stratis pool create labpool /dev/vdb
stratis pool add-data labpool /dev/vdc
stratis filesystem create labpool labfs
lsblk --output=UUID /stratis/labpool/labfs
sudo vim /etc/fstab

UUID=... /labstratisvol xfs defaults,x-systemd.requires=stratisd.service 0 0
```

### Create a file of 2GiB size on the new filesystem

```
dd if=/dev/urandom of=/labstratisvol/labfile2 bs=1M count=2048
```

### Create a snapshot and recover a file

```
stratis filesystem snapshot labpool labfs labfs-snap
rm /labstratisvol/labfile1
mkdir /labstratisvol-snap
sudo mount /stratis/labpool/labfs-snap /labstratisvol-snap
cp /labstratisvol-snap/labfile1 /labstratisvol/
```

### Create a VDO volume with the name labvdo using device /dev/vdd of logical size 50 GB

```
vdo create --name=labvdo --device=/dev/vdd --vdoLogicalSize=50G
mkfs.xfs /dev/mapper/labvdo
lsblk --fs
sudo vim /etc/fstab

UUID=... /labvdovol xfs defaults,x-systemd.requires=vdo.service 0 0

sudo systemctl daemon-reload
```

# Lab netstorage-review

### Configure an automounter indirect map on servera using shares from serverb. Create an indirect map using files named /etc/auto.master.d/shares.autofs for the master map and /etc/auto.shares for the mapping file

```
sudo yum install autofs
sudo vim /etc/auto.master.d/shares.autofs

/remote /etc/auto.shares

sudo vim /etc/auto.shares

* -rw,sync,fstype=nfs4 serverb:/shares/&

sudo systemctl enable --now autofs
```

# Lab boot-review

### Reset the root password to redhat.

Press CTRL+ALT+ESC and then "e" to modify the boot. Add the following at the end of the 4th line.

```
rd.break
```

Then press CTRL+X to continue. Perform the following steps and then continue with the boot

```
mount -o remount,rw /sysroot
chroot /sysroot
passwd root
redhat
redhat
touch /.autorelabel
exit
exit
```

### Reset the root password to redhat.

Press CTRL+ALT+ESC and then "e" to modify the boot. Add the following at the end of the 4th line.

```
systemd.unit=emergency.target
```

Then press CTRL+X to continue. Perform the following steps and then continue with the boot

```
mount -o remount,rw /
mount -a
vim /etc/fstab

# comment out failing line

systemctl daemon-reload
mount -a
reboot
```

### Change the default systemd target to start a GUI

```
systemctl set-default graphical.target
```

# Lab netsecurity-review

### Configure SELinux to allow the httpd service to listen on port 1001/TCP

```
sudo semanage port -l | grep http
sudo semanage port -a -t http_port_t -p tcp 1001
```

### Determine whether the correct ports are assigned to the firewall

```
sudo firewall-cmd --get-default-zone
sudo firewall-cmd --zone=public --list-all
sudo firewall-cmd --add-port=1001/tcp --permanent
sudo firewall-cmd --reload
sudo systemctl start httpd.service
```

# Lab installing-review

### Make the /home/student/kickstart.cfg file available at http://serverb.lab.example.com/ks-config/kickstart.cfg

```
ksvalidator /home/student/kickstart.cfg
sudo semanage port -a -t http_port_t -p tcp 1001

chmod 644 /home/student/kickstart.cfg
sudo cp /home/student/kickstart.cfg /var/www/html/ks-config/
```

# Lab containers-review

### Install the container tools

```
sudo yum module install container-tools

```

### Get latest tag of mariadb image on registry.lab.example.com. Use user podsvc on serverb

```
su - podsvc
podman login registry.lab.example.com
admin
redhat321
skopeo inspect docker://registry.lab.example.com/rhel8/mariadb-103
```

### Create a detached MariaDB container

named inventorydb. Use the rhel8/mariadb-103 image. Map port 3306 in the container to port 13306 on the host. Mount the /home/podsvc/db_data directory on the host as /var/lib/mysql/data in the container. Declare env variables

**use the :Z when mounting volumes**

```
podman run -d --name inventorydb -p 13306:3306 -v /home/podsvc/db_data:/var/lib/mysql/data:Z -e MYSQL_USER=operator1 -e MYSQL_PASSWORD=redhat -e MYSQL_DATABASE=inventory -e MYSQL_ROOT_PASSWORD=redhat registry.lab.example.com/rhel8/mariadb-103:1-86
```

### On serverb, as the podsvc user, configure systemd so that the inventorydb container starts automatically with the server.

```
mkdir -p ~/.config/systemd/user/
cd ~/.config/systemd/user/
podman generate systemd -n inventorydb -f --new
podman stop inventorydb
podman rm inventorydb
systemctl --user daemon-reload
systemctl --user podsvc enable --now container-inventorydb.service
loginctl enable-linger
```

# Lab rhcsa-compreview2

### Mount network filesystem persistently to /local-share

**options rw,sync**

```
vim /etc/fstab
servera.lab.example.com:/share /local-share nfs rw,sync 0 0
```

### Create a group and 4 new users. Add group to all users.

```
groupadd production
for 1 in $(seq 1 4); do useradd -G production production${i}; done
```

### Store tmp files in /run/volatile. Time based cleanup if not accessed for +30min. Octal permissions 0700.

```
man tmpfiles.d
vim /etc/tmpfiles.d/volatile.conf
d /run/volatile 0700 root root 30s
systemd-tmpfiles --create /etc/tmpfiles.d/volatile.conf
```

# Lab rhcsa-compreview3:

### Automatic folder mapping to remote home folder + SELinux allow

```
yum install autofs
vim /etc/auto.master.d/production5.autofs
/-  /etc/auto.production5
getent passwd production5 // --> gives you the user's home folder: /localhome/production5
vim /etc/auto.production5
/localhome/production5 -rw servera.lab.example.com:/home-directories/production5
systemctl restart autofs
getsebool -a | grep home
setsebool use_nfs_home_dirs on
```

### Configure firewall to reject ssh connections from servera(=172.25.250.10/32)

```
firewall-cmd --add-source=172.25.250.10/32 --zone=block --permanent
firewall-cmd --reload
```

### Fix Apache not starting (custom port 30080)

```
systemctl status httpd.service
grep sealert /var/log/messages
sealert -l <YOUR_ALERT>
semanage port -l | grep http
semanage port -a -t http_port_t -p tcp 30080
firewall-cmd --add-port=30080/tcp --permanent
firewall-cmd --reload
```

# Lab rhcsa-compreview4

### Mount volume in container

**use the :Z at the end** such that Podman automatically applies the SELinux container_file_t context type to the host directory

```
podman run -d --name web -v /srv/web:/var/www:Z ...
```

### Configure systemd to automatically start container and map port 8888

Logout and ssh again with user!!!

```
exit
ssh containers@serverb
mkdir -p .config/systemd/user
podman generate systemd --name web --files --new
podman stop -a
podman rm -a
systemctl --user daemon-reload
systemctl --user enable --now container-web.service
loginctl enable-linger
sudo firewall-cmd --add-port=8888/tcp --permanent
sudo firewall-cmd --reload
```
