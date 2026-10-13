#  EtherChannel & Link Aggregation — Complete Networking Notes

**Topics:** CCNA, Cisco Switch Configuration, LACP, PAgP, Network Redundancy, Troubleshooting

##  Table of Contents

1. [Introduction](#1-introduction)
2. [What is Link Aggregation?](#2-what-is-link-aggregation)
3. [Advantages and Limitations](#3-advantages-and-limitations)
4. [EtherChannel Components](#4-etherchannel-components)
5. [LACP vs PAgP vs Static](#5-lacp-vs-pagp-vs-static)
6. [Network Topology](#6-network-topology)
7. [Basic Cisco Switch Commands](#7-basic-cisco-switch-commands)
8. [Configure EtherChannel Using LACP](#8-configure-etherchannel-using-lacp)
9. [Configure EtherChannel Using PAgP](#9-configure-etherchannel-using-pagp)
10. [Static EtherChannel Configuration](#10-static-etherchannel-configuration)
11. [Configure EtherChannel as a Trunk](#11-configure-etherchannel-as-a-trunk)
12. [Verification Commands](#12-verification-commands)
13. [Troubleshooting](#13-troubleshooting)
14. [Security Best Practices](#14-security-best-practices)
15. [Linux Link Aggregation Commands](#15-linux-link-aggregation-commands)
16. [Practice Lab](#16-practice-lab)
17. [Important Terms](#17-important-terms)

---

## 1. Introduction

**EtherChannel** হলো Cisco networking-এর একটি technology, যার মাধ্যমে একাধিক physical Ethernet link-কে একত্র করে একটি logical link বা Port-Channel তৈরি করা হয়।

**Link Aggregation** হলো একাধিক network link একত্র করার সাধারণ ধারণা। LACP হলো এটি negotiate ও manage করার একটি standard protocol।

### সহজ উদাহরণ

ধরো, Switch A এবং Switch B-এর মধ্যে দুটি 1 Gbps Ethernet cable সংযুক্ত আছে।

সাধারণ configuration-এ Spanning Tree Protocol (STP) loop prevention-এর জন্য একটি link block করে রাখতে পারে।

EtherChannel ব্যবহার করলে compatible physical link-গুলো একত্রে একটি logical channel হিসেবে কাজ করতে পারে।

```text
         Switch A                 Switch B
       +----------+              +----------+
       |          |==============|          |
       |          |==============|          |
       +----------+              +----------+
                    2 Physical Links
                           |
                      Port-Channel
```

এখানে দুটি physical link একটি logical interface হিসেবে কাজ করছে।

---

## 2. What is Link Aggregation?

Link Aggregation হলো একাধিক physical network interface-কে একটি logical interface-এ group করার প্রক্রিয়া।

### Main Objectives

* Bandwidth capacity বাড়ানো
* Link redundancy তৈরি করা
* কোনো একটি member link failure হলে connectivity বজায় রাখতে সাহায্য করা
* Network management সহজ করা

### Bandwidth Example

| Physical Links | Nominal Combined Capacity |
| -------------- | ------------------------: |
| 1 × 1 Gbps     |                    1 Gbps |
| 2 × 1 Gbps     |                    2 Gbps |
| 4 × 1 Gbps     |                    4 Gbps |

**Important:** এই হিসাব aggregate capacity বোঝায়। একটি single connection বা flow সাধারণত সব link-এর bandwidth একসাথে ব্যবহার করতে পারে না। Traffic distribution hashing algorithm-এর উপর নির্ভর করে।

---

## 3. Advantages and Limitations

### Advantages

1. **Increased Aggregate Bandwidth:** একাধিক link-এর capacity একত্রে ব্যবহার করা যায়।
2. **Redundancy:** একটি physical link down হলেও অন্য active member link দিয়ে traffic চলতে পারে।
3. **Load Distribution:** Traffic বিভিন্ন member link-এ distribute হতে পারে।
4. **Simplified Management:** একাধিক physical link একটি logical interface হিসেবে পরিচালিত হয়।
5. **STP Integration:** একটি Layer 2 EtherChannel-কে STP সাধারণত একটি logical port হিসেবে বিবেচনা করে।

### Limitations

* সব physical port compatible হতে হবে।
* দুই পাশের configuration সামঞ্জস্যপূর্ণ হতে হবে।
* একটি single flow-এর speed aggregate bandwidth-এর সমান নাও হতে পারে।
* সব ধরনের switch বা interface একই feature support করে না।
* সব member link বা device একসাথে failure হলে redundancy নাও থাকতে পারে।

---

## 4. EtherChannel Components

### 4.1 Physical Interfaces

যে physical Ethernet ports-গুলো একত্র করা হয়।

Examples:

```text
GigabitEthernet0/1
GigabitEthernet0/2
FastEthernet0/1
FastEthernet0/2
```

### 4.2 Port-Channel Interface

একাধিক physical port group করে যে logical interface তৈরি হয়।

Example:

```text
Port-channel 1
```

Configuration mode:

```cisco
Switch(config)# interface port-channel 1
```

### 4.3 Channel Group

Physical interfaces-কে একটি নির্দিষ্ট EtherChannel group-এ যুক্ত করা হয়।

Example:

```cisco
Switch(config-if-range)# channel-group 1 mode active
```

এখানে `1` হলো channel-group number।

### 4.4 Negotiation Protocol

Compatible devices-এর মধ্যে EtherChannel তৈরি ও manage করতে LACP বা PAgP ব্যবহার করা যায়।

---

## 5. LACP vs PAgP vs Static

### LACP — Link Aggregation Control Protocol

LACP হলো standards-based link aggregation protocol। এটি IEEE 802.1AX-এর সঙ্গে সম্পর্কিত; পুরোনো documentation-এ IEEE 802.3ad নামটিও দেখা যায়।

LACP modes:

| Mode    | Meaning                                        |
| ------- | ---------------------------------------------- |
| Active  | নিজে থেকে LACP negotiation শুরু করে            |
| Passive | অন্য পাশ থেকে LACP negotiation এলে respond করে |

Compatible combinations:

```text
Active + Active   = Works
Active + Passive  = Works
Passive + Passive = Does not initiate negotiation
```

### PAgP — Port Aggregation Protocol

PAgP হলো Cisco proprietary EtherChannel negotiation protocol।

| Mode      | Meaning                                       |
| --------- | --------------------------------------------- |
| Desirable | সক্রিয়ভাবে negotiation শুরু করে               |
| Auto      | অন্য পাশ থেকে negotiation-এর জন্য অপেক্ষা করে |

Compatible combinations:

```text
Desirable + Desirable = Works
Desirable + Auto      = Works
Auto + Auto           = Does not initiate negotiation
```

### Static EtherChannel

Static mode-এ negotiation protocol ব্যবহার না করে channel manually configure করা হয়।

```cisco
Switch(config-if-range)# channel-group 1 mode on
```

Static mode-এর দুই পাশেই সামঞ্জস্যপূর্ণ configuration প্রয়োজন।

### Quick Comparison

| Feature            | LACP           | PAgP               | Static                |
| ------------------ | -------------- | ------------------ | --------------------- |
| Standard           | IEEE standard  | Cisco proprietary  | Manual                |
| Negotiation        | Yes            | Yes                | No                    |
| Modes              | Active/Passive | Desirable/Auto     | On                    |
| Peer compatibility | LACP support   | PAgP support       | Matching static setup |
| General preference | Common choice  | Cisco-specific use | Special cases         |

---

## 6. Network Topology

### Required Devices

* 2 Cisco switches
* 2 Ethernet cables
* Cisco Packet Tracer অথবা compatible physical lab

Example:

```text
Switch A                         Switch B
---------                        ---------
Gi0/1  ========================  Gi0/1
Gi0/2  ========================  Gi0/2

         EtherChannel 1
          LACP Active
```

এই উদাহরণে দুটি link `Port-channel 1` তৈরি করবে।

**Note:** Packet Tracer বা switch model অনুযায়ী interface name আলাদা হতে পারে। নিজের device-এর interface list দেখে সঠিক নাম ব্যবহার করবে।

---

## 7. Basic Cisco Switch Commands

### Enter Privileged EXEC Mode

```cisco
Switch> enable
```

### Enter Global Configuration Mode

```cisco
Switch# configure terminal
```

### Set Hostname

```cisco
Switch(config)# hostname SW1
```

### Show Interfaces

```cisco
SW1# show interfaces status
```

### Show IP and Interface Information

```cisco
SW1# show ip interface brief
```

### Show Running Configuration

```cisco
SW1# show running-config
```

### Show Startup Configuration

```cisco
SW1# show startup-config
```

### Exit Configuration Mode

```cisco
SW1(config)# end
```

### Save Configuration

```cisco
SW1# copy running-config startup-config
```

এই command running configuration-কে startup configuration-এ save করে।

---

## 8. Configure EtherChannel Using LACP

এই lab-এ Switch A এবং Switch B-এর মধ্যে দুটি physical port LACP ব্যবহার করে group করা হবে।

### Step 1: Configure Switch A

```cisco
Switch> enable
Switch# configure terminal
Switch(config)# hostname SW1

SW1(config)# interface range gigabitEthernet0/1 - 2
SW1(config-if-range)# channel-group 1 mode active
SW1(config-if-range)# no shutdown
SW1(config-if-range)# end
```

### Step 2: Configure Switch B

```cisco
Switch> enable
Switch# configure terminal
Switch(config)# hostname SW2

SW2(config)# interface range gigabitEthernet0/1 - 2
SW2(config-if-range)# channel-group 1 mode active
SW2(config-if-range)# no shutdown
SW2(config-if-range)# end
```

এখন দুই পাশের compatible ports LACP negotiation-এর মাধ্যমে `Port-channel 1` গঠন করার চেষ্টা করবে।

### Command Explanation

| Command                       | Function                          |
| ----------------------------- | --------------------------------- |
| `enable`                      | Privileged EXEC mode              |
| `configure terminal`          | Global configuration mode         |
| `interface range ...`         | একাধিক interface নির্বাচন         |
| `channel-group 1 mode active` | LACP active mode-এ group তৈরি     |
| `no shutdown`                 | Interface administratively enable |
| `end`                         | Configuration mode থেকে বের হওয়া  |

### Verify

```cisco
SW1# show etherchannel summary
SW2# show etherchannel summary
```

সফল Layer 2 EtherChannel-এর summary-তে `Po1(SU)` দেখা যেতে পারে।

* `S` = Layer 2 port-channel
* `U` = In use

Member port-এর পাশে `P` সাধারণত বোঝায় যে portটি channel-এ successfully bundled হয়েছে।

---

## 9. Configure EtherChannel Using PAgP

**শুধু PAgP-supported Cisco devices-এ ব্যবহার করবে।**

### Switch A — Desirable

```cisco
SW1# configure terminal
SW1(config)# interface range gigabitEthernet0/1 - 2
SW1(config-if-range)# channel-group 1 mode desirable
SW1(config-if-range)# no shutdown
SW1(config-if-range)# end
```

### Switch B — Auto

```cisco
SW2# configure terminal
SW2(config)# interface range gigabitEthernet0/1 - 2
SW2(config-if-range)# channel-group 1 mode auto
SW2(config-if-range)# no shutdown
SW2(config-if-range)# end
```

### Verify

```cisco
SW1# show etherchannel summary
SW1# show pagp neighbor
```

PAgP-এর দুই পাশেই compatible configuration থাকতে হবে।

---

## 10. Static EtherChannel Configuration

Static EtherChannel-এ LACP বা PAgP negotiation হয় না।

### Switch A

```cisco
SW1# configure terminal
SW1(config)# interface range gigabitEthernet0/1 - 2
SW1(config-if-range)# channel-group 1 mode on
SW1(config-if-range)# no shutdown
SW1(config-if-range)# end
```

### Switch B

```cisco
SW2# configure terminal
SW2(config)# interface range gigabitEthernet0/1 - 2
SW2(config-if-range)# channel-group 1 mode on
SW2(config-if-range)# no shutdown
SW2(config-if-range)# end
```

**Warning:** Static configuration ভুলভাবে করলে loop বা traffic disruption হতে পারে। দুই পাশের configuration এবং physical cabling যাচাই করবে।

---

## 11. Configure EtherChannel as a Trunk

যখন EtherChannel-এর মাধ্যমে একাধিক VLAN-এর traffic বহন করতে হয়, তখন সাধারণত trunk configuration প্রয়োজন হয়।

এই উদাহরণে VLAN 10, 20 এবং 30 অনুমোদিত থাকবে।

### Switch A

```cisco
SW1# configure terminal

SW1(config)# interface range gigabitEthernet0/1 - 2
SW1(config-if-range)# channel-group 1 mode active
SW1(config-if-range)# no shutdown
SW1(config-if-range)# exit

SW1(config)# interface port-channel 1
SW1(config-if)# switchport mode trunk
SW1(config-if)# switchport trunk allowed vlan 10,20,30
SW1(config-if)# end
```

### Switch B

```cisco
SW2# configure terminal

SW2(config)# interface range gigabitEthernet0/1 - 2
SW2(config-if-range)# channel-group 1 mode active
SW2(config-if-range)# no shutdown
SW2(config-if-range)# exit

SW2(config)# interface port-channel 1
SW2(config-if)# switchport mode trunk
SW2(config-if)# switchport trunk allowed vlan 10,20,30
SW2(config-if)# end
```

### Important Notes

* দুই পাশের trunk configuration সামঞ্জস্যপূর্ণ হতে হবে।
* VLAN 10, 20 এবং 30 প্রয়োজন অনুযায়ী তৈরি থাকতে হবে।
* Switch model ও IOS অনুযায়ী কিছু command ভিন্ন হতে পারে।
* EtherChannel member ports-এ সাধারণত একই ধরনের Layer 2 configuration রাখতে হয়।
* কিছু platform-এ trunk settings physical members-এ configure করতে হয় অথবা Port-Channel থেকে inherit হয়; device documentation অনুসরণ করবে।

### Verify Trunk

```cisco
SW1# show interfaces trunk
SW1# show interfaces port-channel 1 switchport
```

---

## 12. Verification Commands

### Show EtherChannel Summary

```cisco
SW1# show etherchannel summary
```

Channel status, protocol এবং member port-এর অবস্থা দেখতে।

### Show Detailed Information

```cisco
SW1# show etherchannel 1 detail
```

### Show Port-Channel Interface

```cisco
SW1# show interfaces port-channel 1
```

### Show LACP Neighbors

```cisco
SW1# show lacp neighbor
```

### Show LACP Counters

```cisco
SW1# show lacp counters
```

### Show EtherChannel Load Balancing

```cisco
SW1# show etherchannel load-balance
```

### Show Running Configuration

```cisco
SW1# show running-config
```

### Show Spanning Tree

```cisco
SW1# show spanning-tree
```

**Note:** কিছু command-এর syntax ও availability IOS/platform অনুযায়ী আলাদা হতে পারে।

---

## 13. Troubleshooting

### Problem 1: EtherChannel Is Not Forming

Check:

```cisco
SW1# show etherchannel summary
SW1# show interfaces status
SW1# show lacp neighbor
SW1# show running-config
```

Possible causes:

* দুই পাশে channel-group number বা mode mismatch
* এক পাশে LACP, অন্য পাশে PAgP
* Physical link down
* Speed/duplex বা Layer 2 settings mismatch
* Unsupported interface combination

### Problem 2: Member Port Is Not Bundled

সম্ভাব্য কারণ:

* Port configuration mismatch
* VLAN বা trunk setting mismatch
* Protocol negotiation ব্যর্থ
* Cable বা interface সমস্যা

Useful commands:

```cisco
SW1# show etherchannel 1 detail
SW1# show interfaces gigabitEthernet0/1
SW1# show interfaces gigabitEthernet0/2
SW1# show logging
```

### Problem 3: VLAN Traffic Is Not Passing

Check:

```cisco
SW1# show vlan brief
SW1# show interfaces trunk
SW1# show interfaces port-channel 1 switchport
```

যাচাই করবে:

* প্রয়োজনীয় VLAN তৈরি আছে কি না
* VLAN trunk allowed list-এ আছে কি না
* দুই পাশের trunk configuration মিলছে কি না
* সংশ্লিষ্ট access port সঠিক VLAN-এ আছে কি না

### Problem 4: Bandwidth Expected Performance Is Not Achieved

সম্ভাব্য কারণ:

* একটি flow একটি member link-এর উপর hash হয়েছে
* Traffic distribution সমান হয়নি
* End device বা upstream network bottleneck
* একটি বা একাধিক member link down

Remember:

**Aggregate bandwidth ≠ Guaranteed bandwidth for one connection.**

---

## 14. Security Best Practices

### 14.1 Use LACP Where Appropriate

LACP negotiation ভুল configuration শনাক্ত করতে সাহায্য করতে পারে, তবে এটি নিজে সম্পূর্ণ security mechanism নয়।

### 14.2 Disable Unused Ports

Unused switch port administratively disable রাখবে।

```cisco
SW1(config)# interface gigabitEthernet0/10
SW1(config-if)# shutdown
```

শুধু নিশ্চিতভাবে unused port-এ এই command প্রয়োগ করবে।

### 14.3 Restrict Trunk VLANs

```cisco
SW1(config)# interface port-channel 1
SW1(config-if)# switchport trunk allowed vlan 10,20,30
```

শুধু প্রয়োজনীয় VLAN allow করবে।

### 14.4 Use Management Security

* SSH ব্যবহার করা
* Strong authentication
* Least privilege
* Configuration backup
* Syslog এবং monitoring
* Regular IOS updates

### 14.5 Monitor Link Changes

```cisco
SW1# show logging
SW1# show interfaces status
SW1# show etherchannel summary
```

Unexpected link changes বা member port failure investigate করবে।

---

## 15. Linux Link Aggregation Commands

Linux-এ Cisco EtherChannel-এর মতো উদ্দেশ্যে bonding ব্যবহার করা যায়। তবে Linux bonding এবং Cisco EtherChannel একই configuration system নয়।

### 15.1 Show Network Interfaces

```bash
ip link show
```

### 15.2 Show IP Addresses

```bash
ip addr show
```

### 15.3 Show Routing Table

```bash
ip route
```

### 15.4 Check NetworkManager Connections

```bash
nmcli connection show
nmcli device status
```

### 15.5 Create a Bond Interface

নিচের উদাহরণটি NetworkManager ব্যবহার করে `bond0` নামে active-backup bond তৈরি করে।

```bash
sudo nmcli connection add \
  type bond \
  con-name bond0 \
  ifname bond0 \
  bond.options "mode=active-backup,miimon=100"
```

### 15.6 Add Two Physical Interfaces

এখানে `enp1s0` ও `enp2s0` উদাহরণস্বরূপ interface name। নিজের system-এর actual interface name ব্যবহার করবে।

```bash
sudo nmcli connection add \
  type ethernet \
  con-name bond0-port1 \
  ifname enp1s0 \
  master bond0
```

```bash
sudo nmcli connection add \
  type ethernet \
  con-name bond0-port2 \
  ifname enp2s0 \
  master bond0
```

### 15.7 Bring Up the Connections

```bash
sudo nmcli connection up bond0
sudo nmcli connection up bond0-port1
sudo nmcli connection up bond0-port2
```

**Warning:** এই commands চালানোর আগে network configuration ও management access নিশ্চিত করবে। ভুল interface নির্বাচন করলে network connectivity হারাতে পারো।

### 15.8 Verify Linux Bonding

```bash
ip link show bond0
```

যদি kernel bonding interface তৈরি হয়ে থাকে, তাহলে:

```bash
cat /proc/net/bonding/bond0
```

এখানে bonding mode এবং member interface-এর status দেখা যেতে পারে।

### Linux LACP Note

Linux-এ LACP mode ব্যবহার করতে bonding configuration-এ `mode=802.3ad` সেট করা যায়, তবে switch-এও compatible LACP configuration প্রয়োজন।

Example option:

```text
mode=802.3ad
```

`active-backup` এবং `802.3ad` এক জিনিস নয়:

* `active-backup`: সাধারণত একটি active link, অন্যটি standby।
* `802.3ad`: compatible LACP partner-এর সঙ্গে একাধিক link aggregate করে।

Source: NetworkManager `nmcli` documentation — https://networkmanager.pages.freedesktop.org/NetworkManager/NetworkManager/nmcli-examples.html

---

## 16. Practice Lab

### Objective

দুটি Cisco switch-এর মধ্যে LACP EtherChannel configure ও verify করা।

### Tasks

* [ ] দুইটি switch তৈরি করা
* [ ] দুইটি Ethernet link সংযোগ করা
* [ ] দুই switch-এ hostname সেট করা
* [ ] Channel-group 1 তৈরি করা
* [ ] LACP active mode configure করা
* [ ] EtherChannel status verify করা
* [ ] Trunk mode configure করা
* [ ] VLAN 10, 20 ও 30 allow করা
* [ ] Configuration save করা
* [ ] একটি member link disconnect করে failover পরীক্ষা করা
* [ ] Link পুনরায় connect করে recovery verify করা

### Expected Verification

```cisco
show etherchannel summary
show lacp neighbor
show interfaces trunk
show running-config
```

### Save the Configuration

```cisco
copy running-config startup-config
```

### Lab Safety

নিজের Packet Tracer lab অথবা অনুমোদিত physical network-এ পরীক্ষা করবে। Production switch-এ configuration পরিবর্তনের আগে backup এবং change approval নিশ্চিত করবে।

---

## 17. Important Terms

| Term             | Meaning                                               |
| ---------------- | ----------------------------------------------------- |
| EtherChannel     | Cisco link aggregation technology                     |
| Link Aggregation | একাধিক physical link-কে logical link হিসেবে group করা |
| LACP             | Link Aggregation Control Protocol                     |
| PAgP             | Port Aggregation Protocol                             |
| Port-Channel     | Logical aggregated interface                          |
| Channel Group    | EtherChannel member ports-এর group                    |
| Redundancy       | Failure হলে বিকল্প link থাকার ব্যবস্থা                |
| Load Balancing   | একাধিক link-এ traffic distribute করা                  |
| Trunk            | একাধিক VLAN-এর traffic বহনকারী link                   |
| STP              | Layer 2 loop প্রতিরোধের protocol                      |
| Active           | LACP negotiation শুরু করে                             |
| Passive          | LACP negotiation-এর অপেক্ষা করে                       |
| Desirable        | PAgP negotiation শুরু করে                             |
| Auto             | PAgP negotiation-এর অপেক্ষা করে                       |
| Static `on`      | Negotiation ছাড়াই manually configured EtherChannel    |

---

## Official References

* Cisco EtherChannel Configuration Guide: https://www.cisco.com/c/en/us/td/docs/switches/lan/c9000/lyr2-fwd/etherchannel/etherchannel-configuration-guide/m_ethernetchannel.html
* Cisco EtherChannel Monitoring and Configuration: https://www.cisco.com/c/en/us/td/docs/switches/lan/c9000/lyr2-fwd/etherchannel/etherchannel-configuration-guide/etherchannels.html
* NetworkManager `nmcli` Examples: https://networkmanager.pages.freedesktop.org/NetworkManager/NetworkManager/nmcli-examples.html

---

**End of Notes**
