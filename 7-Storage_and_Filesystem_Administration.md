# 7-Storage and Filesystem Administration

```shell
/etc/fstab, /etc/mtab
blkid # universally unique identifiers
df -h # available filesystem with mount points. Power of 1024
df -H # power of 1000
# 1GB=1 000 000 * bytes, 1GiB = 1024*1024*1024 (1073741824) byte
mount # mounted fs with mount options
mount -o loop rhel.iso /mnt
mount -o remount,ro /boot
findmnt # mounted fs in tree format

fdisk
fdisk -l # list disks
fdisk /dev/sda
    m - help
    q - quit
bc # to use for calculatrice ou python

# change the /etc/fstab file
mkfs.ext4
mkfs.xfs
mkswap / swapon # cat /proc/swaps
lsblk -f # fs-type

# repair
umount /var # maybe rescue mode needed
xfs_repair/dev/sdb1 #fsck.ext4,fsck.ext3
mount /var

## LVM
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

## NFS
mount -t nfs server1.example.com:/pub /share
#/etc/fstab, /etc/nfs.conf
server1:/pub /share nfs rsize=65536,wsize=65536,hard 0 0

dnf install nfs-utils -y

## Automounter

```
