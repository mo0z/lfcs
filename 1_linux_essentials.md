# Linux Essentials

## Installing CentOS 7

create 2 servers: 
* server1.example.com
* server2.example.com  

both with users:
* root / pa55word
* tux / pa55word

set networking:
* adapter 1: NAT Network "NatNetwork" (must first add this through Virtualbox main menu)
* adapter 2: Host-only - in Preferences, ensure host-only network is set-up as follows:
    * Adapter
      * IPv4 Address: 192.168.56.1
      * IPv4 Network Mask: 255.255.255.0
    * DHCP Server
      * Server Address: 192.168.56.100
      * Server Mask: 255.255.255.0
      * Lower Address Bound: 192.168.56.101
      * Upper Address Bound: 192.168.56.254


During install must set Network and hostname:
* Ethernet (enp0s3): on
* Ethernet (enp0s8): on

show IP address
```sh
ip a s
```

network manager command line tool
```sh
nmcli conn show
# to bring up a connection to a specific interface
nmcli conn up enp0s8
# if the network interface is down you will need to bring it up using ifup
ifup enp0s8
```

to ensure interfaces are brought up at boot
```sh
sed -i s/ONBOOT=no/ONBOOT=yes/ /etc/sysconfig/network-scripts/ifcfg-enp0s3
sed -i s/ONBOOT=no/ONBOOT=yes/ /etc/sysconfig/network-scripts/ifcfg-enp0s8
```

update software and install some additional packages, including X (as root) on server 1
```sh
yum update
yum install -y redhat-lsb-core net-tools epel-release kernel-headers kernel-devel
yum groupinstall -y "Development Tools"
yum groupinstall -y "X Window System" "MATE Desktop"
```

service management tool
```sh
# set default run level for graphical environment
systemctl set-default graphical.target
# enter the graphical environment
systemctl isolate graphical.target
```

Reboot server.  This will show an extra option (the latest kernel) at boot time and will use this as default.  Login as "tux"

Within the graphical emulator open terminal then as super user add VBox guest additions
```sh
su -
mount # to view devices
/run/media/tux/VBOX{tab-to-auto-complete}/VBoxL{tab-to-auto-complete}
# reboot once complete
reboot
```

Now you should be able to go full-screen, with Host-f

Right-click on VBOX... on virtual desktop and select "Eject"


## Working at the Command Line

From within virtual machine (server1 GUI), press host+fn+f2 (on Mac), otherwise right-ctrl+f2 (on Windows), to open physical console tty2

Commands
```sh
# to see what terminal I am currently logged on to
tty
# to see who is currently logged on
who
# tux    :0      # graphical environment 
# tux    pts/0   # psuedo terminal 0
# tux    tty2    # physical terminal
```

### Listing files

When we run *ls* to list files we get colouring.  This is because it is being aliased - use the following to see what
```sh
type ls
# ls is aliased to `ls --color=auto'

# to show file types
ls -F

# to clear screen
ctrl-l

# show just the directory, not the contents of that directory
ls -ld
```

### File Types

Everything is a file, even devices such as psuedo terminal

```sh
ls -l /dev/pts/1
# is the same as
ls -l $(tty)
# list block devices (disks and partitions)
lsb
```

Wildcard searching
```sh
# any number of characters
ls -l /dev/sda*
# only one character
ls -l /dev/sda?
# value 1 or 2
ls -l /dev/sda[12]
```

Linux version
```sh
cat /etc/centos-release
cat /etc/redhat-release
lsb_release -d
```

Using rpm package manager
```sh
which lsb_release
# -q query, -f file
rpm -qf /usr/bin/lsb_release
rpm -qf $(which lsb_release)