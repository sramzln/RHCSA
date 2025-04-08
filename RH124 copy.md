# RH124

## Manage files from command line

```Shell
touch
cp
vi
whoami
pwd
mv
rmdir or rm -rf
chgrp
chown
ls -lrt
   - i -> inode
   - r -> reverse
   - t -> time-sorted
```

## Soft link / hard link

```Shell
# Soft link
ln -s 
#
 Hard link
ln

Pipes
command1 [argument] | command2 [argument]
ls -l | more
ls -l | tail -1  
```

## Help commands

```Shell
whatis / apropos # sudo mandb to update
command --help
man command
```

## Password

/etc/login.defs -> password default definition file

```Shell
chage [-m mindays] [-M maxdays] [-d lastday] [-I Inactive] [-E Expiredate] [-W warndays] user

chage -l spiderman
passwd spiderman

# Expirer passwd
passwd --expire spiderman
change --lastday 0 spiderman
id spiderman
```

### Sudo access

```Shell
visudo
/etc/sudoers
su - puis su - spiderman # don't ask passwd

usermod -aG wheel spiderman
```

## Monitor and manage linux process

```Shell
df
df -h 
uptime
top
free
lsof
tcpdump
netstat -rnv, -a, -u, -t
ps -ef
ps -ejH
vmstat
iostat
iftop
```

![alt text](images/image.png)

## Control services and daemons

```Shell
systemctl
systemctl --version
systemctl --all
systemctl status/start/stop/restart/enable/disable application.service
systemctl mask/unmask -> disable service completly
systemctl list-unit-files
```

## Configure ans secure SSH

```Shell
# File: /etc/ssh/sshd.config

# Timeout
    ClientAliveInternal 600
    ClientAliveCountMAx 0

# Disable root login
    PermitRootLogin

# Empty password
    PermitEmptyPasswords no

# Limit users
    AllowUsers user1 user2

# Use a different port
Port 2222

# SSH keys
ssh-keygen -t ed25519 - C "Zoltan key"
ssh-copy-id -i homelab_ed15519.pub zoltan@192.168.10.120
ssh-add ~/.ssh/homelab_ed15519
ssh -l zoltan 192.168.10.120
ssh zoltan@192.168.10.120
```

## Analyze and store logs

```Shell
# Log directory /var/log
boot
chronyd = NTP
cron
maillog
secure -> logins
messages # grep -i error messages
httpd
```

## Manage Networking

```Shell
nmtui
nmcli
nm-connection-editor
Gnome

ip addr
ip route
netstat -rnv

# nmcli
nmcli device show
nmcli connection

# Configure static IP
nmcli device
nmcli connection modify ens18 ipv4.addresses 192.168.10.121/24
nmcli connection modify ens18 ipv4.gateway 192.168.10.254
nmcli connection modify ens18 ipv4.method manual
nmcli connection modify ens18 ipv4.dns 192.168.10.117,1.1.1.1,1.0.0.1
nmcli c m ens18 ipv4.dns-search "exemple.com"
nmcli connection up ens18
nmcli connection down ens18
nmcli connection reload ens18
nmcli con mod ens192 ipv4.addresses "" ipv4.gateway ""

# Configure secondary IP
nmcli device status
nmcli connection show --active
nmcli connection modify ens18 +ipv4.addresses 192.168.10.122/24
nmcli connection reload

systemctl reboot
ip addr show

# Network files and basic command

# Files
/etc/sysconfig/network-scripts
/etc/hosts
/etc/hostname
/etc/resolv.conf
/etc/nsswitch.conf

# Commands
ping
ip
ifup or ifdown
netstat
traceroute
tcpdump
nslookup or dig
ethtool

# ip
ip addr show
ip a
ip -br -c a
ip -o a
ip -j -p a
ip -o -4 a | awk '{print $4}'| tail -n +2

ip link show
ip link set ens19 down
ip link set ens19 up

ip addr add 192.168.10.122/24 dev ens19
ip addr del 192.168.10.122/24 dev ens19

ip route show
ip route
ip r
ip -c route | column -t
ip r add default via 192.168.10.254 dev ens18
ip route add 192.168.10.122/24 via 192.168.10.254 dev ens19

watch "ip -s link show ens19 # statistics"

# Static ip
ip addr flush dev ens19 scope global
```

## File transfert

```Shell
# From my server to distant server / uploadin
scp file zoltan@192.168.10.119:/home/zoltan

# From distant server to my server / downloading
scp zoltan@192.168.10.119:/home/zoltan/file .

# Uploading folder
scp -r /path/to/local/source user@ssh.example.com:/path/to/remote/destination

# Downloading folder
scp -r user@ssh.example.com:/path/to/remote/source /path/to/local/destination
```

## Install and update Software Packages

```Shell
# Config files /etc/yum.repos.d/
dnf update
dnf upgrade
dnf remove

# Check if package is installed
rpm -qa | grep bash

# Get informations from package
rpm -qi package_name

# Get config files from package
rpm -qc package_name

# Get command belongs to which package
which bash
rpm -qf /usr/bin/bash
```

### Create local repo from DVD

[ttps://access.redhat.com/solutions/1355683]

## Acces linux file system

```Shell
ls
cd
pwd
df
du
fdisk

```
