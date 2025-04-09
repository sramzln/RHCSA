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


## Automounter
# Server /etc/exports
/shared 192.168.10.120(ro,sync)
exportfs -arv

# On client
dnf install nfs-utils autofs -y

showmount -e 192.168.10.119

vi /etc/auto.master # don't need to modify
vi /etc/auto.misc
    /data /etc/auto.misc # /data will be created automatically. It will be accesible /misc/data
vi /etc/auto.misc
    data            -rw,soft,intr           192.168.10.119:/shared
```

## Labs1

Add a new virtual drive of at least 2GiB in size to the server1.example.com VM. In this lab, you’ll create one new partition of size 1GiB using parted, format it to the XFS filesystem, and configure it on the /test1 directory in /etc/fstab so that the new partition is properly mounted the next time you boot Linux. 

You’ll also create a second new partition of size 2GiB using fdisk, format it, and configure it as additional swap space in /etc/fstab so that space is also used the next time you boot Linux. Additionally, use UUIDs in /etc/fstab.

```shell
# 1st partition
lsblk
mkfs.xfs /dev/sdb1
lsblk -f
mkdir /test1
vi /etc/fstab 
systemctl daemon-reload 
mount -a
#2nd partition
fdisk /dev/sdb
mkswap /dev/sdb2
vi /etc/fstab 
systemctl daemon-reload 
mount -a
swapon /dev/sdb2
free -m
# Verification
reboot
df -h
cat /proc/swaps
```

## Labs2

In this lab, you’ll set up a formatted logical volume. If you use the partitions created in Lab 1, don’t forget to delete or at least comment out any associated settings in the /etc/fstab file.

Create a new VG of 1000MiB using a new partition that you created on the /dev/sdb drive. Configure an LV of size 900MiB. Mount the resulting LV on the /test2 directory, and confirm the result with the mount and df commands. Name the VG volgroup1 and name the LV logvol1.

Set up the LV on the /test2 directory in the /etc/fstab file, formatted to the XFS filesystem. Use the UUID for the associated logical volume device in /etc/fstab.

```shell
lsblk
pvcreate /dev/sdc1
lsblk
pvs
vgcreate VG /dev/sdc1
vgs
lvcreate -L 900M VG -n LV1
lvs
mkfs.xfs /dev/mapper/VG-LV1 
lsblk -f
vi /etc/fstab 
systemctl daemon-reload 
mkdir /test2
mount -a
lsblk
reboot
```

## Labs3

In this lab, you’ll continue the work done in Lab 2, expanding the space available to the formatted LV closer to the capacity of the VG. For example, if you were able to follow the size guidelines in Lab 2, use appropriate commands to increase the space available to the LV from 900MiB to 950MiB. Don’t delete any of the contents of the filesystem during the resize operation.

Just one hint: it’s far too easy to skip steps during the process.

```shell
lvextend -L +50M /dev/VG/LV1
xfs_growfs /dev/VG/LV1
```

## Labs4

Before starting this lab, remove any existing volumes created on the /dev/sdb drive. Add a new virtual drive to the virtual machine. Then, set up two PVs on the two drives, such as /dev/sdb and /dev/sdc, using the entire size of the drives. Set up a new VG named vg01 using the PVs that you have just created, with a PE size of 2MB.

Configure a new LV named lv01 whose size should be 800 logical extents. (How many MB do the LEs correspond to?) Format the LV to the ext4 filesystem and set it up on the /test4 directory in the /etc/fstab file. Use the LV device name in /etc/fstab.

```shell
pvcreate /dev/sdb /dev/sdd
vgcreate -s 2M VG2 /dev/sdb /dev/sdd
vgdisplay # to check
lvcreate -l 800 -n lv01 VG2
mkfs.ext4 /dev/VG2/lv01
blkid
vi /etc/fstab
systemctl daemon-reload
mount -a
```

## Labs5

In this lab, you’ll configure the automounter to automatically mount an NFS filesystem. While the configuration of an NFS server is not covered in the RHCSA exam, you need an NFS server to practice with NFS mounts. So, the steps included in this lab are designed to help you set up a simple shared NFS directory on one system, with connections allowed from a second system. If you’ve set up the systems described in Chapter 1, the first system would be server1.example.com and the second system would be tester1.example.com.

1. On the first system, back up your current /etc/auto.master and /etc/auto.net configuration files.
2. Install NFS server configuration files with the following command:
`# dnf install nfs-utils`
3. Share the /tmp directory by adding the following line to /etc/exports (be careful not to include extra spaces):
/tmp *(ro)
4. Activate the NFS service, and set a rule on the zone-based firewall with the following commands. (Firewalls are described in Chapter 8.)
`# systemctl restart nfs-server`
`# firewall-cmd --permanent --add-service=nfs --add-service=rpc-bind --add-service=mountd`
`# firewall-cmd --reload`
5. Record the IP address of the local system, as shown by the ip addr show command. If it’s the server1.example.com system described in Chapter 1, it should be 172.16.0.100; but another IP address is okay too.
6. Go to another RHEL 9 system, such as the tester1.example.com system described in Chapter 1. Install the nfs-utils RPM package.
7. Now you can configure the local automounter to mount the shared NFS directory, using the techniques described in the chapter.

```shell
dnf install nfs-utils autofs -y

showmount -e 192.168.10.119

vi /etc/auto.master # don't need to modify
vi /etc/auto.misc
    /data /etc/auto.misc # /data will be created automatically. It will be accesible /misc/data
vi /etc/auto.misc
    data            -rw,soft,intr           192.168.10.119:/shared
```
