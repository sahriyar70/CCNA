#  Spanning Tree Protocol (STP) — Complete CCNA & Cybersecurity Notes

**Category:** Networking | CCNA | Cisco Switching | Network Security
**Level:** Beginner to Intermediate

##  Table of Contents

1. Introduction to STP
2. Why STP Is Necessary
3. Layer 2 Switching Loops
4. Broadcast Storm, MAC Flapping and Duplicate Frames
5. How STP Works
6. Bridge Protocol Data Unit (BPDU)
7. Root Bridge Election
8. Bridge ID and Priority
9. Root Port, Designated Port, Alternate Port and Backup Port
10. STP Port States
11. STP Timers
12. Types of Spanning Tree Protocol
13. STP vs RSTP vs MSTP
14. Cisco STP Configuration Commands
15. Root Bridge Configuration
16. Port Cost and Port Priority
17. PortFast and BPDU Guard
18. Root Guard, Loop Guard and BPDU Filter
19. STP with VLANs
20. STP and EtherChannel
21. Verification Commands
22. Troubleshooting
23. Security Best Practices
24. Packet Tracer Practice Lab
25. Important Terms and Quick Revision

---

# 1. Introduction to STP

**STP stands for Spanning Tree Protocol.**

STP হলো একটি Layer 2 network protocol, যা Ethernet switching network-এ loop প্রতিরোধ করে।

এটি redundant network links-এর মধ্যে একটি loop-free logical topology তৈরি করে।

STP-এর মূল standard হলো IEEE 802.1D। আধুনিক network-এ Rapid Spanning Tree Protocol (RSTP), IEEE 802.1w-এর ধারণা এবং পরবর্তী IEEE standard-গুলোও গুরুত্বপূর্ণ।

### Main Objectives

* Layer 2 switching loop প্রতিরোধ করা
* Broadcast storm প্রতিরোধে সাহায্য করা
* Duplicate frame-এর সমস্যা এড়ানো
* Redundant links ব্যবহার করে network resilience বাড়ানো
* Link failure হলে বিকল্প path চালু করার সুযোগ রাখা

### সহজ উদাহরণ

 তিনটি switch আছে:

```text
          SW1
         /   \
        /     \
      SW2-----SW3
```

তিনটি switch-এর মধ্যে তিনটি link দিয়ে একটি closed loop তৈরি হয়েছে।

STP না থাকলে এই topology-তে Layer 2 frame ঘুরতে থাকতে পারে।

STP একটি redundant path-কে সাধারণ forwarding থেকে বিরত রেখে loop-free topology তৈরি করে।

```text
          SW1
         /   \
        /     \
      SW2-----SW3
             X
        Blocked Path
```

এখানে `X` বোঝাচ্ছে একটি path forwarding-এর জন্য ব্যবহার হচ্ছে না। বাস্তব topology-তে কোন port block হবে, তা STP election ও path calculation-এর উপর নির্ভর করে।

---

# 2. Why STP Is Necessary

Ethernet Layer 2 network-এ frame-এর TTL field থাকে না। তাই Layer 3 IP packet-এর TTL যেভাবে packet-এর অনির্দিষ্ট ঘূর্ণন সীমিত করে, Ethernet frame-এর ক্ষেত্রে তেমন mechanism নেই।

একটি switching loop তৈরি হলে একই frame বারবার network-এ ঘুরতে পারে।

STP ছাড়া redundant links-সহ network-এ নিচের সমস্যাগুলো হতে পারে:

1. Broadcast Storm
2. MAC Address Table Instability
3. Duplicate Frames
4. High CPU Utilization
5. Network Congestion
6. Network Outage

### Redundancy Without STP

```text
          Switch A
          /      \
         /        \
     Switch B----Switch C
         \        /
          \______/
```

এ ধরনের redundant Layer 2 topology-তে STP বা উপযুক্ত loop-prevention mechanism প্রয়োজন।

**মনে রাখবে:** Redundancy নিজে সমস্যা নয়। সঠিক loop-prevention configuration ছাড়া redundant Layer 2 path সমস্যা তৈরি করতে পারে।

---

# 3. Layer 2 Switching Loops

## 3.1 What Is a Switching Loop?

যখন Ethernet frames একাধিক interconnected switch-এর মধ্যে এমনভাবে ঘোরে যে frame forwarding-এর জন্য কোনো loop-free path বজায় থাকে না, তখন তাকে switching loop বলা হয়।

### Example

```text
PC1
 |
SW1
 | \
 |  \
SW2--SW3
```

ধরো, PC1 একটি broadcast frame পাঠাল।

Switch-গুলো frame-টি তাদের forwarding rules অনুযায়ী অন্যান্য link-এ পাঠাতে পারে। যদি topology-তে loop থাকে, তাহলে frame বারবার একাধিক path দিয়ে ঘুরতে পারে।

## 3.2 Broadcast Frames

Broadcast destination MAC address:

```text
FF:FF:FF:FF:FF:FF
```

Switch সাধারণত broadcast frame সংশ্লিষ্ট VLAN-এর অন্যান্য forwarding port-এ flood করে, incoming port বাদ দিয়ে।

Loop থাকলে একই broadcast frame একাধিকবার network-এ ফিরে আসতে পারে।

## 3.3 MAC Address Learning

Switch source MAC address দেখে MAC address table-এ device-এর location শেখে।

Example:

```text
MAC Address         Port
AA:BB:CC:DD:EE:FF   Gi0/1
```

যদি একই source MAC address বিভিন্ন port থেকে বারবার দেখা যায়, তাহলে switch MAC address table-এর entry পরিবর্তন করতে পারে।

একে MAC flapping বলা হয়, যা Layer 2 loop-এর একটি সম্ভাব্য লক্ষণ।

---

# 4. Broadcast Storm, MAC Flapping and Duplicate Frames

| Problem          | Meaning                                       | Impact                       |
| ---------------- | --------------------------------------------- | ---------------------------- |
| Broadcast Storm  | Broadcast traffic অতিরিক্ত পরিমাণে ঘুরতে থাকা | Network congestion           |
| MAC Flapping     | একই MAC address বিভিন্ন port-এ বারবার শেখা    | Unstable forwarding          |
| Duplicate Frames | একই frame একাধিকবার পৌঁছানো                   | Application সমস্যা           |
| CPU Overload     | Excessive traffic/control-plane activity      | Device performance কমে যাওয়া |
| Network Outage   | Network traffic সঠিকভাবে চলতে না পারা         | Service disruption           |

### Cybersecurity Perspective

Layer 2 loop ভুল configuration-এর কারণে হতে পারে। তবে attacker যদি unauthorized switch সংযুক্ত করে বা network configuration পরিবর্তন করে, তাহলে network disruption-ও ঘটতে পারে।

এই কারণে STP-এর পাশাপাশি port security, BPDU Guard, access control এবং network monitoring গুরুত্বপূর্ণ।

---

# 5. How STP Works

STP কয়েকটি ধাপে কাজ করে।

## Step 1: Switches Exchange BPDUs

Switch-গুলো BPDU (Bridge Protocol Data Unit) বিনিময় করে।

BPDU-তে Root Bridge-এর পরিচয়, path cost এবং spanning-tree topology সম্পর্কে প্রয়োজনীয় তথ্য থাকে।

## Step 2: Elect a Root Bridge

STP একটি Root Bridge নির্বাচন করে।

সাধারণভাবে যে switch-এর Bridge ID সবচেয়ে ছোট, সেটি Root Bridge হয়।

## Step 3: Select Root Ports

Root Bridge ছাড়া প্রতিটি switch তার Root Bridge-এ যাওয়ার সবচেয়ে ভালো path নির্বাচন করে।

এই path-এর দিকে থাকা port-টি Root Port হয়।

## Step 4: Select Designated Ports

প্রতিটি network segment-এর জন্য একটি Designated Port নির্বাচন করা হয়।

Designated Port সেই segment-এ loop-free forwarding topology বজায় রাখতে অংশ নেয়।

## Step 5: Block Redundant Paths

যেসব redundant path forwarding করলে loop হতে পারে, সেগুলোকে STP forwarding থেকে বিরত রাখে।

## Step 6: Recalculate After Failure

কোনো active link বা switch failure হলে STP topology পুনরায় হিসাব করতে পারে এবং উপযুক্ত redundant path চালু করতে পারে।

```text
BPDU Exchange
     |
     v
Root Bridge Election
     |
     v
Root Port Selection
     |
     v
Designated Port Selection
     |
     v
Loop-Free Topology
     |
     v
Forwarding / Standby Paths
```

---

# 6. Bridge Protocol Data Unit (BPDU)

**BPDU = Bridge Protocol Data Unit**

BPDU হলো STP-সংশ্লিষ্ট control information বহনকারী Layer 2 frame।

Switch-গুলো BPDU ব্যবহার করে spanning-tree topology সম্পর্কে তথ্য বিনিময় করে।

### BPDU Information

* Root Bridge ID
* Sender Bridge ID
* Root Path Cost
* Sender Port ID
* Bridge and port information
* Protocol-specific flags and timers

### BPDU-এর কাজ

1. Root Bridge election-এ সাহায্য করা
2. Path cost তুলনা করা
3. Port roles নির্ধারণে সাহায্য করা
4. Topology change সম্পর্কে তথ্য আদান-প্রদান করা
5. Redundant path-এর loop-prevention decision সমর্থন করা

### BPDU কেন Security-এর জন্য গুরুত্বপূর্ণ?

কোনো unauthorized switch যদি network-এ যুক্ত হয় এবং superior BPDU পাঠায়, তাহলে Root Bridge election প্রভাবিত হতে পারে।

এটি ঠেকাতে সঠিক জায়গায় Root Guard, BPDU Guard এবং network access control ব্যবহার করা হয়।

---

# 7. Root Bridge Election

Root Bridge হলো spanning-tree topology-এর কেন্দ্রীয় reference switch।

একটি spanning-tree instance-এর জন্য একটি Root Bridge নির্বাচিত হয়।

PVST+ বা Rapid PVST+-এর মতো protocol-এ বিভিন্ন VLAN-এর জন্য আলাদা Root Bridge নির্বাচিত হতে পারে।

### Root Bridge Selection Rule

**Lowest Bridge ID wins.**

Bridge ID-এর মধ্যে রয়েছে:

* Bridge Priority
* Extended System ID, সাধারণত VLAN ID-সহ
* Switch MAC address

সহজভাবে, একই priority এবং একই extended system ID হলে সাধারণত সবচেয়ে ছোট MAC address-সহ switch নির্বাচিত হয়।

### Example

| Switch | Priority | MAC Address       |
| ------ | -------: | ----------------- |
| SW1    |    32768 | 00:00:00:00:00:01 |
| SW2    |    32768 | 00:00:00:00:00:02 |
| SW3    |    32768 | 00:00:00:00:00:03 |

এখানে একই priority এবং একই VLAN-এর ক্ষেত্রে SW1-এর MAC address সবচেয়ে ছোট।

তাই SW1 Root Bridge নির্বাচিত হবে।

### Important

Root Bridge নির্বাচনের জন্য switch-এর hostname বা IP address ব্যবহার করা হয় না।

মূল বিষয় হলো Bridge ID।

---

# 8. Bridge ID and Priority

Bridge ID বা BID হলো spanning-tree election-এর একটি গুরুত্বপূর্ণ identifier।

সাধারণ ধারণা:

```text
Bridge ID
   |
   +-- Bridge Priority
   |
   +-- Extended System ID
   |
   +-- MAC Address
```

### Default Priority

অনেক Cisco Catalyst configuration-এ default base priority হলো:

```text
32768
```

Extended System ID ব্যবহারের কারণে `show spanning-tree` output-এ displayed bridge priority VLAN ID অনুযায়ী ভিন্ন হতে পারে।

উদাহরণস্বরূপ, VLAN 10-এর ক্ষেত্রে default base priority 32768 হলে displayed priority হতে পারে 32778।

### Priority Values

সাধারণ Cisco STP configuration-এ priority নির্দিষ্ট increment-এ সেট করা হয়, সাধারণত 4096-এর গুণিতক।

উদাহরণ:

```text
0
4096
8192
12288
16384
20480
24576
28672
32768
36864
40960
45056
49152
53248
57344
61440
```

Lower priority মানে Root Bridge নির্বাচিত হওয়ার বেশি সম্ভাবনা।

তবে শেষ সিদ্ধান্ত Bridge ID-এর সম্পূর্ণ তুলনার উপর নির্ভর করে।

---

# 9. STP Port Roles

STP প্রতিটি port-কে topology অনুযায়ী একটি role দেয়।

## 9.1 Root Port (RP)

Root Bridge ছাড়া প্রতিটি switch-এ সাধারণত একটি Root Port থাকে।

এই port Root Bridge-এ যাওয়ার best path নির্দেশ করে।

```text
SW2 -------- SW1 (Root Bridge)
       RP
```

Root Port সাধারণত forwarding state-এ থাকে।

## 9.2 Designated Port (DP)

প্রতিটি Layer 2 segment-এর জন্য একটি Designated Port নির্বাচিত হয়।

এটি segment-এ loop-free topology-এর অংশ হিসেবে traffic forward করে।

Root Bridge-এর active spanning-tree topology-তে সাধারণত সব operational participating ports Designated Port হিসেবে কাজ করে।

## 9.3 Alternate Port

Alternate Port হলো Root Bridge-এর দিকে একটি বিকল্প path।

RSTP-তে এই port সাধারণত forwarding state-এ থাকে না; এটি প্রয়োজন হলে বিকল্প path হিসেবে ব্যবহৃত হতে পারে।

## 9.4 Backup Port

Backup Port একই shared network segment-এ Designated Port-এর বিকল্প হিসেবে থাকতে পারে।

আধুনিক switched Ethernet network-এ এটি তুলনামূলকভাবে বিরল।

### Port Roles Summary

| Port Role       | Main Function                           |
| --------------- | --------------------------------------- |
| Root Port       | Root Bridge-এর দিকে best path           |
| Designated Port | Segment-এর forwarding port              |
| Alternate Port  | Root Bridge-এর বিকল্প path              |
| Backup Port     | একই segment-এ Designated Port-এর backup |

**Important:** Root Port ও Designated Port সাধারণত forwarding করে। Alternate ও Backup Port সাধারণত forwarding topology-এর বাইরে থাকে।

---

# 10. STP Port States

Classic STP (IEEE 802.1D)-তে পাঁচটি port state শেখানো হয়।

## 10.1 Disabled

Port administratively disabled বা STP-এর operational participation-এর বাইরে থাকে।

## 10.2 Blocking

Port সাধারণ user traffic forward করে না এবং MAC address learning করে না।

## 10.3 Listening

Port topology information ও BPDUs process করে, কিন্তু user traffic forward করে না এবং MAC address learning করে না।

## 10.4 Learning

Port MAC address শেখে, কিন্তু user traffic forward করে না।

## 10.5 Forwarding

Port user traffic forward করে এবং MAC address learning করে।

### Classic STP State Flow

```text
Blocking
   |
   v
Listening
   |
   v
Learning
   |
   v
Forwarding
```

সব port একইভাবে এই sequence অতিক্রম করে না; transition topology, port role ও protocol behavior-এর উপর নির্ভর করে।

### RSTP Port States

RSTP তিনটি operational state ব্যবহার করে:

| RSTP State | Meaning                |
| ---------- | ---------------------- |
| Discarding | Traffic forward করে না |
| Learning   | MAC address শেখে       |
| Forwarding | Traffic forward করে    |

RSTP-এর Discarding state classic STP-এর Disabled, Blocking এবং Listening state-এর operational behavior-কে একত্র করে।

---

# 11. STP Timers

Classic STP-তে তিনটি গুরুত্বপূর্ণ timer রয়েছে।

## 11.1 Hello Time

Switch কত সময় পরপর BPDU পাঠায়, তা নির্ধারণ করে।

সাধারণ default:

```text
2 seconds
```

## 11.2 Forward Delay

Listening ও Learning state-এ কত সময় থাকবে, তার সঙ্গে সম্পর্কিত।

সাধারণ default:

```text
15 seconds
```

## 11.3 Max Age

Root information বা BPDU information কতক্ষণ পর্যন্ত valid ধরে রাখা হবে, তার সঙ্গে সম্পর্কিত।

সাধারণ default:

```text
20 seconds
```

### Default Timer Summary

| Timer         | Typical Default |
| ------------- | --------------: |
| Hello Time    |       2 seconds |
| Forward Delay |      15 seconds |
| Max Age       |      20 seconds |

এই default values classic STP-এর জন্য। RSTP সাধারণত faster convergence অর্জন করতে পারে এবং সব ক্ষেত্রে classic STP-এর মতো timer-based waiting-এর উপর নির্ভর করে না।

**পরামর্শ:** প্রয়োজন না হলে timer manually পরিবর্তন করবে না। Root Bridge ও topology design সঠিকভাবে configure করাই বেশি গুরুত্বপূর্ণ।

---

# 12. Types of Spanning Tree Protocol

## 12.1 STP — IEEE 802.1D

Classic Spanning Tree Protocol।

বৈশিষ্ট্য:

* Layer 2 loop prevention
* Root Bridge election
* Root/Designated Port selection
* Classic port states
* তুলনামূলকভাবে ধীর convergence

## 12.2 RSTP — IEEE 802.1w

Rapid Spanning Tree Protocol।

বৈশিষ্ট্য:

* Faster convergence
* Alternate ও Backup port roles
* Discarding, Learning ও Forwarding states
* Point-to-point link-এ rapid transition mechanism

## 12.3 PVST+

Per-VLAN Spanning Tree Plus হলো Cisco technology, যেখানে প্রতিটি VLAN-এর জন্য আলাদা spanning-tree instance পরিচালিত হয়।

সুবিধা:

* VLAN অনুযায়ী Root Bridge নির্বাচন
* VLAN অনুযায়ী path control
* VLAN-ভিত্তিক load distribution

## 12.4 Rapid PVST+

Cisco-এর rapid per-VLAN spanning-tree implementation।

এটি প্রতিটি VLAN-এর জন্য rapid spanning-tree behavior প্রদান করে।

## 12.5 MSTP — Multiple Spanning Tree Protocol

MSTP একাধিক VLAN-কে এক বা একাধিক spanning-tree instance-এ map করতে পারে।

সুবিধা:

* অনেক VLAN-এর ক্ষেত্রে instance সংখ্যা কমানো
* Network resource ব্যবহার কার্যকর করা
* VLAN group অনুযায়ী spanning-tree topology design

MST configuration-এ region name, revision এবং VLAN-to-instance mapping-এর সামঞ্জস্য গুরুত্বপূর্ণ।

---

# 13. STP vs RSTP vs MSTP

| Feature       | STP                      | RSTP                                                        | MSTP                                                        |
| ------------- | ------------------------ | ----------------------------------------------------------- | ----------------------------------------------------------- |
| Main standard | IEEE 802.1D              | IEEE 802.1w concepts, now incorporated into later standards | IEEE 802.1s concepts, now incorporated into later standards |
| Convergence   | তুলনামূলক ধীর            | সাধারণত দ্রুত                                               | সাধারণত দ্রুত                                               |
| Port States   | 5 classic states         | 3 states                                                    | RSTP-based states                                           |
| VLAN Handling | Implementation-dependent | Implementation-dependent                                    | এক instance-এ একাধিক VLAN map করা যায়                       |
| Main Purpose  | Loop prevention          | Faster loop-free recovery                                   | Multiple VLANs-এর জন্য scalable topology                    |

---

# 14. Cisco STP Configuration Commands

নিচের commands Cisco IOS/Catalyst-এর সাধারণ উদাহরণ। Packet Tracer ও switch model অনুযায়ী support ও syntax ভিন্ন হতে পারে।

## 14.1 Enter Privileged Mode

```cisco
Switch> enable
```

## 14.2 Enter Configuration Mode

```cisco
Switch# configure terminal
```

## 14.3 Set Hostname

```cisco
Switch(config)# hostname SW1
```

## 14.4 Check STP Information

```cisco
SW1# show spanning-tree
```

## 14.5 Check a Specific VLAN

```cisco
SW1# show spanning-tree vlan 10
```

## 14.6 Check STP Summary

```cisco
SW1# show spanning-tree summary
```

## 14.7 Check Interface Status

```cisco
SW1# show interfaces status
```

## 14.8 Check Running Configuration

```cisco
SW1# show running-config
```

## 14.9 Save Configuration

```cisco
SW1# copy running-config startup-config
```

---

# 15. Root Bridge Configuration

Network design অনুযায়ী Root Bridge নির্ধারণ করা উচিত। সাধারণত distribution/core layer-এর উপযুক্ত switch-কে Root Bridge হিসেবে নির্বাচন করা হয়।

## 15.1 Configure Primary Root

```cisco
SW1# configure terminal
SW1(config)# spanning-tree vlan 10 root primary
SW1(config)# end
```

এটি VLAN 10-এর জন্য SW1-কে primary root করার চেষ্টা করে, platform-এর supported logic অনুযায়ী priority নির্ধারণ করে।

## 15.2 Configure Secondary Root

```cisco
SW2# configure terminal
SW2(config)# spanning-tree vlan 10 root secondary
SW2(config)# end
```

এটি SW2-কে VLAN 10-এর জন্য secondary root candidate হিসেবে configure করে।

## 15.3 Configure Explicit Priority

```cisco
SW1# configure terminal
SW1(config)# spanning-tree vlan 10 priority 4096
SW1(config)# end
```

এখানে VLAN 10-এর জন্য SW1-এর STP priority কমানো হয়েছে।

## 15.4 Verify Root Bridge

```cisco
SW1# show spanning-tree vlan 10
```

Output-এ `This bridge is the root` বা root bridge-এর পরিচয় যাচাই করবে।

**Important:** Root primary/secondary command ও priority support device/IOS অনুযায়ী যাচাই করবে। Configuration-এর পরে output দেখে নিশ্চিত হতে হবে যে প্রত্যাশিত switch-ই Root Bridge হয়েছে।

---

# 16. Port Cost and Port Priority

STP শুধু Root Bridge-এর priority দেখে না। Root Bridge-এর দিকে best path নির্ধারণ করার জন্য path cost-ও ব্যবহার করে।

## 16.1 Path Cost

Path cost link speed-এর সঙ্গে সম্পর্কিত।

সাধারণভাবে বেশি bandwidth-এর link-এর STP cost কম হয়।

উদাহরণস্বরূপ, প্রচলিত short path-cost method-এ:

| Link Speed | Typical Cost |
| ---------- | -----------: |
| 10 Mbps    |          100 |
| 100 Mbps   |           19 |
| 1 Gbps     |            4 |
| 10 Gbps    |            2 |

নির্দিষ্ট value path-cost method ও platform অনুযায়ী ভিন্ন হতে পারে।

## 16.2 Configure Port Cost

```cisco
SW1# configure terminal
SW1(config)# interface gigabitEthernet0/1
SW1(config-if)# spanning-tree vlan 10 cost 10
SW1(config-if)# end
```

এটি supported Cisco IOS platform-এ VLAN 10-এর জন্য ওই interface-এর STP path cost configure করার একটি উদাহরণ।

## 16.3 Configure Port Priority

```cisco
SW1# configure terminal
SW1(config)# interface gigabitEthernet0/1
SW1(config-if)# spanning-tree vlan 10 port-priority 64
SW1(config-if)# end
```

Port priority একই ধরনের path-cost পরিস্থিতিতে port selection-এর tie-breaker হিসেবে প্রভাব ফেলতে পারে।

## 16.4 Root Port Selection Tie-Breakers

Root Port নির্ধারণে সাধারণভাবে নিচের বিষয়গুলো ধারাবাহিকভাবে বিবেচনা করা হয়:

1. সর্বনিম্ন Root Bridge ID
2. সর্বনিম্ন Root Path Cost
3. সর্বনিম্ন Sender Bridge ID
4. সর্বনিম্ন Sender Port ID
5. প্রয়োজন হলে স্থানীয় Port ID

সঠিক tie-break sequence ও details protocol implementation অনুযায়ী যাচাই করতে হবে।

---

# 17. PortFast and BPDU Guard

## 17.1 PortFast

PortFast সাধারণত end-device-facing access port-কে দ্রুত forwarding state-এ যেতে সাহায্য করে।

এটি PC, printer বা server-এর মতো endpoint-এ ব্যবহারের জন্য উপযুক্ত, যখন portটি একটি সাধারণ endpoint-এ সংযুক্ত।

### Configuration

```cisco
SW1# configure terminal
SW1(config)# interface gigabitEthernet0/10
SW1(config-if)# spanning-tree portfast
SW1(config-if)# end
```

কিছু IOS release-এ `spanning-tree portfast edge` syntax দেখা যেতে পারে।

### Important Warning

Switch-to-switch link-এ PortFast ব্যবহার করা বিপজ্জনক হতে পারে। এতে ভুলভাবে loop তৈরি হলে network disruption ঘটতে পারে।

## 17.2 BPDU Guard

BPDU Guard PortFast-enabled edge port-এ BPDU গ্রহণ করলে port-কে সাধারণত err-disabled state-এ নিয়ে যায়।

এটি অনাকাঙ্ক্ষিত switch সংযোগ শনাক্ত করে network protection-এ সাহায্য করে।

### Interface-Level Configuration

```cisco
SW1# configure terminal
SW1(config)# interface gigabitEthernet0/10
SW1(config-if)# spanning-tree portfast
SW1(config-if)# spanning-tree bpduguard enable
SW1(config-if)# end
```

### Global Configuration

```cisco
SW1(config)# spanning-tree portfast default
SW1(config)# spanning-tree portfast bpduguard default
```

Global command-এর support ও behavior platform অনুযায়ী যাচাই করবে।

---

# 18. Root Guard, Loop Guard and BPDU Filter

## 18.1 Root Guard

Root Guard কোনো নির্দিষ্ট interface দিয়ে superior BPDU-এর কারণে অনাকাঙ্ক্ষিত Root Bridge election প্রভাবিত হওয়া ঠেকাতে সাহায্য করে।

সাধারণত এমন port-এ ব্যবহার করা হয় যেখানে downstream switch-কে Root Bridge হওয়ার অনুমতি দেওয়া উচিত নয়।

### Configuration

```cisco
SW1(config)# interface gigabitEthernet0/2
SW1(config-if)# spanning-tree guard root
```

## 18.2 Loop Guard

Loop Guard নির্দিষ্ট পরিস্থিতিতে প্রত্যাশিত BPDU বন্ধ হয়ে গেলে port ভুলভাবে forwarding state-এ চলে যাওয়া থেকে সুরক্ষা দিতে পারে।

এটি unidirectional link failure-এর মতো পরিস্থিতিতে কাজে লাগতে পারে।

### Configuration

```cisco
SW1(config)# spanning-tree loopguard default
```

এই global command-এর support ও scope platform অনুযায়ী যাচাই করবে।

## 18.3 BPDU Filter

BPDU Filter BPDU sending/receiving suppress করতে পারে।

এটি ভুলভাবে ব্যবহার করলে STP-এর loop-prevention protection দুর্বল হতে পারে।

**Best Practice:** BPDU Filter ব্যবহার করার আগে topology ও platform documentation ভালোভাবে বুঝতে হবে। সাধারণ edge-port protection-এর জন্য PortFast এবং BPDU Guard সাধারণত বেশি উপযুক্ত পদ্ধতি।

---

# 19. STP with VLANs

VLAN একটি Layer 2 broadcast domain তৈরি করে। VLAN-এর মধ্যে loop prevention গুরুত্বপূর্ণ।

Cisco-এর PVST+ এবং Rapid PVST+ প্রতিটি VLAN-এর জন্য আলাদা spanning-tree instance পরিচালনা করতে পারে।

### Example VLANs

```text
VLAN 10 → 192.168.10.0/24
VLAN 20 → 192.168.20.0/24
VLAN 30 → 192.168.30.0/24
```

এই IP subnets শুধু উদাহরণ। STP নিজে IP address নির্বাচন বা inter-VLAN routing করে না।

## Configure STP Mode

```cisco
SW1# configure terminal
SW1(config)# spanning-tree mode rapid-pvst
SW1(config)# end
```

এটি supported Cisco switch-এ Rapid PVST+ mode নির্বাচন করার উদাহরণ।

## Configure Root for VLAN 10

```cisco
SW1(config)# spanning-tree vlan 10 root primary
```

## Configure Root for VLAN 20

```cisco
SW2(config)# spanning-tree vlan 20 root primary
```

এই পদ্ধতিতে VLAN অনুযায়ী ভিন্ন switch-কে root করা যায়, ফলে উপযুক্ত design-এ traffic path distribution করা সম্ভব।

---

# 20. STP and EtherChannel

EtherChannel হলো একাধিক physical Ethernet link-কে একটি logical Port-Channel-এ একত্র করা।

STP সাধারণত সঠিকভাবে formed Layer 2 EtherChannel-কে একটি logical port হিসেবে বিবেচনা করে।

### Example

```text
SW1                          SW2
 |                            |
 Gi0/1 ==================== Gi0/1
 Gi0/2 ==================== Gi0/2
          EtherChannel
             Po1
```

দুটি physical link সঠিকভাবে Port-Channel-এ bundle হলে STP সাধারণত প্রতিটি member link-কে আলাদা redundant path হিসেবে গণনা করে না।

### Verify EtherChannel

```cisco
SW1# show etherchannel summary
```

### Verify STP

```cisco
SW1# show spanning-tree
```

### Important

EtherChannel member ports-এর configuration mismatch হলে channel তৈরি নাও হতে পারে। EtherChannel সঠিকভাবে গঠন হয়েছে কি না, তা যাচাই করা জরুরি।

---

# 21. Verification Commands

| Command                              | Purpose                            |
| ------------------------------------ | ---------------------------------- |
| `show spanning-tree`                 | STP-এর বিস্তারিত তথ্য              |
| `show spanning-tree vlan 10`         | VLAN 10-এর STP information         |
| `show spanning-tree summary`         | STP mode ও summary                 |
| `show spanning-tree root`            | Root bridge information            |
| `show spanning-tree interface gi0/1` | Interface-specific STP information |
| `show spanning-tree detail`          | Detailed topology information      |
| `show spanning-tree blockedports`    | Supported platform-এ blocked ports |
| `show interfaces status`             | Interface status                   |
| `show mac address-table`             | MAC address table                  |
| `show logging`                       | System logs                        |
| `show etherchannel summary`          | EtherChannel status                |

Interface name ও command availability switch model/IOS অনুযায়ী পরিবর্তিত হতে পারে।

---

# 22. Troubleshooting

## Problem 1: Unexpected Root Bridge

### Check

```cisco
SW1# show spanning-tree vlan 10
```

### Possible Causes

* Switch priority প্রত্যাশার চেয়ে বেশি
* অন্য switch-এর Bridge ID কম
* VLAN-specific root configuration ভুল
* Topology পরিবর্তিত হয়েছে

### Solution

সঠিক root candidate নির্বাচন করে priority configure করো এবং output দিয়ে verify করো।

## Problem 2: Port Is Blocking

### Check

```cisco
SW1# show spanning-tree
```

### Possible Causes

* STP loop prevention-এর জন্য port blocking/alternate state-এ আছে
* Redundant path রয়েছে
* Port role বা path cost-এর কারণে ওই path নির্বাচিত হয়নি

### Solution

প্রথমে যাচাই করো blocking স্বাভাবিক কি না। STP loop prevention-এর জন্য কোনো port forwarding থেকে বিরত রাখলে সেটি সবসময় fault নয়।

## Problem 3: Network Slow or Broadcast Storm

### Check

```cisco
SW1# show spanning-tree
SW1# show mac address-table
SW1# show interfaces status
SW1# show logging
```

### Possible Causes

* Physical switching loop
* Incorrect EtherChannel configuration
* Unauthorized switch connection
* STP disabled বা ভুলভাবে configured
* Excessive broadcast traffic

### Solution

Topology ও logs যাচাই করো। অজানা link অন্ধভাবে disconnect বা STP disable না করে অনুমোদিত maintenance procedure অনুসরণ করো।

## Problem 4: Port Is Err-Disabled

### Check

```cisco
SW1# show interfaces status
SW1# show logging
```

BPDU Guard trigger হয়ে থাকতে পারে।

### Recovery

প্রথমে port-এ অপ্রত্যাশিত switch বা loop-এর কারণ খুঁজে বের করো। কারণ ঠিক না করে port চালু করলে network আবার disrupt হতে পারে।

প্রয়োজন হলে:

```cisco
SW1(config)# interface gigabitEthernet0/10
SW1(config-if)# shutdown
SW1(config-if)# no shutdown
```

এটি শুধু উপযুক্ত পরিস্থিতিতে এবং root cause সমাধানের পরে প্রয়োগ করবে।

---

# 23. Security Best Practices

1. Network topology document করে রাখা।
2. Root Bridge নির্ধারিত ও নিয়ন্ত্রিত রাখা।
3. End-device-facing port-এ প্রয়োজন অনুযায়ী PortFast এবং BPDU Guard ব্যবহার করা।
4. উপযুক্ত uplink-এ Root Guard ব্যবহার করা।
5. প্রয়োজন অনুযায়ী Loop Guard ও অন্যান্য protection feature configure করা।
6. Unused switch ports disable রাখা।
7. Unauthorized physical switch connection প্রতিরোধ করা।
8. STP topology changes ও MAC flapping monitor করা।
9. Running configuration backup রাখা।
10. Production network-এ পরিবর্তনের আগে approval ও rollback plan রাখা।

### Security Reminder

STP মূলত Layer 2 loop prevention protocol। এটি নিজে authentication, encryption বা সম্পূর্ণ network access control দেয় না।

---

# 24. Packet Tracer Practice Lab

## Objective

তিনটি switch ব্যবহার করে STP Root Bridge election এবং redundant link-এর behavior পর্যবেক্ষণ করা।

### Required Devices

* 3 Cisco switches
* Ethernet cables
* Cisco Packet Tracer

### Topology

```text
             SW1
            /    \
           /      \
         SW2------SW3
```

তিনটি switch-এর মধ্যে তিনটি link সংযুক্ত করবে।

### Task 1: Configure Hostnames

SW1:

```cisco
enable
configure terminal
hostname SW1
end
```

SW2:

```cisco
enable
configure terminal
hostname SW2
end
```

SW3:

```cisco
enable
configure terminal
hostname SW3
end
```

### Task 2: Configure Rapid PVST+

প্রতিটি switch-এ:

```cisco
enable
configure terminal
spanning-tree mode rapid-pvst
end
```

### Task 3: Make SW1 the Root Bridge

```cisco
SW1# configure terminal
SW1(config)# spanning-tree vlan 1 root primary
SW1(config)# end
```

### Task 4: Verify the Root Bridge

```cisco
SW1# show spanning-tree vlan 1
SW2# show spanning-tree vlan 1
SW3# show spanning-tree vlan 1
```

যাচাই করবে:

* কোন switch Root Bridge হয়েছে
* কোন port Root Port
* কোন port Designated Port
* কোন port Alternate/Discarding অবস্থায় আছে

### Task 5: Test Redundancy

1. সব link চালু রেখে topology স্থিতিশীল হতে দাও।
2. STP output দেখে কোন port forwarding করছে না তা চিহ্নিত করো।
3. একটি active link বিচ্ছিন্ন করো।
4. দেখো বিকল্প path সক্রিয় হয় কি না।
5. Link পুনরায় সংযুক্ত করে topology আবার verify করো।

**Expected Result:** STP loop-free topology বজায় রাখবে। RSTP ব্যবহার করলে উপযুক্ত topology-তে recovery সাধারণত classic STP-এর তুলনায় দ্রুত হতে পারে।

### Task 6: Save Configuration

```cisco
copy running-config startup-config
```

### Lab Checklist

* [ ] STP mode verify করেছি
* [ ] Root Bridge শনাক্ত করেছি
* [ ] Root Port শনাক্ত করেছি
* [ ] Designated Port শনাক্ত করেছি
* [ ] Alternate/blocked path শনাক্ত করেছি
* [ ] Link failure test করেছি
* [ ] Recovery verify করেছি
* [ ] Configuration save করেছি

---

# 25. Important Terms and Quick Revision

| Term            | সহজ অর্থ                                                                 |
| --------------- | ------------------------------------------------------------------------ |
| STP             | Layer 2 loop prevention protocol                                         |
| Root Bridge     | Spanning-tree topology-এর মূল reference switch                           |
| Bridge ID       | Root election-এর জন্য ব্যবহৃত identifier                                 |
| BPDU            | STP control information বহনকারী frame                                    |
| Root Port       | Root Bridge-এর দিকে best path                                            |
| Designated Port | Segment-এর নির্বাচিত forwarding port                                     |
| Alternate Port  | বিকল্প root path                                                         |
| Backup Port     | একই shared segment-এ Designated Port-এর backup                           |
| Port Cost       | Root Bridge-এর দিকে path নির্বাচনে ব্যবহৃত cost                          |
| PortFast        | Endpoint port দ্রুত forwarding-এ যেতে সাহায্য করে                        |
| BPDU Guard      | Protected edge port-এ BPDU এলে port disable করতে পারে                    |
| Root Guard      | অনাকাঙ্ক্ষিত superior BPDU-এর প্রভাব সীমিত করে                           |
| Loop Guard      | কিছু BPDU-loss পরিস্থিতিতে অনাকাঙ্ক্ষিত forwarding প্রতিরোধে সাহায্য করে |
| RSTP            | Rapid Spanning Tree Protocol                                             |
| PVST+           | VLAN-প্রতি spanning-tree instance                                        |
| Rapid PVST+     | VLAN-প্রতি rapid spanning-tree implementation                            |
| MSTP            | একাধিক VLAN-কে spanning-tree instance-এ map করার protocol                |
| Convergence     | Topology পরিবর্তনের পর network-এর নতুন স্থিতিশীল অবস্থায় পৌঁছানো         |

##  Memory Tricks

* **STP:** Switch loop থামায়।
* **Root Bridge:** Reference switch।
* **Root Port:** Root-এর দিকে best path।
* **Designated Port:** Segment-এর forwarding path।
* **Alternate Port:** Backup path।
* **BPDU:** Switch-গুলোর STP control message।
* **PortFast:** End device দ্রুত connect হয়।
* **BPDU Guard:** Edge port-এ unexpected BPDU পেলে protection।
* **RSTP:** Faster spanning-tree convergence।
* **MSTP:** একাধিক VLAN-এর জন্য grouped spanning-tree instances।

---

## Official References

* Cisco IOS XE STP Configuration Guide: https://www.cisco.com/c/en/us/td/docs/switches/lan/c9000/lyr2-fwd/stp/stp-configuration-guide/m-stp.html
* Cisco PortFast and BPDU Guard: https://www.cisco.com/c/en/us/support/docs/lan-switching/spanning-tree-protocol/10586-65.html
* Cisco STP Loop Guard and BPDU Guard: https://www.cisco.com/c/en/us/support/docs/lan-switching/spanning-tree-protocol-stp-8021d/218321-configure-stp-with-loop-guard-and-bpdu-s.html

**End of Notes**
