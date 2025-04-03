# Managing Basic Networking

```Shell
### Network commands
ping IP
ping6 -I interface IP

traceroute # UDP by default
traceroute -n # Display IP address rather than hostname
traceroute -I # ICMP
traceroute -T # TCP

### Networking, hostname
ip addr show
ip addr show ens18

ip addr add 172.16.22.10/16 dev eth0
ip link set up eth0
ip link set down eth0

ip route # default routes and ip addresses
ip neigh # ARP

# nmcli
nmcli device status # systemctl status NetworkManager
nmcli device show
nmcli connection show 
nmcli connection show ens19 # to see all parameters

# Configure static IP
nmcli device
nmcli connection modify ens18 ipv4.addresses 192.168.10.121/24
nmcli connection modify ens18 ipv4.gateway 192.168.10.254
nmcli connection modify ens18 ipv4.method manual
nmcli connection modify ens18 ipv4.dns 192.168.10.117,1.1.1.1,1.0.0.1
nmcli c m ens18 ipv4.dns-search "exemple.com"
nmcli connection up ens18
nmcli connection down ens18
nmcli connection reload ens18
nmcli con mod ens192 ipv4.addresses "" ipv4.gateway ""
nmcli con mod "Default1" connection.autoconnect yes

# Configure secondary IP
nmcli device status
nmcli connection show --active
nmcli connection modify ens18 +ipv4.addresses 192.168.10.122/24
nmcli connection reload

nmcli con add con-name Net type ethernet ifname eth0 ip4 192.168.10.15/24 gw 192.168.10.1
nmcli con mod Net ipv4.dns 8.8.8.8

# nmtui
nmtui

### DNS resolution
# /etc/hostname
hostnamectl
hostnamectl set-hostname hostname2

# /etc/resolv.conf # DNS servers, DNS search
# don't edit directly

# /etc/nsswitch.conf
# DNS resolution order
authselect # modify nsswitch.conf

### SSH Connection
dnf install openssh-server
ssh-keygen -t ed25519 - C "Zoltan key"
ssh-copy-id -i homelab_ed15519.pub zoltan@192.168.10.120
ssh-add ~/.ssh/homelab_ed15519

ssh -l zoltan 192.168.10.120
ssh zoltan@192.168.10.120
ssh -X # X11 forwarding
ssh -Y # trusted X11 forwarding

scp file zoltan@server1:/home/zoltan/
scp -r folder zoltan@server1:/home/zoltan/ # copy to server
scp zoltan@server1:/home/zoltan/file /home/zoltan # copy from server
```

## LAB

Lab 1

To prepare the server1.example.com system for this lab, download the file ch03lab01.sh from the companion website, copy the file to server1 using scp, and run the script as root:
    # chmod +x ch03lab01.sh
    # ./ch03lab01.sh
Don’t look try to understand what the script does. Just run the script. Then, reboot the system.
Log in to the system, troubleshoot network connectivity, and fix the configuration.
A backup of the network configuration file is available on your system in the /root/backup directory.

```Shell
nmcli con mod ens18 -ipv4.address 192.168.1.100/24
nmcli con reload ens18
nmcli con mod ens18 ipv4.method auto
nmcli con down ens18
nmcli con up ens18
```

Lab 2

Repeat the same steps as in Lab 1, but this time use the ch03lab02.sh file.

```Shell
nmcli connection show ens18 | grep autoconnect
connection.autoconnect: no

nmcli connection modify ens18 connection.autoconnect yes
systemctl restart NetwokManager
nmcli con show --active
```

Lab 3

Repeat the same steps as in Lab 1, but this time use the ch03lab03.sh file.

```Shell
cat /etc/resolv.conf # Blank
ncmli con mod ens18 ipv4.dns 192.168.10.117,1.1.1.1,1.0.0.1
ncmli con down ens18
nmcli con up ens18
```

Lab 4

In this lab, you’ll set up an /etc/hosts file for the different systems on the local network. The instructions in this lab are based on the server1 and tester1 systems described in Chapter 1.

1. Back up the /etc/hosts file to an appropriate directory, such as /root.
2. Open the /etc/hosts file. You’ll probably see IPv4 and IPv6 entries for their respective loopback hostnames and addresses. That can serve as a model for the other entries that you’ll make in this file.
3. When all the noted systems are running, test the result. Run the ping command, first on each IP address in /etc/hosts, and then on each hostname in /etc/hosts.

```Shell
# vi /etc/hosts
192.168.10.120 node2
```

Lab 5

In this lab, you’ll use the nmtui tool. But before you do so, remember to back up appropriate configuration files. As this is a console tool, you’ll have to use the TAB and ENTER or SPACEBAR keys to make selections.

1. Back up the network configuration files:
    `# mkdir /root/backup 2>/dev/null`
    `# cp /etc/NetworkManager/system-connections/* /root/backup`
2. Open the nmtui configuration tool from a command line with the command of the same name.
3. In the console menu that appears, select Edit a Connection and press ENTER.
4. In the Select A Device menu that appears, select the connection profile and press ENTER.
5. Make a change in the current configuration. Assuming a static IP address configuration for one of the systems described in Chapter 1, you should be able to make a minor change in theIP address. For example, you could change the server1.example.com IPv4 address from 172.16.0.100 to 172.16.0.101. Highlight OK and press ENTER.
6. You’re taken back to the connection screen. Highlight the Back option and press ENTER.
7. You’re taken back to the initial screen. Highlight Activate a Connection and press ENTER.
8. In the next screen, deactivate and reactivate the connection, then highlight the Back option and press ENTER.
9. Make sure Quit is highlighted and press ENTER.
10. Check the IP configuration with the ip addr show command.
11. Use the diff command to analyze the difference between the configuration files in the /etc/NetworkManager/system-connections and /root directories.
12. To restore the original configuration, restore the connection file to the /etc/NetworkManager/system-connections directory and deactivate/activate the connection.

```Shell
nmtui
```

Lab 6

Repeat the process described in Lab 5 with the Network Connection Editor tool. To open it, run nm-connection-editor from a GNOME terminal.

```Shell
nmtui
```
