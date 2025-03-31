# RHCSA

https://www.redhat.com/en/services/training/ex200-red-hat-certified-system-administrator-rhcsa-exam?section=objectives

## Understand and use essential tools

- **Access a shell prompt and issue commands with correct syntax**
- **Use input-output redirection (>, >>, |, 2>, etc.)**
- Use grep and regular expressions to analyze text
- Access remote systems using SSH
- **Log in and switch users in multiuser targets**
- Archive, compress, unpack, and uncompress files using tar, gzip, and bzip2
- **Create and edit text files**
- **Create, delete, copy, and move files and directories**
- Create hard and soft links
- **List, set, and change standard ugo/rwx permissions**
- Locate, read, and use system documentation including man, info, and files in /usr/share/doc

## Create simple shell scripts

- Conditionally execute code (use of: if, test, [], etc.)
- Use Looping constructs (for, etc.) to process file, command line input
- Process script inputs ($1, $2, etc.)
- Processing output of shell commands within a script

## Operate running systems

- Boot, reboot, and shut down a system normally
- Boot systems into different targets manually
- Interrupt the boot process in order to gain access to a system
- Identify CPU/memory intensive processes and kill processes
- Adjust process scheduling
- **Manage tuning profiles**
- Locate and interpret system log files and journals
- Preserve system journals
- Start, stop, and check the status of network services
- Securely transfer files between systems

## Configure local storage

- **List, create, delete partitions on MBR and GPT disks**
- **Create and remove physical volumes**
- **Assign physical volumes to volume groups**
- **Create and delete logical volumes**
- **Configure systems to mount file systems at boot by universally unique ID (UUID) or label**
- **Add new partitions and logical volumes, and swap to a system non-destructively**

## Create and configure file systems

- Create, mount, unmount, and use vfat, ext4, and xfs file systems
- Mount and unmount network file systems using NFS
- Configure autofs
- **Extend existing logical volumes**
- **Create and configure set-GID directories for collaboration**
- **Diagnose and correct file permission problems**

## Deploy, configure, and maintain systems

- Schedule tasks using at and cron
- Start and stop services and configure services to start automatically at boot
- Configure systems to boot into a specific target automatically
- Configure time service clients
- Install and update software packages from Red Hat Network, a remote repository, or from the local file system
- Modify the system bootloader

## Manage basic networking

- Configure IPv4 and IPv6 addresses
- Configure hostname resolution
- Configure network services to start automatically at boot
- Restrict network access using firewall-cmd/firewall

## Manage users and groups

- **Create, delete, and modify local user accounts**
- **Change passwords and adjust password aging for local user accounts**
- **Create, delete, and modify local groups and group memberships**
- **Configure superuser access**

## Manage security

- Configure firewall settings using firewall-cmd/firewalld
- Manage default file permissions
- Configure key-based authentication for SSH
- Set enforcing and permissive modes for SELinux
- List and identify SELinux file and process context
- Restore default file contexts
- Manage SELinux port labels
- Use boolean settings to modify system SELinux settings
- Diagnose and address routine SELinux policy violations

## Manage containers

- Find and retrieve container images from a remote registry
- Inspect container images
- Perform container management using commands such as podman and skopeo
- Perform basic container management such as running, starting, stopping, and listing running containers
- Run a service inside a container
- Configure a container to start automatically as a systemd service
- Attach persistent storage to a container

```Shell
### Basic Commands
touch
cp
ls -l long
   -r reverse
   -t time-sorted
mv
mkdir /rmdir
mkdir folder1/folder2/folder3

### Use input ouput redirection
ls -l > file
ls -l >> file2 # append
ls -l 2> err # error redirection
ls -l >> err+out 2>&1 # input and output redirection to same file
ls -l &> err+out # input and output in the same file
ls -l > 2> /dev/null

cat << EOF > reirect-file
..
EOF

tee -a output << EOF
..
EOF

command < file
cat < jerry

ls -l /etc | wc -l # pipes

### Find command
find / -name fstab -exec cp -a {} /home/zoltan/Documents/ \;
find / -size +20M
find / -atime +12 # 12 Hours
find /home/zoltan -empty
find / -iname toto # iname is not case senitive like -name

### Vim
ESC
   i -> insert
   r -> replace
   d -> delete
   x -> delete 1 char.
   u -> undo
 :q! -> quit
:wq! -> quit and save # shift + zz
   o -> new line

### Standard permissions
r - read
w - write
x - execute

u - user
g - group
o - others
a - all

chmod g-w jerry
chmod a-r jerry

chown -R
chgrp -R

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

#### Special attributes 
chattr # Change attribute
chattr -a # append only
chattr -i # immutable
chattr -d # disallow backups with dump command
lsattr # List attribute

### umask /etc/profile or /etc/bashrc
umask
# for a directory 777-mask give the permissions
# for a file 666-mask give the permissions

#### PATH variable
echo $PATH
export PATH="/path/new/folder:$PATH" # Add new folder to PATH. For interactive shell add this line in .bashrc, for login shell add to .bash profile
# For all users a /etc/profile.d/custom_path.sh
echo 'PATH="/path/new/folder:$PATH"' | tee -a /etc/profile.d/custom_path.sh
sudo chom +x /etc/profile.d/custom_path.sh

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
```
