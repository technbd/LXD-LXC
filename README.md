## LXD-LXC:

LXD is an open-source, next-generation system container and virtual machine manager developed by Canonical. It allows you to run complete Linux operating systems inside lightweight system containers (via LXC) or full KVM virtual machines on a single machine or a clustered data center.




The LXD project is no longer part of the Linux Containers project but can now be found directly on Canonical's websites.


## Install LXD on Ubuntu: 


### Install Snap (if not already installed):


```
apt install snapd -y
```


```
snap version

snap          2.75.2+ubuntu24.04
snapd         2.75.2+ubuntu24.04
series        16
ubuntu        24.04
kernel        6.8.0-134-generic
architecture  amd64
```


### Install LXD via Snap (Recommended): 

```
snap find lxd
```


```
snap install lxd

Or,
snap install lxd --channel=latest/stable
snap install lxd --channel=5.21/stable
```


_Verify:_
```
lxd --version
```



```
lxc version
```



```
snap list


Name    Version      Rev    Tracking       Publisher   Notes
core26  20260531     409    latest/stable  canonical✓  base
lxd     6.9-a34e1d7  39858  latest/stable  canonical✓  -
snapd   2.76         27406  latest/stable  canonical✓  snapd
```




#### Add user to the `lxd` group:


```
usermod -aG lxd $USER


-- Apply the new group membership:
newgrp lxd
```



### Initialize and Configure LXD:

```
lxd init
```


```
### Output:

Would you like to use LXD clustering? (yes/no) [default=no]: no
Do you want to configure a new storage pool? (yes/no) [default=yes]: y
Name of the new storage pool [default=default]:
Name of the storage backend to use (ceph, powerflex, powerstore, pure, zfs, btrfs, dir, lvm, alletra) [default=zfs]:
Create a new ZFS pool? (yes/no) [default=yes]: y
Would you like to use an existing empty block device (e.g. a disk or partition)? (yes/no) [default=no]:
Size in GiB of the new loop device (1GiB minimum) [default=17GiB]: 20GiB
Warning: Creating a bridge enables IPv4 and IPv6 forwarding on the host, which affects all interfaces.
Would you like to create a new local network bridge? (yes/no) [default=yes]:
What should the new bridge be called? [default=lxdbr0]:
What IPv4 address should be used? (CIDR subnet notation, “auto” or “none”) [default=auto]:
What IPv6 address should be used? (CIDR subnet notation, “auto” or “none”) [default=auto]: none
Would you like the LXD server to be available over the network? (yes/no) [default=no]: yes
Address to bind LXD to (not including port) [default=all]:
Port to bind LXD to [default=8443]:
Would you like to create an initial LXD UI access link? (yes/no) [default=yes]:
Would you like stale cached images to be updated automatically? (yes/no) [default=yes]:
Would you like a YAML "lxd init" preseed to be printed? (yes/no) [default=no]:
UI initial identity (type: Initial UI token bearer): ui-admin-initial
UI initial access link (expires: 2026-07-11 20:20): https://10.1.1.22:8443?token=eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJseGQ6MDE5ZjRjNjItYmRlNi03MmY1LThlOTItNzAwMTQyZjljYTNhIiwic3ViIjoiY2MyNmVmODQtNmMyNy00ZTEwLThjY2UtNWM0M2RlNGU4MDA3IiwiYXVkIjpbImx4ZDowMTlmNGM2Mi1iZGU2LTcyZjUtOGU5Mi03MDAxNDJmOWNhM2EiXSwiZXhwIjoxNzgzNzc5NjI1LCJuYmYiOjE3ODM2OTMyMjUsImlhdCI6MTc4MzY5MzIyNSwic2VydmVyX2NlcnRfZmluZ2VycHJpbnQiOiI4ZTk1MGMwNmRlNWEzNzgwNTJlNTVlYWZjN2Q3MWFmNjM5MjBjYWI2M2I0NWY1NWI0Yjg4MTY3ZDg0MzhlNDcyIn0.qXIoAzXtRDIFwoR5XEMgUzEfF6G2IybWL4x9G2TFfRmY6LfrfyustTLrpCN44fDzCouvMo0S5Fz2QtqIDYSOCQ
```



_Verify installation:_
```
lxc info
```



```
lxc storage list

+---------+--------+--------------------------------------------+-------------+---------+---------+
|  NAME   | DRIVER |                   SOURCE                   | DESCRIPTION | USED BY |  STATE  |
+---------+--------+--------------------------------------------+-------------+---------+---------+
| default | zfs    | /var/snap/lxd/common/lxd/disks/default.img |             | 3       | CREATED |
+---------+--------+--------------------------------------------+-------------+---------+---------+
```




### Launching an Instance:

#### Create Container:


```
lxc launch <image_server>:<image_name> <instance_name>

lxc launch ubuntu:24.04 my-container
```


_List containers:_
```
lxc list

+--------------+---------+----------------------+------+-----------+-----------+
|     NAME     |  STATE  |         IPV4         | IPV6 |   TYPE    | SNAPSHOTS |
+--------------+---------+----------------------+------+-----------+-----------+
| my-container | RUNNING | 10.208.106.76 (eth0) |      | CONTAINER | 0         |
+--------------+---------+----------------------+------+-----------+-----------+
```



_Assign CPU and Memory to an Existing Container:_
```
-- Limit to 2 CPUs: 
lxc config set my-container limits.cpu 2


-- Limit Memory:
lxc config set my-container limits.memory 512MiB


-- Verify Limits: 
lxc config show my-container
```




_Access the container:_
```
lxc exec <instance_name> -- <command>

lxc exec my-container -- bash

lxc exec my-container -- apt-get update


Or,
lxc shell my-container
```





#### Create VM:

```
lxc launch <image_server>:<image_name> <instance_name> --vm

lxc launch ubuntu:24.04 my-vm --vm
```



_List containers:_
```
lxc list

+--------------+---------+-------------------------+------+-----------------+-----------+
|     NAME     |  STATE  |          IPV4           | IPV6 |      TYPE       | SNAPSHOTS |
+--------------+---------+-------------------------+------+-----------------+-----------+
| my-container | RUNNING | 10.208.106.76 (eth0)    |      | CONTAINER       | 0         |
+--------------+---------+-------------------------+------+-----------------+-----------+
| my-vm        | RUNNING | 10.208.106.186 (enp5s0) |      | VIRTUAL-MACHINE | 0         |
+--------------+---------+-------------------------+------+-----------------+-----------+
```




_Assign CPU and Memory to an Existing VM:_
```
-- Limit to 2 CPUs: 
lxc config set my-vm limits.cpu 2


-- Limit Memory:
lxc config set my-vm limits.memory 512MiB


-- Verify Limits: 
lxc config show my-vm
```



_Access the container:_
```
lxc exec my-vm -- bash

Or,
lxc shell my-vm
```




### Assign Resources During Launch (Using Profiles): 

_Create a profile:_
```
lxc profile create small
```


_Edit profile:_
```
lxc profile edit small


name: small
description: ""
config:
  limits.cpu: "1"
  limits.memory: 512MiB
devices: {}
used_by: []
project: default
```


_Show `small` profile:_
```
lxc profile show small


name: small
description: ""
config:
  limits.cpu: "1"
  limits.memory: 512MiB
devices: {}
used_by: []
project: default
```


_Show `default` profile:_
```
lxc profile show default


name: default
description: Default LXD profile
config: {}
devices:
  eth0:
    name: eth0
    network: lxdbr0
    type: nic
  root:
    path: /
    pool: default
    type: disk
used_by: []
project: default
```



`Note`: Keep your `default` profile for networking and storage, and use `small` only for resource limits.

_Launch using the both profiles:_
```
lxc launch ubuntu:24.04 my-container2 --profile default --profile small

lxc launch ubuntu:24.04 my-vm2 --profile default --profile small
```



```
lxc config show my-vm2
```




### Stop and Start Instance:

```
lxc stop my-container

lxc stop my-vm
```



```
lxc start my-container

lxc start my-vm
```




---
---


## Accessing LXC Containers from External Users:

By default, an LXC/LXD container gets an IP from the lxdbr0 bridge (e.g., 10.x.x.x) and is not directly reachable from external networks.


### Option 1: Port Forward (Proxy Device) a host port to the container -  Most Common

Suppose:
- Host IP: `10.1.1.22`
- Container name: `my-container`
- Nginx running on container port `80`



```
-- Syntax: 
lxc config device add <container_name> <device_name/alias_name> proxy listen=tcp:<host-ip>:<host-port> connect=tcp:<container-ip>:<container-port>

--- 
lxc config device add my-container web proxy listen=tcp:0.0.0.0:80 connect=tcp:127.0.0.1:80

lxc config device add my-container ssh-port proxy listen=tcp:0.0.0.0:2222 connect=tcp:127.0.0.1:22
```




_To show:_
```
lxc config device show my-container

ssh-port:
  connect: tcp:127.0.0.1:22
  listen: tcp:0.0.0.0:2222
  type: proxy

web:
  connect: tcp:127.0.0.1:80
  listen: tcp:0.0.0.0:80
  type: proxy
```



```
netstat -tlpn | grep 80

tcp6       0      0 :::80                   :::*                    LISTEN      1351803/lxd
```



_To remove:_
```
lxc config device remove my-container ssh-port
```



_Verify:_
```
curl http://10.1.1.22
```



### Option 2: NAT Port Forwarding (iptables) to the container

_Find container IP:_
```
lxc list

+--------------+---------+-------------------------+------+-----------------+-----------+
|     NAME     |  STATE  |          IPV4           | IPV6 |      TYPE       | SNAPSHOTS |
+--------------+---------+-------------------------+------+-----------------+-----------+
| my-container | RUNNING | 10.208.106.124 (eth0)   |      | CONTAINER       | 0         |
+--------------+---------+-------------------------+------+-----------------+-----------+
| my-vm        | RUNNING | 10.208.106.186 (enp5s0) |      | VIRTUAL-MACHINE | 0         |
+--------------+---------+-------------------------+------+-----------------+-----------+
```


_Enable forwarding:_
```
sysctl -w net.ipv4.ip_forward=1
```


_Add NAT:_
```
iptables -t nat -A PREROUTING -p tcp --dport 80 -j DNAT --to-destination 10.208.106.124:80 
```



_Show all NAT rules:_
```
iptables -t nat -L -n -v | grep 10.208.106.124
```



_Show rules in command format:_
```
iptables -t nat -S

iptables -t nat -S | grep 80
```




---
---



### Option 1: Port Forwarding a host port to the VM 

LXD VMs don't support proxy devices the same way containers do. For VMs, **only NAT mode is supported**. For NAT mode, connect must use the **VM's actual IP**, not `127.0.0.1`. 


Suppose:
- Host IP: `10.1.1.22`
- VM name: `my-vm`
- VM IP: `10.208.106.186`
- Web server running on port `80`



_Find VM's IP first:_
```
lxc list my-vm

+--------------+---------+-------------------------+------+-----------------+-----------+
|     NAME     |  STATE  |          IPV4           | IPV6 |      TYPE       | SNAPSHOTS |
+--------------+---------+-------------------------+------+-----------------+-----------+
| my-vm        | RUNNING | 10.208.106.186 (enp5s0) |      | VIRTUAL-MACHINE | 0         |
+--------------+---------+-------------------------+------+-----------------+-----------+
```


_Check the profile and find the `nic` device name (e.g., `eth0`):_
```
lxc profile show default


name: default
description: Default LXD profile
config: {}
devices:
  eth0:
    name: eth0
    network: lxdbr0
    type: nic
  root:
    path: /
    pool: default
    type: disk
used_by:
- /1.0/instances/my-vm
- /1.0/instances/my-container
project: default
```


_Add the device directly to the VM (override profile):_
```
lxc config device add my-vm eth0 nic network=lxdbr0 name=eth0 ipv4.address=10.208.106.186

lxc restart my-vm
```


```
lxc list my-vm

+-------+---------+-----------------------+------+-----------------+-----------+
| NAME  |  STATE  |         IPV4          | IPV6 |      TYPE       | SNAPSHOTS |
+-------+---------+-----------------------+------+-----------------+-----------+
| my-vm | RUNNING | 10.208.106.186 (eth0) |      | VIRTUAL-MACHINE | 0         |
+-------+---------+-----------------------+------+-----------------+-----------+
```


_Add the proxy:_
```
lxc config device add my-vm web proxy listen=tcp:<HOST-IP>:80 connect=tcp:<VM-IP>:80 nat=true

lxc config device add my-vm web-http proxy listen=tcp:10.1.1.22:8080 connect=tcp:10.208.106.186:80 nat=true
```



```
lxc config device show my-vm


eth0:
  ipv4.address: 10.208.106.186
  name: eth0
  network: lxdbr0
  type: nic
web-http:
  connect: tcp:10.208.106.186:80
  listen: tcp:10.1.1.22:8080
  nat: "true"
  type: proxy
```


_To remove:_
```
lxc config device remove my-vm web-http
```



_Verify:_
```
curl http://10.1.1.22:8080
```




### Option 2: NAT Port Forwarding to the VM 

_Find the VM's IP:_
```
lxc list my-vm

+-------+---------+-------------------------+------+-----------------+-----------+
| NAME  |  STATE  |          IPV4           | IPV6 |      TYPE       | SNAPSHOTS |
+-------+---------+-------------------------+------+-----------------+-----------+
| my-vm | RUNNING | 10.208.106.186 (enp5s0) |      | VIRTUAL-MACHINE | 0         |
+-------+---------+-------------------------+------+-----------------+-----------+
```


_Add an iptables DNAT rule:_
```
iptables -t nat -A PREROUTING -p tcp --dport 80 -j DNAT --to-destination 10.208.106.186:80
```


_Allow forwarding:_
```
iptables -A FORWARD -p tcp -d 10.208.106.186 --dport 80 -j ACCEPT
```




_Show all NAT rules:_
```
iptables -t nat -L -n -v | grep 10.208.106.186
```



_Show rules in command format:_
```
iptables -t nat -S

iptables -t nat -S | grep 80
```


_List the NAT rules with line numbers:_
```
iptables -t nat -L PREROUTING --line-numbers -n -v


Chain PREROUTING (policy ACCEPT 168K packets, 10M bytes)
num   pkts bytes target     prot opt in     out     source               destination
1     253K   18M cali-PREROUTING  0    --  *      *       0.0.0.0/0            0.0.0.0/0            /* cali:6gwbT8clXdHdC1b1 */
2     254K   18M KUBE-SERVICES  0    --  *      *       0.0.0.0/0            0.0.0.0/0            /* kubernetes service portals */
3      783 48563 DOCKER     0    --  *      *       0.0.0.0/0            0.0.0.0/0            ADDRTYPE match dst-type LOCAL
4       10   592 DNAT       6    --  *      *       0.0.0.0/0            0.0.0.0/0            tcp dpt:80 to:10.208.106.124:80
5        0     0 DNAT       6    --  *      *       0.0.0.0/0            0.0.0.0/0            tcp dpt:80 to:10.208.106.186:80
```



_Delete rule 4:_
```
iptables -t nat -D PREROUTING 4
```








---
---




### Useful commands: 

``` 
-- List of configured remotes:
lxc remote list


-- List available Ubuntu images:
lxc image list ubuntu:
lxc image list ubuntu:24.04

lxc image list images: | grep CONTAINER

lxc image list images:ubuntu
lxc image list images:ubuntu/24.04
lxc image list images:ubuntu/noble


-- Show information:
lxc info my-container


-- Restart:
lxc restart my-container


-- Delete:
lxc delete my-container --force


-- Take a snapshot:
lxc snapshot my-container snap1


-- Restore a snapshot:
lxc restore my-container snap1
```



---
---


### Ref: 

- [Install LXD](https://canonical.com/lxd/install)
- [github.com | canonical/lxd](https://github.com/canonical/lxd)
- [Image Server](https://images.lxd.canonical.com/)



