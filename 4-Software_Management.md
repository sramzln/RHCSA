# Software Management

```Bash
### rpm
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
rpm --checksig -v package.rpm

rpm --verify --file /bin/ls
rpm --verify -p package.rpm

rpm -qpi tmux-3.2a-5.el9.x86_64.rpm
rpm -ivh tmux-3.2a-5.el9.x86_64.rpm
rpm -ql tmux

### dnf
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
name=basos
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
```
