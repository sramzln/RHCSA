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
```
