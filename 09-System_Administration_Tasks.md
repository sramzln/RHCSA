# System Administration Tasks

```shell
ps aux
ps -u bob
ps exl # with env variables
ps axl # PID,PPID
ps -efZ # with SELinux context

top
du
df -h 
uptime
free # memory usage
lsof # liste opened files
tcpdump # network traffic
ss
ps -ef # every process
ps -ejH # every process tree
vmstat # memory statistics
iostat
iftop # network communication

sar # dnf install sysstat

nice # start with the nice value
renice # change nice value of a running process

nice -n 19 ./myscript.sh
renice -n 10 process # as sudo

kill PID
killall PID
killall httpd
top + k
kill -l
man 7 signal

# systemctl
systemctl
systemctl --version
systemctl --all
systemctl status/start/stop/restart/enable/disable application.service
systemctl mask/unmask -> disable service completly
systemctl list-unit-files

### tuned
dnf install tuned
tuned-adm list
tuned-adm active # to show which profile is active
tuned-adm profile balanced # To change profile
tuned-adm recommend # show the recommended profile
tuned-adm off

### Archive and compression
gzip2 test.doc
bzip2 test2.doc

gunzip test.doc # gzip2 -d test.doc.gz
bunzip test2.doc # bzip2 -d test.doc.bz2

tar czvf --selinux home.tar.gz /home
tar xzvf --selinux home.tar.gz
    -c create
    -z compress
    -x extract
    -j compress with bzip2

### Cron and at
## Cron
# folder: /var/spool/cron/, /etc/cron.d/
# file: /etc/anacrontab, /etc/crontab
cat /etc/crontab # to see exemple job definition
% # new lines
crontab -e # edit
        -l # list
        -r # remove
        -u # user

sudo vi /etc/cron.monthly/script.sh
    #!/bin/bash
    su - student -c "/home/student/backup.sh"
chmod u+x script.sh

sudo run-parts /etc/cron.monthly

# Verification
/var/log/cron or 
/var/log/messages

## at
at now + 10 minutes
at now + 2 hours
at now + 1 day
at> /home/zoltan/script.sh
CTRL+D

at now + 1 day < /home/zoltan/script.sh
echo '/bin/bash /home/zoltan/script.sh' | at 1430 

atq # list the job
atrm job_number # remove at job

## Security
# /etc/cron.allow,/etc/cron.deny
# /etc/at.alow,/etc/at.deny

## Bash Scripts
$((expression)) # arithmetic expressions

$(command) 
day=$(date +%d)
echo $day

# Parameters
$? # last exit code
$0..$9 # positional parameters
$# # number of command line arguments
$$ # PID current shell
getent passwd bob | awk -F: '{print $6}'
getent passwd bob | cut -f 6 -d ":"

## Parameters expansion

# substitution if variable not exist
${parameter:-word}
ch=Hello
echo ${ch:-World} 
echo ${ch2:-World}

${parameter/pattern/string} # replace longest match pattern with string
${parameter#word} # remove shortest occurence of word staer at the begining
${parameter##word} # remove lonest occurence of word start at the begining
${parameter%word} # remove shortest occurence of word staer at the end
${parameter%%word} # remove lonest occurence of word start at the end

## Test operators
sting1 = string2
string1 != string2
integer1 -eq integer2
integer1 -ne integer2
integer1 -ge integer2
integer1 -gt integer2
integer1 -le integer2
integer1 -lt integer2
-d folder # true if is a directory
-e file # true if is a file exist
-f file # true if is a regular file
-r file # true if is a file with read permissions
-w file # true if is a file with write permissions
-x file # true if is a file with execute permissions

## Log
/etc/rsyslog.conf
/etc/logrotate.conf

/var/log/messages
logger "Test log message"

lastlog # to know the last login for user

logrotate --force /etc/logrotate.d/mylog # force to execute logrotate

/etc/cron.daily/logrotate
systemctl list-timers # if present, don't need cronjob for logrotate

# Journalctl
cat /etc/systemd/journald.conf # can modify journal storage; volatile,persistent or auto
/var/log/journal # if folder exists, auto use permanent storage, if not volatile
journalctl -p err
journalctl --since 04:00 --until 10:59
journalctl _UID=1000
```

## Labs 1

Install the tuned RPM package and ensure that is started and enabled at boot.
Find the current active tuned profile and switch the system to the balanced profile.
Reboot the system and confirm that the balanced profile is the active one.
Finally, restore the default profile.

```shell
dnf install -y tuned
systemctl enable --now tuned

tuned-adm active
tuned-adm list profiles
tuned-adm profile balanced
```

## Labs 2

As the root user, create cron jobs that change the login message for users at the text console. To do so, you’ll want to change the content of /etc/motd. Make sure that people who log in at different times get appropriate messages:
If users log in between 7 a.m. and 1 p.m., create the login message “Coffee time!”
If users log in between 1 p.m. and 6 p.m., create the login message “Want some ice cream?”
If users log in between 6 p.m. and 7 a.m., create the login message “Shouldn’t you be doing something else?”

```shell
crontab -e
0 7 * * * /usr/bin/echo "Cofee time!" > /etc/motd
0 13 * * * /usr/bin/echo "Want some ice cream?"  > /etc/motd
0 18 * * * /usr/bin/echo "Shouldn't you be doing something else?" > /etc/motd
```

## Labs 3

In this lab, you’ll set up an at job as the root administrative user, to save a list of currently installed RPMs in the /root/rpms.txt file. That job will be run once, 24 hours from now. If you want to verify your work, set up a second at job with the same commands to start five minutes from now.

```shell
# at now + 1 day
warning: commands will be executed using /bin/sh
at> /usr/bin/rpm -qa > /root/rpms.txt        
at> <EOT>
job 9 at Sat Apr 12 15:53:00 2025
```

## Labs 4

In this lab, you’ll set up a cron job to back up the /etc directory every Saturday at 2:05 a.m. You should ensure that SELinux contexts are preserved. You are not allowed to edit the root crontab. The backup should be saved in a gzipped-tar archive format in the /tmp directory, using the filename etc-backup-MMDD.tar.gz, where MMDD is the current date in numeric format. Extract one of the generated backup files, to confirm that SELinux contexts are preserved.

```shell
vi /etc/cron.d/backup
SHELL=/bin/bash
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root
01 * * * * root /usr/bin/tar --selinux -czf /tmp/etc-backup-$(/bin/date +"\%m\%d\%I\%M").tar.gz /etc/
```

## Labs 5

In this lab, you'll create a script that can be used to backup any given directory to a specified destination directory. The script should:

1. Accept two arguments: the first argument represents the source directory to be backed up, while the second argument represents the destination directory where the backup will be stored. If the specified directory doesn’t exist, the script should create it.
2. Name the backup file in the format backup-MMDDHHSS.tar, where MMDDHHSS is the current month, day, hour, and second.
3. Check the number of arguments passed to the script. If they are not equal to two, the script should display a usage message and exit.
4. Confirm that the first argument passed to the script represents an existing directory. If not, the script should display an error message and exit.
5. Confirm that the second argument is a directory. If it doesn't exist, create it.
6. For this lab, do not consider the scenario where the second argument is a file but not a directory.

```shell
#!/bin/bash
DATE=$(/bin/date +"%m%d%I%M%m")

if [ "$#" -ne 2  ]; then
   echo "1st parameter is source folder, 2nd parameter is destination folder."
   exit 1
fi

if [ ! -d "$1" ]; then
   echo "The source parameters is not a folder"
   exit 1
fi

if [ ! -d "$2" ]; then
   mkdir -p "$2"
   exit 1
fi

/usr/bin/tar -cf "$2/backup-$DATE.tar" "$1"
```
