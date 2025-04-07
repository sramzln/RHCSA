```Shell

### Add group / add secondary group
# /etc/passwd
# /etc/group
groupadd newgroup
useradd -G newgroup harsh

useradd nitin -s /sbin/nologin
useradd -g superheroes 
        -s /bin/bash
        -c "user description"
        -m -> create home directory
        -d /home/spiderman -> home folder
        spiderman
chgrp -R superheros spiderman



### SUID / GUID / sticky bit
-rwsr-xr-x. 1 root root 32648 Aug 10  2021 /usr/bin/passwd # SUID . Files are executed by the effectif user ID of the proccess. (only files)
-rwx--s--x. 1 root slocate 41032 Aug 10  2021 /usr/bin/locate # GUID Files are executed by the effectif group ID of the proccess. Files created in this directory are the same ownership as the directory. (files and folders)
drwxrwxrwt. 17 root root 4096 Mar 31 09:09 /tmp # Sticky bit. Files in this directory can be renamed or removed only by th owner (only folders)

chown -R :mac /linux # Chown group mac all existing files
chmod g+s /linux
chmod +t /linux # only the user-owner can delete the file

### ACL
getfacl  /var/fstab 
setfacl -m u:natasha:rw /var/fstab # change access only for one user
setfacl -m g:mac:--- /var/fstab # change access for one group



### Configuring repositories and install packages
# Repositories
# https://xyz.server.com/baseos
# https://xyz.server.com/appstream
# /etc/yum.repos.d/local.repo
name=baseOS
baseurl=https://xyz.server.com/baseos
gpgcheck=0
enabled=1

dnf install tuned httpd stratis-cli stratisd
dnf update kernel

### Tuned, NTP, systemctl
# tuned
dnf install tuned
tuned-adm list
tuned-adm active # to show which profile is active
tuned-adm profile balanced # To change profile
tuned-adm recommend # show the recommended profile
tuned-adm off

### NTP - chrony, chronyd
dnf install chrony
vi /etc/chrony.conf
pool add_given_server iburst
systemctl restart chronyd
chronyc sources -c
timedatectl
timedatectl set-ntp true/false
timedatectl list-zones
timedatectl set-timezone America/New_York
timedatectl set-time HH:MM:SS
timedatectl set-time '2021-08-18 20:15:50'
timedatectl set-ntp true

### Hard / Soft links
ln # hard link
ln -s # soft link

### Disk partition and swap space
lsblk
fdisk /dev/sdb
partprobe /dev/sdb
mkdir /newdisk
mkfs.xfs /dev/sdb1
mount -t xfs /dev/sdb1 /newdisk
umount /newdisk
vi /etc/fstab # add the disk to fstab file
systemctl daemon-reload
mount -a
# add swap partition size
free -mh
# Add as a normal disk, change type to swap n° 82
fdisk /dev/sdb
vi /etc/fstab
# /dev/sdb3 swap  swap  defaults 0 0
swapon -a
free -mh

### LVM2
lsblk
lsblk -f # fs type
lsblk -o name,size,fstype

# Create PV
pvcreate /dev/sdb
pvdisplay

# Create PV 2
fdisk /dev/sdc
pvcreate /dev/sdc1 /dev/sdc2
pvscan
pvremove

# Create VG
vgcreate lvm1 /dev/sdb /dev/sdc1
vgdisplay
vgscan

# Extend / Reduce VG by a physical volume
vgextend lvm1 /dev/sdc2
vgreduce lvm1 /dev/sdc2
vgremove lvm1

# Logical Volumes
lvextend -r -L +2G /dev/VG1/lv1 # -r extend automatically the file system too
vgextend VG1 /dev/sde
lvextend -r -L +6G /dev/VG1/lv1

lvchange -an /dev/VG1/LV1
vgremove /dev/VG1

vgcreate -s 8M VG2 /dev/sdc # Extend size 8MB
lvcreate -l 10 -n LV2 VG2 # Specify the number of extends
lvcreate -l 100%FREE -n LV2 VG2 # Extend to 100% free space

lvcreate -L size -n name vgname
lvreate -L +8G -n lv1 lvm1

lvcreate -l 60%VG -n lv2 lvm2
lvcreate -l 100%FREE -n lv2 lvm2

lvscan

mkfs.ext4 /dev/lvm1/lv1
mount -t ext4 /dev/lvm1/lv1 /mnt

lvresize -L +2GB /dev/lvm1/lv1
resize2fs /dev/lvm1/lv1

lvreduce -r -L -1G dev/lvm1/lv1 # with resizefs

xfs_grofs /dev/lvm2/lv1

### Stratis
# Use XFS only
# thin provisioning
# fs snapshots
# tiering
# pool-based management
# moitoring
blockdev(min size 1GB) => pool(1 or more bock devices) => filesystem

dnf install stratis-cli stratisd
systemctl enable stratisd --now
# Create new pool
stratis pool create pool1 /dev/sde
stratis pool list

stratis pool add-data pool1 /dev/sdd

stratis filsystem create pool1 fs1
stratis filesystem set-quota mypool/fs1 10G
stratis filesystem list
stratis filesystem list-quota

# vi /etc/fstab
UUID  /fs1  xfs   defaults,x-systemd.requires=stratisd.service 0 0

### VDO (Virtual Data Optimiser)
# Inline data reduction, deduplication, compression, thin provisionning

dnf install vdo kmod-kvdo

vdo create --name VD01 --device=/dev/sde --vdoLogicalSize=50G
vdo list
mkfs.xfs /dev/..

### cron
#minutes hours day_of_month month day_of_week command
[https://crontab.guru/]
systemctl status crond
crontab -e
crontab -l
crontab -r

run-parts /etc/cron.hourly

# 0 10 4 2 * /usr/local/bin/backup
crontab -e -u baljit 
# 08 12 * * 4 /bash/echo hello

### grep
grep -i "root" /etc/group



###SELinux
getenforce
setenforce

semanage fcontext -a -t httpd_sy_content_t "/NEW(/.*)?"
retorecon -Rv /web
# Modify /etc/httpd/conf/httpd.conf
# DocumentRoot and Directory


getsebool -a
setsebool -P httpd_enable_cgi on # -P persistent after reboot
setsebool -P httpd_enable_homedirs on
semanage port -a -t http_port-t -p tcp 82

### Containers, podman
# Pull a container image
dnf install podman container-tools
podman login registry.redhat.io
podman search httpd
podman pull docker.io/manasip/httpd
podman pull docker.io/library/httpd
podman rmi # remove image

mkdir /web
touch /web/index.html

# Run a container
podman run -d --name web1 docker.io/library/httpd
podman run -d --name web2 -p 8080:80 docker.io/library/httpd
podman run -ti --name web3 -p 8081:80 docker.io/library/httpd /bin/bash

# Map the container to a local directory
podman run -d --name web6 -p 8082:80 -v /web:/usr/local/apache2/htdocs:z  docker.io/library/httpd
curl localhost:8082

# Run the container as a service
# root user
podman generate systemd web > /etc/systemd/system/web.service
systemctl daemon-reload
systemctl enable web --now

# regular user
useradd test
passwd test
ssh test@localhost
podman login registry.redhat.io
podman pull docker.io/library/httpd

podman run -d --name new -p 8082:80 -v docker.io/library/httpd
mkdir -p ~/.config/systemd/user
podman generate systemd new > ~/.config/systemd/user/new.service

systemctl --user daemon-reload
systemctl --user denable web --now

### Keymap
localctl status
localctl list-locales
localctl list-keymaps
localctl set-keymap map
localctl set-X11-keymap map



### Shell scripting
#!/bin/bash

# Local variables

# Env. variables
env
$HOME
$SHELL

# Example 1
echo "Enter your name"
read name
touch $name
echo "File created"

# Posotional parameters
$0 name of script
$1 #first argument
$# #total number of arguments
$* #value of all arguments

# Example 2
read -p "Enter a number: " number

if [[ $number > 10 ]]; then
  echo "The number is grather then 10"; else
  echo "The number is not grather then 10"
fi

-eq # equal
-ne # not equal
-gt # greather then
-ge # greather or equal
-lt # less then
-le # less ou equal
!exp # experssion is false

# Example 3
#!/bin/bash
read -p "Enter a number: " number

if [[ $number -ge 10 ]]; then
  echo "The number is grather then 10"; else
  echo "The number is not grather then 10"
fi

read -p "Enter a number: " number

if (( $number >= 10 )); then
  echo "The number is grather then 10"; else
  echo "The number is not grather then 10"
fi

= # if string is equal
!= # if trings are not equal
-n # true if string is not null
-z # true if string is null
```
