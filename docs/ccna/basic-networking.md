---
layout: default
title: Basic Networking
description: Basic Networking
has_toc: false
nav_order: 1
parent: CCNA
permalink: /ccna/basic-networking
---

# Basic Networking
{: .no_toc }
List of basic networking commands for troubleshooting/configuration

---

<details closed markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>


### Setup
---

#### Initialize

These commands wipe all config and reboot the device

```
erase startup-config
delete vlan.dat
reload
```

**Note:** Remember to say "no" to saving running config on reload. If you say yes, running config will be saved and you wont be working with fresh config on reload.

#### Basic Switch Config

```
configure terminal
no ip domain-lookup
hostname S1
line console 0
logging synchronous
exit
banner motd $ Authorized Access Only! And Godzilla will beat Kong any day $
exit
copy running-config startup-config
```

#### Basic Router Config

```
configure terminal
no ip domain-lookup
hostname R1
line console 0
logging synchronous
exit
banner motd $ Authorized Access Only! And Godzilla will beat Kong any day $
exit
copy running-config startup-config
```

#### Basic Config with Password Security

_paste-able_

```
configure terminal
no ip domain-lookup
hostname R1
line console 0
logging synchronous
exit
banner motd $ Authorized Access Only! And Godzilla will beat Kong any day $
exit
copy running-config startup-config
conf t
enable secret class
line console 0
password cisco
login
exit
line vty 0 4
password cisco
login
exit
service password-encryption
end
copy running-config startup-config
```

#### Basic Security

```
conf t
enable secret class
line console 0
password cisco
login
exit
line vty 0 4
password cisco
login
exit
service password-encryption
end
```

#### Configure SSH

```
show ip ssh
conf t
ip domain-name cisco.com
crypto key generate rsa

username admin secret ccna
line vty 0 15
transport input ssh
login local
exit
ip ssh version 2
exit
```

#### Set Clock

*Show Clock*

```
show clock
```

*Sets clock to eastern US time*

```
clock timezone EST -5
```

*Revert to Default Timezone*

```
no clock timezone
```

#### Basic Hardening (Work Needed)

```
conf t
! Logout timer
!
line con 0
 exec-timeout 5
line vty 0 4
 exec-timeout 5
 
exit

ip ssh time-out 60
ip ssh authentication-retries 3
end
```

#### Backup config over FTP

*Using included [FTP server](#ftp-server-usage)*

```
copy running-config startup-config
copy startup-config ftp://192.168.1.10/config.txt
```

#### Backup config over console

_coming soon_

#### Restore Config
```
copy ftp://192.168.1.10/config.txt running-config
```

#### Nuking (ROMMON, Password Recovery, etc)

*Perform a Boot Interrupt to Recover a lost or unknown password*

**WARNING**: This operation will delete all current config on the device

1. Ensure Console Cable is connected at 9600 Baudrate
2. Backup config if you need
3. Unplug Power
4. Wait for a few seconds
5. Re-insert the power cord to the switch
6. Within 15 seconds, hold the `Mode` button until the green flashing light flashes amber and then returns to flashing green. Release the `Mode` button.
7. Something like the following should display:

    ```
    initialize the flash file system, and finish loading the operating system software#
    
    flash_init
    load_helper
    boot
    ```
8. Run `flash_init`
9. Run `copy flash:config.text flash:config.text.old`
10. Run `boot`

    The device should now boot with no config and grant you access to it.


### Interfaces
---


#### Interface Selection

*Assign and IP address to a port*
```
conf t
int f0/1
ip addr 192.168.10.11 255.255.255.0
end
```

#### Assign Static IP to Interface

```
cont t
int g0/0
ip addr 10.0.0.10 255.255.255.0
```

#### Interface Ranges

*Assign and IP address to a port*
```
conf t
int f0/1
ip addr 192.168.10.11 255.255.255.0
end
```

*Select Single Range and Assign to a VLAN*
```
conf t
int range f0/1-12
switchport mode access
switch access vlan 10
end
```

```
conf t
int range f0/13-24
switchport mode access
switchport access vlan 20
end
```

*Select Multiple Interface Ranges and Move to a VLAN*
```
conf t
int range f0/1-4,g0/1,f0/16-20
switchport mode access
switchport access vlan 10
end
```

### Interface Verification

```
show ip interface brief
```

*or*

```
show ip int br
```

#### Remove IP Addresses

```
conf t
int f0/1
no ip addr
end
```

### Console Port

#### Change Console Baudrate

```
conf t
line con 0
speed 115200
end
```

```
conf t
line con 0
speed 9600
end
```

### DHCP
---

#### Snippet: Enable Router DHCP Server

This snippet configures a DHCP Server on R1 and will hand out
IPs on the `10.0.0.1/24` network. Great for using an [FTP Server](#ftp-server-usage) with.

```
conf t
ip domain name cisco.com
ip dhcp excluded-address 10.0.0.1
ip dhcp pool test
network 10.0.0.0 255.255.255.0
default-router 10.0.0.1
end
```

#### Snippet: Enable Switch DHCP Server

```
ip dhcp pool test
network 10.0.0.0 255.255.255.0
domain-name cisco.com
default-router 10.0.0.1
dns-server 10.0.0.1
lease 4
ip dhcp snooping
ip dhcp-server 10.0.0.3
interface vlan 1
ip address 10.0.0.3
```

#### Create DHCP Pool

*Workaround for CCNA labs at Liberty University since we can't change the LAB IP addresses*

```
conf t
ip domain name cisco.com
ip dhcp excluded-address 10.0.0.1
ip dhcp pool managementpool
network 10.0.0.1 255.255.255.0
default-router 10.0.0.1
end
```

```
conf t
ip dhcp excluded-address 192.168.10.1
ip dhcp excluded-address 192.168.10.254
ip dhcp pool office-pool-1
network 192.168.10.0 255.255.255.0
default-router 192.168.10.1
dns-server 192.168.5.5
domain-name linux.org
end
```

#### DHCP Verification

```
show running-config | section dhcp
show ip dhcp binding
show ip dhcp server statistics
```

#### Disable DHCP

```
conf t
no service dhcp
end
```

#### Re-enabled DHCP

```
conf t
service dhcp
end
```

#### Create VLAN DHCP

*Creates a Seperate DHCP Pool for each VLAN*

*Create VLANS*
```
conf t
vlan 10
name Management
vlan 20
name Sales
vlan 30
name Operations
end
```

*Configure SVI's and IP Address*

| VLAN | IP Address | Gateway
|------|------------|--------|
| 10   | 192.168.10.254 | 192.168.10.1
| 20 | 192.168.20.254 | 192.168.20.1|
| 30 | 192.168.30.254 | 192.168.30.1|

```
conf t
int vlan 10
ip address 192.168.10.254 255.255.255.0
ip default-gateway 192.168.10.1
no shut

int vlan 20
ip address 192.168.20.254 255.255.255.0
ip default-gateway 192.168.20.1
no shut

int vlan 30
ip address 192.168.30.254 255.255.255.0
ip default-gateway 192.168.30.1
no shut
end
```

*Add interfaces to VLANS, 8 ports per vlan*

```
conf t
int range f0/1-7
switchport mode access
switchport access vlan 10

int range f0/8-15
switchport mode access
switchport access vlan 20

int range f0/16-24
switchport mode access
switchport access vlan 30
end
```

*Create DHCP Pools for each vlan*

```
conf t
ip domain name cisco.com
ip dhcp excluded-address 192.168.10.1
ip dhcp pool vlan10pool
network 192.168.10.0 255.255.255.0
default-router 192.168.10.1
import all


ip dhcp excluded-address 192.168.20.1
ip dhcp pool vlan20pool
network 192.168.20.0 255.255.255.0
default-router 192.168.20.1
import all

ip dhcp excluded-address 192.168.30.1
ip dhcp pool vlan30pool
network 192.168.30.0 255.255.255.0
default-router 192.168.30.1
import all
end
```

Now when a device plugs into a port `f0/4` for instance and performs a DHCP request, it should get an IP like `192.168.10.3` because it is plugged into the ports assigned to VLAN 10

#### Verify DHCP Pool

```
show ip dhcp pool
```

#### Delete DHCP Pool

```
conf t
no ip dhcp pool managementpool
end
```