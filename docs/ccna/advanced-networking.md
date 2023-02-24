---
layout: default
title: Advanced Networking 
description: Advanced Networking
has_toc: false
nav_order: 3
parent: CCNA
permalink: /ccna/advanced-networking
---

# Advanced Networking
{: .no_toc}
List of advanced networking commands for troubleshooting/configuration

---

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>


### OSPFv2

#### OSPF Router IDs

##### All Commands

```
show ip ospf neighbor
show ip ospf database 
```

##### Enable router OSPF process

Starting Mode: Global, Non-enabled

```
enable
conf t
router ospf 10
```

##### Configure Loopback

```
enable
conf t
interface Loopback 1
ip addr 1.1.1.1 255.255.255.255
end
```

##### Configure OSPF Router ID

_replace `1.1.1.1` with desired id_
```
conf t
router ospf 10
router-id 1.1.1.1
end
```

##### Modify OSPF router ID

_Prompt confirmation with 'y' needed_

```
conf t
router ospf 10
router-id 1.1.1.2
end
clear ip ospf process
```

_Verify_

```
show ip proto | include Router ID
```
#### OSPF - Point-to-Point Networks

##### Network Command Syntax

`Router(config-router)# network network-address wildcard-mask area area-id`

##### Configure OSPF With Network Command

The following configures a trianngle of 3 routers connected to
each other as an OSPF point to point network.

```
conf t
router ospf 10
network 10.10.1.0 0.0.0.255 area 0
network 10.10.1.4 0.0.0.3 area 0
network 10.10.1.12 0.0.0.3 area 0
end
```

##### Use Entire Gigabit Interfaces

```
conf t
router ospf 10
network 10.10.1.1 0.0.0.0 area 0
network 10.10.1.5 0.0.0.0 area 0
network 10.10.1.14 0.0.0.0 area 0
end
```

##### Configure OSPF with `ip ospf`

Configure OSPF directly on the interfaces rather with with the network
command.

Syntax: `Router(config-if)# ip ospf <process-id> area <area-id>`

```
R1(config)# router ospf 10
R1(config-router)# no network 10.10.1.1 0.0.0.0 area 0
R1(config-router)# no network 10.1.1.5 0.0.0.0 area 0
R1(config-router)# no network 10.1.1.14 0.0.0.0 area 0
R1(config-router)# interface GigabitEthernet 0/0/0
R1(config-if)# ip ospf 10 area 0
R1(config-if)# interface GigabitEthernet 0/0/1 
R1(config-if)# ip ospf 10 area 0
R1(config-if)# interface Loopback 0
R1(config-if)# ip ospf 10 area 0
R1(config-if)#
```

##### OSPF Passive Interfaces

```
conf t
router ospf 10
passive-interface loopback 0
end
```

```
conf t
router ospf 10
passive-interface Gi0/0/0
end
```

##### Find Designated Router and Backup

```
show ip ospf interface GigabitEthernet 0/0/0
```

##### Change OSPF from Broadcast to Point-to-Point

```
conf t
interface GigabitEthernet 0/0/0
ip ospf network point-to-point
```

##### Loopback and P2P Networks

Loobacks can be used to simulate real LAN networks

```
conf t
interface Loopback 0
ip ospf network point-to-point
```

```
show ip route | include 10.10.1
```

#### Multiaccess OSPF Networks

##### Configure OSPF Priority

```
conf t
int g0/0/1
ip ospf priority 255
end
```

Where `255` can be values from `0` to `255` with higher numbers making the router to be elected `DR`.

#### Modifying Single Area OSPF

##### Adjusting Reference Bandwidth

```
Router# router ospf 10
Router(config-router) auto-cost reference bandwidth 1000
```

_Where 1000 is the speed of the link in Mpbs_
Common Values: 10, 100, 1000

##### Manually Set OSPF Link Cost

```
conf t
int g0/0/1
ip ospf cost 25
interface l0
ip ospf cost 15
end
```

##### Show OSPF Hello Packet Intervals

```
show ip ospf int g0/0/1
```

##### Set OSPF Hello Packet Intervals

```
Router(config-if)# ip ospf hello-interval <seconds>
```

```
conf t
int g0/0/1
ip ospf hello-interval 30
end
```

Note: dead-interval automatically gets set as `hello-interval * 4`


##### Set OSPF Dead Interval

#### OSPF Default Routes

##### Propagate Default Route

```
conf t
ip route 0.0.0.0 0.0.0.0 loopback 1
router ospf 10
default-information originate
```

##### Verify Propagated Default Route

```
show ip route | begin Gateway
```

#### Verify Single-Area OSPF

##### Verify OSPF Neighbors

```
show ip ospf neighbor
```

##### Verify OSPF Protocols

```
show ip protocols
```

##### Verify OSPF Process Info

```
show ip ospf
```

##### Verify OSPF Interface Setting

```
show ip ospf int g0/0/1
show ip ospf int brief
```

Where `g0/0/1` is the interface you was to see OSPF information on.

```
conf t
int g0/0/1
ip ospf dead-interval 100
end
```