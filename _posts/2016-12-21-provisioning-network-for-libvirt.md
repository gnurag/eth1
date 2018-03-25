---
layout: post
title: Provisioning network setup for libvirt
---

libvirt is a great platform for deploying virtual machine instances. Setting up a provisioning network that assigns IP addresses via DHCP and points to a boot server is quite straightforward. 

### Create examplenet network definition

```xml
[root@coruscant ~]# vim examplenet.xml 
<network>
  <name>examplenet</name>
  <uuid>f73de5a7-4ee0-4ecf-8159-fdffc81bea29</uuid>
  <forward mode='nat'>
    <nat>
      <port start='1024' end='65535'/>
    </nat>
  </forward>
  <bridge name='virbr3' stp='on' delay='0'/>
  <mac address='36:06:D0:13:85:5D'/>
  <domain name='example.com' localOnly='yes'/>
  <ip address='192.168.103.1' netmask='255.255.255.0'>
    <dhcp>
      <range start='192.168.103.2' end='192.168.103.254'/>
      <bootp file='/pxelinux.0' server='192.168.103.3'/>
    </dhcp>
  </ip>
</network>
```

This XML file specifies a network named examplenet and helps the guest VMs PXE boot from 192.168.103.3

#### Define the network

```
[root@coruscant ~]# virsh net-define examplenet.xml 
Network examplenet defined from examplenet.xml
```

#### Start the network

```
[root@coruscant ~]# virsh net-autostart examplenet
Network examplenet marked as autostarted

[root@coruscant ~]# virsh net-start examplenet
Network examplenet started
```

#### Status check

```
[root@coruscant ~]# virsh net-list
 Name                 State      Autostart     Persistent
----------------------------------------------------------
 default              active     yes           yes
 examplenet           active     yes           yes
 fdev                 active     yes           yes
```
