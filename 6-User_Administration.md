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
/etc/skel

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
# /etc/profile, ~/.bash_profile executed at start interactive shell
# /etc/bashrc, ~./bashrc executed interactive shell or script

```
