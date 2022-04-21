# Raspberry Pi 4 K3S install guide

This is a guide taken from [NetworkChuck](https://www.youtube.com/watch?v=X9fSMGkjtug&t=1183s&ab_channel=NetworkChuck) on how to install k3s on a raspberry pi 4. This also includes a guide on how to install Rancher on a separate server for cluster management.

# STEP 1 - Raspberry Pi Headless Setup
## Image the SD card using the Raspberry Pi Imager.
Raspberry Pi Imager: https://www.raspberrypi.org/software/

## Install "Raspberry Pi OS Lite"

Insert the SD card into the Raspberry Pi and allow it to boot (3-4 minutes)

Remove the SD card and plug it back into your computer

Open the SD card location and open the file `cmdline.txt`

Add the following to the end of the line of text:

`cgroup_memory=1 cgroup_enable=memory ip=192.168.1.43::192.168.1.1:255.255.255.0:rpiname:eth0:off`

Use this for reference (ip={client-ip}:{server-ip}:{gw-ip}:{netmask}:{hostname}:{device}:{autoconf} )

Open the file `config.txt`

Add this line of config to the end of the file:
`arm_64bit=1`

## Enable SSH
### Windows
Open powershell and change directories to the SD card (ex. I:)

Type this command and hit enter: 
`new-item ssh`
Put the SD card back in your raspberry pi and boot. (make sure you plug in your ethernet cable!)


# STEP 2 - K3s Prep

SSH into your raspberry pi
Install IP tables:
```bash
sudo apt-get install iptables
```
Configure legacy IP tables:
````bash
sudo iptables -F
sudo update-alternatives --set iptables /usr/sbin/iptables-legacy\
sudo update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy
````
Then reboot:
````bash
sudo reboot
````

# STEP 3 - K3s Install (master setup)
This is for setting up the master node in the cluster.

Become root:
````bash
sudo su -
````
Install K3s (master setup)
````bash
curl -sfL https://get.k3s.io | K3S_KUBECONFIG_MODE="644" sh -s -
````
Get the node token from the master:
````bash
sudo cat /var/lib/rancher/k3s/server/node-token
````

# STEP 4 - K3s Install (node setup)
This is for if you want to add additional nodes to your cluster.

Run this command on your other Raspberry Pi nodes
````bash
curl -sfL https://get.k3s.io | K3S_TOKEN="YOURTOKEN" K3S_URL="https://[your server]:6443" K3S_NODE_NAME="servername" sh -
````