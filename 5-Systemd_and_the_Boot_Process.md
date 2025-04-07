# Systemd and the Boot Process

```shell
### Emergency boot
# At boot tape e and add at the end of vmlinuz line
systemd.unit=rescue.target # mount fs in rw mode
systemd.unit=emergency.target # mount only root fs in ro mode
systemd.unit=multi-user.target

systemctl reboot / poweroff / halt
openssl rand -base64 16 | tr -d '/+='

### Reset root password
# When redhat start
# /boot/grub2/grubenv : menu_auto_hide=1
grub2-editenv - unset menu_auto_hide
# At boot menu
tape e
# add at the end of linunx line
rd.break
mount -o remount,rw /sysroot
chroot /sysroot
passwd
touch /.autorelabel
exit exit

ls -l /etc/systemd/system/default.target
systemctl get-default

### Modify system bootloader
grub2-mkconfig
grub2-mkconfig -o /boot/grub2/grub.cfg

vi /etc/default/grub
grub2-set-default 1 # default kernel to boot

grub2-install # reinstall grub2

grub>
insmod lvm
ls
search.file /grub2/grub.cfg
cat (hd0,msdos1)/grub2/grub.cfg
cat (lvm/rhel-root)/etc/fstab

set root=(lvm/rhel-root)
linux (hd0,msdos1)/vmlinuz root=/dev/mapper/rhel-root #use tab to autocomplete
initrd (hd0,msdos1)/initramfs.img #use tab to autocomplete

grub2-mkconfig -o /boot/grub2/grub.cfg

keymap (hd0,gpt1)/boot/grub2/layouts/fr.gkb

# systemd
systemctl list-units --type=service --all
systemctl list-units --type=target --all
systemctl list-unit-files

systemctl get-default
ls -l /usr/lib/systemd/system/runlevel?.target

systemctl list-dependencies graphical.target
systemctl list-dependencies sshd.service

systemctl isolate multi-user-target # switch between targets
systemctl mask sshd.service # can't be started accidentally

systemd-analyze time
systemd-analyze blame
journalctl -b 0 # log messages from last boot

journalctl -p warning

systemd-cgls # cgroups hierarchy, correspondance with systemd units

### Time zone configuration
timedatectl
timedatectl set-timezone Europe/Paris

timedatectl set-ntp yes # activate ntp
vi /etc/chrony.conf # to modify ntp servers
chronyc sources -v # to see ntp servers
systemctl start chronyd
```

## Lab1

This lab is focused on targets and the boot process. You’ll boot a system into the emergency target, with a full display of all boot messages. You’ll then set up the system to boot into the multi-user target by default.

1. Power up the server1.example.com system. During the boot process, when you see the following message (the operating system name and version number may vary), press a key: The selected entry will be started automatically in 5 seconds....
2. Press e to edit the current menu entry.
3. Scroll down to locate the line starting with linux.
4. What do you need to change and add to that command line to boot into the emergency target?
5. Make the required changes and then proceed with booting into the emergency target.
6. Reboot the system into the graphical target. What’s the default target?
7. Change the /etc/systemd/system/default.target symbolic link so that the system boots normally into the multi-user target.
8. Reboot the system. How do you confirm that the changes worked?
9. Restore the original default target.

```shell
# 4 add: systemd.unit=emergency.target, delete: quiet
systemctl get-default #5
systemctl set-default multi-user.target
systemctl isolate graphical.target #6

cd /etc/systemd/system #7
ln -s /usr/lib/systemd/system/multi-user.target default.target #7
```

## Lab2

In this lab you’ll change the root administrative password. But here’s a twist: assume that you don’t know the current value of that password. What do you do?

```shell
# When redhat start
# /boot/grub2/grubenv : menu_auto_hide=1
grub2-editenv - unset menu_auto_hide
# At boot menu
tape e
# add at the end of linunx line
rd.break
mount -o remount,rw /sysroot
chroot /sysroot
passwd
touch /.autorelabel
exit exit
```

## Lab3

In this lab, you’ll set the timeout of GRUB 2 to 10 seconds. Before getting started, it’s best to back up the file. For example, the following command backs up the file to the root user’s home directory (/root):

`cp /boot/grub2/grub.cfg ~`

As a bonus task, change the configuration of GRUB 2 to enable verbose boot messages at boot. To prove the result, reboot the system.

```shell
vi /etc/default/grub
# modify GRUB_TIMEOUT : 10
# modify GRUB_CMDLINE_LINUX : delete quiet

grub2-mkconfig -o /boot/grub2/grub.cfg
```

## Lab4

Log in to the server1.example.com VM and take the following steps:

1. Log in to the root account. Execute the following command:

`mv /boot/grub2/grub.cfg /root/` 

Note that this will make the system unbootable.

2. Reboot the system.
3. When you see the grub> prompt, use the skills described in this chapter to identify the drive and partition with the /boot directory. Where applicable, take advantage of the command completion features at the grub> prompt. That’s especially useful to avoid typos when typing the file paths that follow the linux and initrd commands.
Remember that the top-level root directory is specified by the root directive with the kernel command line.
4. After entering the location of the initial RAM disk, run the boot command at the grub> prompt.
5. If your efforts are successful, the system will boot normally. In the “Lab Answers” section, you’ll see how to restore the backed-up GRUB 2 configuration file.
6. If your efforts are not successful, boot the system from the installation DVD and select Troubleshooting, as described in the main body of the chapter.

```shell
grub>
insmod lvm
ls
search.file /grub2/grub.cfg
cat (hd0,msdos1)/grub2/grub.cfg
cat (lvm/rhel-root)/etc/fstab

set root=(lvm/rhel-root)
linux (hd0,msdos1)/vmlinuz root=/dev/mapper/rhel-root #use tab to autocomplete
initrd (hd0,msdos1)/initramfs.img #use tab to autocomplete

grub2-mkconfig -o /boot/grub2/grub.cfg
```

## Lab5

Log in to the server1.example.com VM and take the following steps:

1. Set the current time zone to America/Chicago.
2. Reconfigure chronyd to synchronize the time from time.google.com.
3. Confirm that your changes are working.

```shell
timedatectl set-timezone America/Chicago
# /etc/chrony.conf
pool time.google.com iburst
systemctl restart chronyd
timedatectl
chronyc sources -v
```

## Lab6

Log in to the tester1.example.com VM and take the following steps:

1. Ensure that journal log files are written persistently on disk.
2. Reboot the system and save the journal log messages from the boot before the last one to the file /root/journal-beforelast.log.

```shell
mkdir /var/log/journal
journalctl --flush
reboot
journalctl -b 1 > /root/journal-beforelast.log
```

## Lab7

Log in to the tester1.example.com VM and complete the following tasks:

1. Stop kdump and ensure that it does not start at boot.
2. Configure the rhcd service so that it starts at boot.
3. Reboot the system and verify your changes.

```shell
systemctl stop kdump
systemctl disable kdump
systemctl eanble rhcd
reboot
systemctl is-enabled kdump rhcd
```
