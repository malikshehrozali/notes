# <center style="font-size:5rem; color:orange">Linux</center>

There are five main pillers of Hacking.

- General Computing
- Networking
- Programming & Scripting (Bash Scripting)
- System Administration & Security
- Linux
  Here we will talk about Linux.

## Table of contents

1.[Linux Commands](#linux-commands) 2.[Linux Packages](#linux-packages) 3.[Linux Services](#linux-services) 4.[Linux Filesystem](#linux-filesystem) 5.[Linux Permissions](#linux-permissions) 6.[Linux Networking](#linux-networking) 7.[Linux Networking Commands](#linux-networking-commands) 8.[Linux Networking Configuration](#linux-networking-configuration) 9.[Linux Networking Configuration Commands](#linux-networking-configuration-commands) 10.[Linux Networking Configuration Files](#linux-networking-configuration-files) 11.[Linux Networking Configuration Files Commands](#linux-networking-configuration-files-commands)

## Linux Commands

```shell
ls // list files
cd // change directory
mkdir // make directory
rm // remove file
rmdir // remove directory
cp // copy file
mv // move file
cat // concatenate files
echo // print text
pwd // print working directory
whoami // print user name
reboot // reboot the system
```

## Linux Packages

```shell
sudo apt update // update the package
sudo apt upgrade // upgrade the package
sudo apt install // install the package
sudo apt remove // remove the package
sudo apt search // search the package
```

## Linux Services

```shell
sudo systemctl start // start the service
sudo systemctl stop // stop the service
sudo systemctl restart // restart the service
sudo systemctl status // check the status of the service
sudo systemctl enable // enable the service
sudo systemctl disable // disable the service
```

## Linux Filesystem

```shell
df // display free disk space
du // display disk usage
free // display free memory and swap
df -h // display free disk space in human readable format
du -h // display disk usage in human readable format
free -h // display free memory and swap in human readable format
```

## Linux Permissions

```shell
chmod // change file permissions
chown // change file owner
chgrp // change file group
```

## Linux Networking

```shell
ifconfig // display network interface information
ip // display network information
route // display routing table
netstat // display network statistics
ping // ping a host
traceroute // trace a route to a host
nslookup // query name server
hostname // display or set the host name
```

## Linux Networking Commands

```shell
ifconfig // display network interface information
ip // display network information
route // display routing table
netstat // display network statistics
ping // ping a host
traceroute // trace a route to a host
nslookup // query name server
hostname // display or set the host name
```

## Linux Networking Configuration

```shell
/etc/network/interfaces // network interface configuration file
/etc/hosts // host name configuration file
/etc/resolv.conf // DNS configuration file
/etc/hostname // host name configuration file
/etc/hosts.allow // host name configuration file
/etc/hosts.deny // host name configuration file
/etc/sysctl.conf // system configuration file
/etc/sysctl.d // system configuration file
/etc/ssh/sshd_config // ssh configuration file
/etc/ssh/ssh_config // ssh configuration file
/etc/ssh/sshd_config // ssh configuration file
/etc/ssh/ssh_config // ssh configuration file

// systemd configuration files

systemctl // systemd configuration file
systemd // systemd configuration file

// systemd service files

service // systemd service file
services // systemd service file

// systemd unit files

unit // systemd unit file
units // systemd unit file
```

## Linux Networking Configuration Commands

```shell
sudo systemctl start // start the service
sudo systemctl stop // stop the service
sudo systemctl restart // restart the service
sudo systemctl status // check the status of the service
sudo systemctl enable // enable the service
sudo systemctl disable // disable the service
```

## Linux Networking Configuration Files

```shell
sudo nano /etc/network/interfaces // edit network interface configuration file
sudo nano /etc/hosts // edit host name configuration file
sudo nano /etc/resolv.conf // edit DNS configuration file
sudo nano /etc/hostname // edit host name configuration file
sudo nano /etc/hosts.allow // edit host name configuration file
sudo nano /etc/hosts.deny // edit host name configuration file
sudo nano /etc/sysctl.conf // edit system configuration file
sudo nano /etc/sysctl.d // edit system configuration file
sudo nano /etc/ssh/sshd_config // edit ssh configuration file
sudo nano /etc/ssh/ssh_config // edit ssh configuration file
sudo nano /etc/ssh/sshd_config // edit ssh configuration file
sudo nano /etc/ssh/ssh_config // edit ssh configuration file

    // systemd configuration files

sudo nano /etc/systemd/system.conf // edit systemd configuration file
sudo nano /etc/systemd/system.conf // edit systemd configuration file

        // systemd service files

sudo nano /etc/systemd/system.service // edit systemd service file
sudo nano /etc/systemd/system.service // edit systemd service file

            // systemd unit files

sudo nano /etc/systemd/system.unit // edit systemd unit file
sudo nano /etc/systemd/system.unit // edit systemd unit file
```

## Linux Networking Configuration Files Commands

```shell
sudo systemctl start // start the service
sudo systemctl stop // stop the service
sudo systemctl restart // restart the service
sudo systemctl status // check the status of the service
sudo systemctl enable // enable the service
sudo systemctl disable // disable the service
```
