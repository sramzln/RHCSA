# User Administration

```shell
last # recent login
id
# files
# /etc/passwd, man 5 passwd
mark:x:1001:1001:mark,,,:/home/mark:/bin/bash
[--] - [--] [--] [-----] [--------] [--------]
|    |   |    |     |         |        |
|    |   |    |     |         |        +-> 7. Login shell
|    |   |    |     |         +----------> 6. Home directory
|    |   |    |     +--------------------> 5. GECOS
|    |   |    +--------------------------> 4. GID
|    |   +-------------------------------> 3. UID
|    +-----------------------------------> 2. Password
+----------------------------------------> 1. Username

# /etc/shadow
mark:$6$.n.:17736:0:99999:7:::
[--] [----] [---] - [---] ----
|      |      |   |   |   |||+-----------> 9. Unused
|      |      |   |   |   ||+------------> 8. Expiration date
|      |      |   |   |   |+-------------> 7. Inactivity period
|      |      |   |   |   +--------------> 6. Warning period
|      |      |   |   +------------------> 5. Maximum password age
|      |      |   +----------------------> 4. Minimum password age
|      |      +--------------------------> 3. Last password change
|      +---------------------------------> 2. Encrypted Password
+----------------------------------------> 1. Username
# /etc/group
# /etc/gshadow
# /etc/login.defs default user accounts settings (if we change it's for new users)

useradd / groupadd -r # system user
userdel -r / groupdel

vipw / vigr
/etc/skel # can add files automatically for new users

vigr # to edit /etc/group
vigr -s # to edit /etc/gshadow
vipw # edit /etc/passwd
vipw -s # edit /etc/shadow

useradd 
        -u UID
        -g GID 
        -s shell /bin/bash
        -c "user description"
        -m -> create home directory
        -d /home/spiderman -> home folder
        -e expiration date YYY-MM-DD
        -f days after exp. account will be disabled
        spiderman

groupadd -g 60001 group1
groupdel group1

usermod -e 2025-06-08 toto
usermod -aG group user
        -l newlogin # change the username without the home folder
        -L # locks user's password
        -U # unlock user's password
groupmod -g 60002 group1
groupmod -n newgroup group1

# chage
chage [-m mindays] [-M maxdays] [-d lastday] [-I Inactive] [-E Expiredate] [-W warndays] user

chage -l spiderman
passwd spiderman

# Expirer passwd
passwd --expire spiderman
chage --lastday 0 spiderman
id spiderman

# limit su usage
#/etc/pam.d/su

sg # execute command like a group, we are in the group
sg groupe1 -c "cp important.doc /home/groupe1"

# sudoers
# /etc/sudoers
visudo
man 5 sudoers

# /etc/ptofile.d
# /etc/skel copy hidden files to home folder
# /etc/profile, ~/.bash_profile, /etc/profile.d/ system wide configuration,executed at start interactive shell
# /etc/bashrc, ~./bashrc executed interactive shell or script. Files overcharge precedent files.

/etc/login.defs # default user configuration
/etc/security/acces.conf # file to limit access for users
/etc/pam.d/su # modify who can su; account required pam_access.so

SGID # Force all new files/folders created inside a directory to inherit the directory's group ownership (not the creating user’s default group).
useradd test3; echo changeme | passwd --stdin test3
```

## Lab1

Create a new user named newguy. Make sure that user is a member of the users group as a supplementary group. Create a second user named intern. Create a special group named peons, and make both new users members of that group. Assign a GID of 12345 to that group.

```shell
useradd newguy; echo changeme | passwd --stdin newguy
usermod -aG users newguy
useradd intern; echo changeme | passwd --stdin intern
groupadd -g 12345 peons
usermod -aG peons newguy
usermod -aG peons intern
```

## Lab2

Create a new user named senioradm. Set up that user with sudo privileges to run any command as any user, but senioradm should still be required to enter his regular account password before he is allowed to run a command via sudo.

```shell
useradd senioradm; echo changeme | passwd --stdin senioradm
usermod -aG wheel senioradm # or /etc/sudoers senioradm ALL=(ALL) ALL
```

## Lab3

Create a new user named junioradm. Set up that user with privileges to run the fdisk command as root, with the help of sudo. In this case, user junioradm should not be required to enter her regular account password before she’s allowed to run the fdisk command.

```shell
junioradm   ALL=(root) NOPASSWD: /usr/sbin/fdisk
```

## Lab4

Make sure all new users get a copy of the bash subdirectory of the /usr/share/doc directory, including all files therein. Test the result by creating a new user named infouser with the useradd command.

```shell
cp -a /usr/share/doc/bash /etc/skel # -a preserve everything
```

## Lab5

Create a new user named mike. Force the user to change his password after the first login. Ensure that user mike changes his password at most every 30 days, and at the minimum after 7 days from the last password change. New users created on the system must change their password every 30 days.

```shell
chage -d 0 -M 30 -m 7 mike
vi /etc/login.defs
PASS_MAX_DAYS 30
```

## Lab6

In this lab you will explore the differences between locking a user’s password, disabling a user account, and setting a user account’s shell to /sbin/nologin.
Create a user account named testlock and lock the user’s password.
Can the user log in from a terminal? Can you su to the user as a normal user? Can you su to the user as root? Can you open an SSH session as the user?
Unlock the user’s password and disable the account.
Can the user log in from a terminal? Can you su to the user as a normal user? Can you su to the user as root? Can you open an SSH session as the user?
Reenable the account and set the user account’s shell to /sbin/nologin.
Can the user log in from a terminal? Can you su to the user as a normal user? Can you su to the user as root? Can you open an SSH session as the user?

```shell
passwd -l testlock # lock passwd
passwd -S testlock # status
# su as normal user NO
# su as roor YES
# ssh as user NO
passwd -u testlock # unlock passwd

usermod -L testlock # lock user
usermod -U testlock # unlock user
usermod -e 2025-04-08 testlock # disable account
usermod -e "" testlock # enable account
usermod -s /sbin/nologin # change shell for user
```

## Lab7

Create a private directory for a group of engineers designing some galleys. Create a group named galley for the engineers, and name their user accounts mike, rick, terri, and maryam. Enable them to share files for collaboration in the /home/galley directory.

```shell
mkdir /home/galley
groupadd -g 99999 galley
chown nobody:galley /home/galley/
chmod g+rwxs,o-rwx galley/
usermod -aG galley mike,rick,terri,maryam
usermod -aG galley mike
usermod -aG galley rick
usermod -aG galley terri
usermod -aG galley maryam
#groupadd -G 99999 -a mike rick terri maryam
```
