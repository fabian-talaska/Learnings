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