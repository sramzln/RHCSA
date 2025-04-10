# Linux Security

```shell
## Firewall
# /etc/services, list of ports with services
systemctl status firewalld
systemctl is-active firewalld

firewall-cmd --get-default-zone
firewall-cmd --set-default-zone=internal

firwall-cmd --list-all

# Services
firewall-cmd --add-service=http
firewall-cmd --remove-service=http

# Ports
firewall-cmd --add-port=8080
firewall-cmd --remove-port=8080

## SSH
# qqh,scp,sftp
sshd
ssh-agent
ssh-add
ssh
ssh-keygen
ssh-copy-id -i ~/.ssh/id_key.pub zoltan@192.168.10.119

# Permissions
~/.ssh - 700
private key - 600
piblic key - 644

## SELinux
ls -Z
# /etc/selinux/config
# enforcing,permissive,disabled
# targeted(default),mls
getenforce
setenforce
sestatus

# Selinux Users
getsebool -a
semanage boolean -l
setsebool

# chcon - unitl relabelling
chcon -R -u system_u -t public_content_t /folder1 # -R - recursive
chcon -R -u system_u -t public_content_rw_t /folder1 # -R - recursive
chcon -R --reference /var/ftp /ftp # Utilise folder as reference
retorecon -Rv /ftp

# Restore SELinux file contexts
resorecon -F /ftp # -F - force

semanage fcontext -l # list all contexts
ftp(/.*)? # folder

semanage fcontext -a -t public_content_t '/ftp(/.*)?'
restorecon -RF /ftp
ls -Zdl /ftp

# Temporary
chcon -R --reference /var/ftp /ftp
restorecon -Rv /ftp
# Permanent
semanage fcontext -a -s system_u -t public_content_t '/ftp(/.*)?'
restorecon -Rv /ftp

# Port labeling
semanage port -l # to list
semanage port -l | grp http
semanage port -a -t http_port_t -p tcp 8080

# File: /var/log/audit/audit/log
sestatus
ausearch -m avc -c sudo
ausearch -m avc -ts today
sealert -a /var/log/audit/audit.log

dnf install policycoreutils-gui

# local contexts
/etc/selinux/targeted/contexts/files/file_context.local

semanage boolean -l
getsebool -a # list all boolean values
setsebool -P # set boolean values
```

## Lab1

In this lab, you’ll review the process for disabling and re-enabling SELinux on a system. Review the current status of SELinux with the sestatus command. You can disable SELinux through the /etc/sysconfig/selinux file or through the SELinux
Administration tool.

Do so and reboot the system. Try the sestatus command again. Re-enable SELinux and reboot the system. What happens? Does the process take long? How many times does the system reboot? What would happen if you had to wait for the relabel and the reboot process during a Red Hat exam?

```shell
vi /etc/selinux/conig # pass enabled to disabled and run grubby command
```

## Lab2

In this lab, you’ll set up an Apache web server that you will use for the subsequent lab exercises. Although the configuration of a web server is not part of the RHCSA exam objectives, you may be required to troubleshoot issues related with firewall configuration or SELinux.

1. Log in as root into server1.example.com and confirm that firewalld is running.
2. Install the httpd RPM package.
3. Start the httpd service and ensure that it is enabled to start at boot. If the installation was successful, you should be able to browse the default test web page at <http://localhost>.
4. Check the current default firewall zone. Set the zone to dmz.
5. Add the http service to the default firewall zone.
6. From tester1.example.com, open Firefox and point the browser to the IP address of server1.example.com (i.e., <http://172.16.0.100>, or substitute accordingly the IP address of your server1 machine). If successful, you should see the default test page of the web server
7. Reboot server1.example.com to ensure that your configuration survives a reboot.

```shell
systemctl is-active firewalld.service #1
dnf install -y httpd #2
systemctl enable --now httpd.service #3
curl http://localhost #3
firewall-cmd --set-default-zone=dmz #4
firewall-cmd --get-default-zone #4
firewall-cmd --add-service=http --permanent #5
firewall-cmd --reload
firewall-cmd --list-services #5

```

## Lab3

This lab follows from the web server that you have configured in Lab 2. You will run the web server on a different port.

1. Open the /etc/httpd/conf/httpd.conf file and change the Listen 80 directive to Listen 81.
2. Restart the httpd service.
3. On server1.example.com, open Firefox and point the browser to <http://localhost:81>. What happens?
4. On tester1.example.com, open Firefox and point the browser to <http://172.16.0.100:81> (substitute with the IP address of server1). What happens?
5. Add TCP port 81 to the http service on the firewall and repeat steps 4. What do you see.
6. Finally, reboot server1.example.com, to ensure that your configuration survives a reboot.

```shell
vi /etc/httpd/conf/httpd.conf  # port 81
systemctl restart httpd.service
systemctl status httpd.service
semanage port -l | grep http
semanage port -a -t http_port_t -p tcp 81
systemctl restart httpd.service
reboot
```

## Lab4

This lab is almost identical to Lab 3, but with an additional caveat.

1. Open the /etc/httpd/conf/httpd.conf file and change the Listen directive to Listen 82.
2. Restart the httpd service. What is the problem here? Check the logs and fix it.
3. Add TCP port 82 to the http service on the firewall and repeat restart the service.
4. On tester1.example.com, open Firefox and point the browser to <http://172.16.0.100:82> (substitute with the IP address of server1). What do you see?
5. Reboot server1.example.com to ensure that your configuration survives a reboot.

```shell
vi /etc/httpd/conf/httpd.conf  # port 82
systemctl restart httpd.service
systemctl status httpd.service
semanage port -l | grep http
semanage port -a -t http_port_t -p tcp 82
systemctl restart httpd.service
reboot
```

## Lab5

In this lab, you will continue the configuration of the web server by setting a nondefault location to serve web pages.

1. Open the /etc/httpd/conf/httpd.conf file and change the Listen directive to Listen 80.
2. Restart the httpd service.
3. Create a new default web page:
`# echo "Hello world!" > /var/www/html/index.html`
4. On tester1.example.com, open Firefox and point the browser to <http://172.16.0.100> (substitute with the IP address of server1). What do you see?
5. Create the directory /html.
6. Open the /etc/httpd/conf/httpd.conf file and change the DocumentRoot "/var/www/html" directive to DocumentRoot "/html". Next, add the following lines:

```shell
<Directory "/html">
  Require all granted
  Option Indexes
</Directory>
```

7. Create a new test web page:
`# echo "A new test page" > /html/index.html`
8. Restart the httpd service.
9. On tester1.example.com, open Firefox and point the browser to <http://172.16.0.100> (substitute with the IP address of server1). What do you see? Check the logs and fix the problem.

```shell
vi /etc/httpd/conf/httpd.conf
systemctl restart httpd.service
systemctl status httpd.service

semanage fcontext -a -t httpd_sys_content_t '/html(/.*)?'
retorecon -Rv /html
```

## Lab6

Your task is to perform the following operations on tester1.example.com and server1.example.com:

1. Create a new user named bob on tester1.example.com.
2. Generate an SSH key pair for bob without using a passphrase.
3. Create a new user named alice on server1.example.com. Set the passwords for both bob and alice to “changeme”.
4. Enable bob to SSH into server1.example.com as alice without using a password.
5. Confirm that bob is able to SSH into server1.example.com as alice without being prompted for a password.

```shell
useradd bob && echo changeme | passwd --stdin bob
useradd alice && echo changeme | passwd --stdin alice
su - bob
   ssh-keygen
   ssh-copy-id alice@192.168.10.120
   ssh alice@192.168.10.120
```
