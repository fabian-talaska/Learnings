# Lab rhcsa-compreview2:

### Mount network filesystem persistently to /local-share

```
vim /etc/fstab
servera.lab.example.com:/share /local-share nfs rw,sync 0 0
```

### Create a group and 4 new users. Add group to all users.
```
groupadd production
for 1 in $(seq 1 4); do usermod -aG production production$i; done 
```

### Store tmp files in /run/volatile. Time based cleanup if not accessed for +30min. Octal permissions 0700.
```
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

### Configure firewall to reject ssh connections from servera(=172.25.250.10/32)
```
firewall-cmd --add-source=172.25.250.10/32 --zone=block --permanent
firewall-cmd --reload
```
