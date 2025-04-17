# Software Management

```shell
# rpm
rpm -ivh package # install rpm package
rpm -ivh package --nodeps # dangerous
rpm -U package # upgrade, upgrade package if exist, install if not exist
rpm -F package # freshen, upgrade package if exist
rpm -e package # delete package

rpm -ivh ftp://ftp.exemple.com/pub/package.rpm
rpm -ivh ftp://username:password@ftp.exemple.com:port/path/package.rpm

rpm -qa # list all installed package
rpm -qf # identify package path to file
which systemctl
rpm -qf /usr/bin/systemctl

rpm -qc # list configuration files
rpm -qd # list documentation
rpm -qi # basic information
rpm -ql # list all files
rpm -qR # list dependecies
rpm -qp # package distant

rpm --import /etc/pki/rpm-gpg/RPM-GPG-rehate-release
rpm --checksig -v package.rpm # Validate associates signature
rpm -K package.rpm # Validate associates signature

rpm --verify --file /bin/ls
rpm -aV | grep /usr/sbin
rpm --verify -p package.rpm

rpm -qpi tmux-3.2a-5.el9.x86_64.rpm
rpm -ivh tmux-3.2a-5.el9.x86_64.rpm
rpm -ql tmux

# dnf
subscription-manager register
subscription-manager list --available
subscription-manager attach --pool=id

subscription-manager repos
subscription-manager repos --list-enabled
subscription-manager repos --enable=repoid

dnf repolist
dnf config-manager --dump
# Config files /etc/dnf/dnf.conf, /etc/yum.repos.d/redhat-repo
# Create repo
dnf config-manager --add-repo=https://test.com

vi /etc/yum.repos.d/test.repo
name=baseos
baseurl=file:///
baseurl=https://
baseurl=ftp://
enabled=1
gpgcheck=0

dnf list | grep packagename
dnf search
dnf info

dnf check-update
dnf list updates

dnf clean # flush cache

dnf group list
dnf group list hidden # to show all groups
dnf group info "Fedora Packager"
dnf group install --with-optional "emote Desktop Clients"
dnf group install "Fedora Packager" -x git
dnf group remove

dnf config-manager
dnf download

dnf module list
dnf module install nodejs:18
dnf module install nodejs:18/minimal

dnf provides /etc/passwd # identifies the package associate with file
```

## Lab 1

In this lab, you will use the rpm command to query RPM packages.

1. On server1.example.com, create a list of all the packages installed on the system and save it to the /root/pkgs.txt file.
2. How many packages are installed on your system?
3. Which package does the file /etc/sestatus.conf belong to?
4. What is the zlib package used for? Which files does it provide?
5. Mount the RHEL 9 DVD. Find the tmux RPM in the BaseOS/Packages/ directory. What is this package used for? Which files does it provide?

```shell
rpm -qa > /root/pkgs.txt #1
rpm -qa | grep wc -l #2
rpm -qf /etc/sestatus.conf #3
rpm -qil zlib-1.2.11-40.el9.x86_64 #4
rpm -qipl /run/media/zoltan/RHEL-9-4-0-BaseOS-x86_64/BaseOS/Packages/tmux-3.2a-5.el9.x86_64.rpm #5
```

## Lab 2

Configure a repository. You must satisfy the following requirements:

1. Configure the repository in a file named sql.repo.
2. Make the name of the repository sql-tools.
3. Set the URL of the repository to https://packages.microsoft.com/rhel/9/prod/.
4. Enable GPG checks, using the key at https://packages.microsoft.com/keys/microsoft.asc.
After configuring the repository, search for and install the mssql-tools package.

```shell
[sql-tools]
name="sql-tools"
baseurl=https://packages.microsoft.com/rhel/9/prod/
enabled=1
gpgcheck=1
gpgkey=https://packages.microsoft.com/keys/microsoft.asc

dnf search mssql-tools
dnf install -y mssql-tols
```

## Lab 3

In this lab, you’ll be identifying a potential security problem. Run the following command to change the modified date of the SSH daemon:

`# touch /usr/sbin/sshd`

Now, imagine that you don’t know that this file was modified. You’ve been given a tip by security staff that the problem is related to a binary file that starts a server.
Identify the binary file that was modified by inspecting all the contents of the /usr/sbin directory.
Assume that the problem was more serious, and that the content of the binary was modified. To fix the problem, uninstall and reinstall the associated package.

```shell
rpm -aV | grep /usr/sbin # check integrity
rpm -qf /usr/sbin # which package belongs this file
dnf reinstall openssh-server
```

## Lab 4

This and the following labs require having the server1.example.com system subscribed to Red Hat Subscription Management, as explained on Exercise 4-2. In this lab, you’ll examine what happens when you run an update to upgrade all the packages available. Before you start, run the following command to clear the cache, to enable the full set of messages:

`# dnf clean all`

Run the following command and review the output:

`# dnf update`

If a lot of updates are available, this process may take some time. If you want to download and install the updates, use the -y switch, which answers “yes” to all prompts. Save the output to a file. The complete command becomes

`# dnf update -y > update.txt`

After the download and installation is complete, review the update.txt file. Note how it loads information from the repositories, downloads headers, and resolves dependencies.
Once dependencies are resolved, examine where the downloads come from. Note how some packages are installed and how others are updated.

```shell
dnf update -y > update.txt
```

## Lab 5

Find information about group “Graphical Administration Tools.” How many packages does it include? Are they all optional?
Then, install the wireshark package.

```shell
dnf group info “Graphical Administration Tools”
dnf install -y wireshark
```

## Lab 6

Install the module stream postgresql 15, using the client profile.

```shell
dnf module install postgresql:15/client
dnf module list
```
