# Fundamental Command_Line Skills

```bash
### Accessing the documentation
command -h
command --help
man command
whatis # Find man pages in the title
apropos # ind man pages in the description
info command # /usr/share/info
pinfo command
# /usr/share/doc

### Basic Commands
touch
cp
ls -l long
   -r reverse
   -t time-sorted
mv
mkdir /rmdir
mkdir folder1/folder2/folder3
grep
sort
sed
diff
awk

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

### PATH variable
echo $PATH
export PATH="/path/new/folder:$PATH" # Add new folder to PATH. For interactive shell add this line in .bashrc, for login shell add to .bash profile
# For all users a /etc/profile.d/custom_path.sh
echo 'PATH="/path/new/folder:$PATH"' | tee -a /etc/profile.d/custom_path.sh
sudo chom +x /etc/profile.d/custom_path.sh

### Permissions and Ownership
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

### Special attributes 
chattr # Change attribute
chattr -a # append only
chattr -i # immutable
chattr -d # disallow backups with dump command
lsattr # List attribute

### umask /etc/profile or /etc/bashrc
umask
# for a directory 777-mask give the permissions
# for a file 666-mask give the permissions

```

## Lab 1

Create a directory named book in your home directory. Then, in the book/ directory, create 19 empty files named Ch01.txt, Ch02.txt, etc., up to Ch19.txt.
Rather than manually creating one file at a time, use brace expansion. This is a shell mechanism that allows you to generate arbitrary strings. For example,

```Shell
touch a{d,c,b}e

touch Ch{01..19}.txt
touch Ch{0,1}{1..9}.txt

rm -rf ~/book/
```

## Lab 2

Create a directory named rhcsa in your home directory. Then, in the rhcsa/ directory, create a file named lab.txt whose first line is the string "Labs for the RHCSA Exam".
Run the date +%F command and append the output to the lab.txt file.
Display the contents of the lab.txt file and count the number of characters in it.
Create a directory named archive inside the rhcsa directory.
Move the lab.txt file into the archive/ directory.
Finally, move the rhcsa/ directory into the /tmp directory.

```Shell
mkdir rhcsa && cd rhcsa
echo "Labs for the RHCSA Exam" > lab.txt
wc -m < lab.txt
wc -m lab.txt
mkdir archive
mv lab.txt archive/
mv ~/rhcsa/ /tmp/
```

## Lab 3

Create a list of all filenames in the /etc directory whose names end with “conf” and save them in the filelist.txt file in your home directory.
How many files have you found?
Next, display all the lines in filelist.txt that contain the string "security".

```Shell
sudo find /etc -type f -name "*.conf" > filelist.txt
cat filelist.txt
wc -l ~/filelist.txt
wc -l < filelist.txt
grep security filelist.txt
```

## Lab 4

Display the third and first fields in the /etc/passwd file, separated by a space (the field separator in /etc/passwd is the : character). Filter the output to show only the lines that start with two digits and are followed by a space character.

```Shell
awk -F: '{print $3" "$1}' /etc/passwd | grep '^[0-9][0-9] '
#-F field separator
```

## Lab 5

As the root user, create a new directory /var/log/rhcsa. Within this directory, create a file named main.log. This file should have the content “A simple logfile”.
Then, make sure the file is owned by the group nobody. Set the permissions so that the root user can both read and write to this file, while every other user has only read permissions. However, the nobody group should not be able to read it.
For the directory /var/log/rhcsa, ensure the root user has full access rights. Contrarily, the directory’s owner group should not have any permissions. All remaining users should be able to access files inside, but they must not be able to list the directory’s contents.

```Shell
su -
mkdir /var/log/rhcsa
touch /var/log/rhcsa/main.log
chown :nobody /var/log/rhcsa/main.log
chmod 604 /var/log/rhcsa/main.log
chmod 701 /var/log/rhcsa/
ls -l /var/log/rhcsa/main.log
ls -ld /var/log/rhcsa/
# Test
cat /var/log/rhcsa/main.log
ls -l /var/log/rhcsa/
```
