# DHCP, LAN Configuration & ARP — নেটওয়ার্কিং নোট

## সূচিপত্র

1. [LAN Configuration](#1-lan-configuration)
2. [LAN Components](#2-lan-components)
3. [Basic LAN Configuration](#3-basic-lan-configuration)
4. [DHCP](#4-dhcp)
5. [DHCP DORA Process](#5-dhcp-dora-process)
6. [DHCP Configuration](#6-dhcp-configuration)
7. [DHCP Important Ports](#7-dhcp-important-ports)
8. [ARP](#8-arp)
9. [How ARP Works](#9-how-arp-works)
10. [ARP Cache](#10-arp-cache)
11. [ARP Commands](#11-arp-commands)
12. [ARP Security Risks](#12-arp-security-risks)
13. [DHCP vs ARP](#13-dhcp-vs-arp)
14. [Network Communication Flow](#14-network-communication-flow)
15. [Quick Reference](#15-quick-reference)

---

# ১. LAN Configuration

## LAN কী?

**LAN (Local Area Network)** হলো এমন একটি নেটওয়ার্ক, যেখানে সীমিত ভৌগোলিক এলাকার মধ্যে একাধিক ডিভাইসকে সংযুক্ত করা হয়।

উদাহরণ:

* বাসার নেটওয়ার্ক
* অফিসের নেটওয়ার্ক
* স্কুলের নেটওয়ার্ক
* কম্পিউটার ল্যাব
* ছোট প্রতিষ্ঠানের নেটওয়ার্ক

উদাহরণ:

```text
PC ─────┐
Laptop ─┤
Phone ──┼── Switch ─── Router ─── Internet
Printer ┤
Server ─┘
```

---

# ২. LAN Components

একটি সাধারণ LAN-এ থাকতে পারে:

| ডিভাইস       | কাজ                                 |
| ------------ | ----------------------------------- |
| PC           | End Device                          |
| Laptop       | End Device                          |
| Switch       | একই LAN-এর ডিভাইসগুলোকে সংযুক্ত করে |
| Router       | বিভিন্ন নেটওয়ার্ককে সংযুক্ত করে    |
| Access Point | Wireless সংযোগ প্রদান করে           |
| Server       | বিভিন্ন Network Service প্রদান করে  |
| Printer      | Network Resource হিসেবে কাজ করে     |

### গুরুত্বপূর্ণ Network Information

একটি ডিভাইসের মধ্যে সাধারণত থাকতে পারে:

* IP Address
* MAC Address
* Subnet Mask
* Default Gateway
* DNS Server

উদাহরণ:

```text
IP Address      : 192.168.1.10
Subnet Mask     : 255.255.255.0
Default Gateway : 192.168.1.1
DNS Server      : 8.8.8.8
```

---

# ৩. Basic LAN Configuration

ধরা যাক আমাদের নেটওয়ার্কটি এমন:

```text
Router
192.168.1.1
     │
     │
   Switch
  ┌──┼────┐
  │  │    │
 PC1 PC2  PC3
```

উদাহরণ Configuration:

| ডিভাইস | IP Address   | Subnet Mask   | Gateway     |
| ------ | ------------ | ------------- | ----------- |
| Router | 192.168.1.1  | 255.255.255.0 | —           |
| PC1    | 192.168.1.10 | 255.255.255.0 | 192.168.1.1 |
| PC2    | 192.168.1.20 | 255.255.255.0 | 192.168.1.1 |
| PC3    | 192.168.1.30 | 255.255.255.0 | 192.168.1.1 |

সবগুলো ডিভাইস একই Network-এর অন্তর্ভুক্ত:

```text
192.168.1.0/24
```

---

## LAN Connectivity পরীক্ষা

Router-এর সাথে যোগাযোগ পরীক্ষা করতে:

```bash
ping 192.168.1.1
```

অন্য PC-এর সাথে পরীক্ষা করতে:

```bash
ping 192.168.1.20
```

ডিভাইসগুলো সফলভাবে একে অপরের সাথে যোগাযোগ করতে পারলে basic LAN connectivity কাজ করছে।

---

# ৪. DHCP

## DHCP কী?

**DHCP (Dynamic Host Configuration Protocol)** এমন একটি Protocol, যা Client Device-কে স্বয়ংক্রিয়ভাবে Network Configuration প্রদান করে।

প্রতিটি ডিভাইসে manually configuration না করে DHCP Server স্বয়ংক্রিয়ভাবে দিতে পারে:

* IP Address
* Subnet Mask
* Default Gateway
* DNS Server
* Lease Information

উদাহরণ:

```text
DHCP Server
     │
     ├── IP Address
     ├── Subnet Mask
     ├── Gateway
     └── DNS
          ↓
       Client PC
```

---

# ৫. DHCP DORA Process

DHCP-এর সবচেয়ে গুরুত্বপূর্ণ প্রক্রিয়া হলো **DORA**।

```text
D → Discover
O → Offer
R → Request
A → Acknowledge
```

### ১. DHCP Discover

Client কাছাকাছি থাকা DHCP Server খুঁজে।

```text
Client → DHCP Server

"কোনো DHCP Server আছে কি?"
```

### ২. DHCP Offer

DHCP Server Client-কে একটি IP Configuration অফার করে।

```text
DHCP Server → Client

"তুমি 192.168.1.10 ব্যবহার করতে পারো।"
```

### ৩. DHCP Request

Client অফার করা Configuration গ্রহণ করার জন্য Request পাঠায়।

```text
Client → DHCP Server

"আমি এই IP Address ব্যবহার করতে চাই।"
```

### ৪. DHCP ACK

DHCP Server Configuration নিশ্চিত করে।

```text
DHCP Server → Client

"তোমার Configuration অনুমোদিত হয়েছে।"
```

### DORA Flow

```text
Client
  │
  │ DHCP Discover
  ↓
DHCP Server
  │
  │ DHCP Offer
  ↓
Client
  │
  │ DHCP Request
  ↓
DHCP Server
  │
  │ DHCP ACK
  ↓
Client Configured
```

---

# ৬. DHCP Configuration

একটি DHCP Server সাধারণত Available IP Address-এর একটি **Pool** সংরক্ষণ করে।

উদাহরণ:

```text
Network: 192.168.1.0/24

DHCP Pool:
192.168.1.100
192.168.1.101
192.168.1.102
...
192.168.1.200
```

কোনো Client IP Address চাইলে DHCP Server Pool থেকে একটি Available IP দিতে পারে।

উদাহরণ:

```text
PC1 → 192.168.1.100
PC2 → 192.168.1.101
PC3 → 192.168.1.102
```

---

## DHCP Reservation

DHCP Server কোনো নির্দিষ্ট Device-এর জন্য নির্দিষ্ট IP Address Reserve করে রাখতে পারে।

এটি সাধারণত Device-এর **MAC Address** ব্যবহার করে করা হয়।

উদাহরণ:

```text
MAC Address → 00:11:22:33:44:55

Reserved IP → 192.168.1.50
```

এর ফলে Device-টি DHCP থেকে Configuration চাইলে একই IP Address পাওয়ার সম্ভাবনা থাকে।

---

# ৭. DHCP Important Ports

DHCP **UDP** ব্যবহার করে।

| Protocol | Port | কাজ         |
| -------- | ---: | ----------- |
| UDP      |   67 | DHCP Server |
| UDP      |   68 | DHCP Client |

```text
Client → UDP 68
Server → UDP 67
```

---

# ৮. ARP

## ARP কী?

**ARP (Address Resolution Protocol)** IPv4 Network-এ কোনো **IPv4 Address-এর সাথে সম্পর্কিত MAC Address খুঁজে বের করতে** ব্যবহৃত হয়।

সহজভাবে:

```text
IP Address → MAC Address
```

উদাহরণ:

```text
IP: 192.168.1.20

ARP জিজ্ঞাসা করে:

"192.168.1.20 কার?"
```

যে Device-এর এই IP আছে, সে তার MAC Address দিয়ে Reply করে।

---

# ৯. How ARP Works

ধরা যাক PC1, PC2-এর সাথে যোগাযোগ করতে চায়।

```text
PC1
IP: 192.168.1.10
MAC: AA:AA:AA:AA:AA:AA

PC2
IP: 192.168.1.20
MAC: BB:BB:BB:BB:BB:BB
```

PC1, PC2-এর IP Address জানে কিন্তু MAC Address জানে না।

### ধাপ ১ — ARP Request

PC1 একটি ARP Request পাঠায়:

```text
Who has 192.168.1.20?
```

এই Request Local Network-এ Broadcast হয়।

### ধাপ ২ — ARP Reply

PC2 Reply করে:

```text
192.168.1.20 is at BB:BB:BB:BB:BB:BB
```

### ধাপ ৩ — Communication

এখন PC1, PC2-এর MAC Address ব্যবহার করে Ethernet Frame পাঠাতে পারে।

```text
IP Address
    ↓
ARP
    ↓
MAC Address
    ↓
Ethernet Frame
```

---

# ১০. ARP Cache

Device-গুলো সম্প্রতি শেখা **IP-to-MAC Mapping** একটি ARP Cache/Table-এ সংরক্ষণ করে।

উদাহরণ:

```text
IP Address       MAC Address
192.168.1.1      AA:BB:CC:DD:EE:01
192.168.1.20     AA:BB:CC:DD:EE:02
192.168.1.30     AA:BB:CC:DD:EE:03
```

এর ফলে পরিচিত Device-এর সাথে প্রতিবার নতুন করে ARP Request পাঠানোর প্রয়োজন হয় না।

ARP Entry নির্দিষ্ট সময় পরে Expire হতে পারে এবং আবার নতুন করে শেখা হতে পারে।

---

# ১১. ARP Commands

## Windows

ARP Cache দেখতে:

```cmd
arp -a
```

একটি ARP Entry Delete করতে:

```cmd
arp -d <IP>
```

উদাহরণ:

```cmd
arp -d 192.168.1.20
```

---

## Linux

ARP/Neighbour Information দেখতে:

```bash
ip neigh
```

উদাহরণ:

```text
192.168.1.1 dev eth0 lladdr aa:bb:cc:dd:ee:ff REACHABLE
```

---

# ১২. ARP Security Risks

ARP-এ ARP Message-এর জন্য শক্তিশালী Authentication ব্যবস্থা নেই।

একটি গুরুত্বপূর্ণ আক্রমণ হলো:

## ARP Spoofing / ARP Poisoning

একজন Attacker ভুয়া ARP Information পাঠাতে পারে।

উদাহরণ:

```text
Real Router:
192.168.1.1 → AA:AA:AA:AA:AA:AA

Attacker:
192.168.1.1 → XX:XX:XX:XX:XX:XX
```

এর ফলে Victim ভুলভাবে Router-এর IP Address-কে Attacker-এর MAC Address-এর সাথে যুক্ত করতে পারে।

এটি সম্ভাব্যভাবে ব্যবহার করা যেতে পারে:

* Man-in-the-Middle Attack
* Traffic Interception
* Traffic Redirection
* Network Disruption

### প্রতিরোধের কিছু ব্যবস্থা

Network-এ ব্যবহার করা যেতে পারে:

* Dynamic ARP Inspection (DAI)
* DHCP Snooping
* প্রয়োজন অনুযায়ী Static ARP Entry
* Network Segmentation
* অস্বাভাবিক ARP Activity Monitoring

---

# ১৩. DHCP vs ARP

| বিষয়          | DHCP                                | ARP                            |
| -------------- | ----------------------------------- | ------------------------------ |
| পূর্ণ নাম      | Dynamic Host Configuration Protocol | Address Resolution Protocol    |
| প্রধান কাজ     | Network Configuration প্রদান করা    | IPv4 থেকে MAC খুঁজে বের করা    |
| কাজ করে        | IP Configuration নিয়ে              | IPv4 + MAC নিয়ে               |
| Transport      | UDP                                 | ARP Protocol                   |
| Port           | UDP 67/68                           | TCP/UDP Port নেই               |
| প্রধান ব্যবহার | IP Configuration পাওয়া             | Local MAC Address খুঁজে পাওয়া |

### সহজ পার্থক্য

```text
DHCP:

"আমি কোন IP Configuration ব্যবহার করব?"
```

```text
ARP:

"এই IP Address-এর MAC Address কোনটি?"
```

---

# ১৪. Network Communication Flow

একটি সাধারণ LAN Communication-এর ক্ষেত্রে ধাপগুলো এমন হতে পারে:

```text
1. Device LAN-এ Connect করে
          ↓
2. DHCP IP Configuration প্রদান করে
          ↓
3. Device নিজের IP, Subnet Mask এবং Gateway জানতে পারে
          ↓
4. Device অন্য Local Device-এর সাথে যোগাযোগ করতে চায়
          ↓
5. ARP Destination MAC Address খুঁজে বের করে
          ↓
6. Ethernet Frame তৈরি হয়
          ↓
7. Switch Frame Forward করে
          ↓
8. Destination Device Frame গ্রহণ করে
```

উদাহরণ:

```text
          DHCP
           ↓
     IP Configuration
           ↓
Client ── ARP ──→ MAC খুঁজে বের করে
           ↓
       Ethernet Frame
           ↓
         Switch
           ↓
       Destination
```

---

# ১৫. Quick Reference

## LAN

```text
LAN = Local Area Network
```

সাধারণ Components:

```text
PC
Switch
Router
Access Point
Server
```

---

## DHCP

```text
DHCP = Dynamic Host Configuration Protocol
```

### DORA

```text
Discover
Offer
Request
ACK
```

### Ports

```text
UDP 67 → Server
UDP 68 → Client
```

---

## ARP

```text
ARP = Address Resolution Protocol
```

### প্রধান কাজ

```text
IPv4 Address → MAC Address
```

### Windows

```cmd
arp -a
```

### Linux

```bash
ip neigh
```

### Security Risks

```text
ARP Spoofing
ARP Poisoning
```

---

# Final Mental Model

```text
                 LAN
                  │
       ┌──────────┴──────────┐
       │                     │
      DHCP                  ARP
       │                     │
       ↓                     ↓
Network Configuration    MAC Address খুঁজে বের করা
       │                     │
       └──────────┬──────────┘
                  ↓
             Communication
                  ↓
                Switch
                  ↓
             Destination
```

### মনে রাখবে

> **DHCP Device-কে Network Configuration দেয়।**

> **ARP Local IPv4 Address-এর জন্য MAC Address খুঁজে বের করতে সাহায্য করে।**

> **LAN একটি Local Network-এর মধ্যে বিভিন্ন Device-কে সংযুক্ত করে।**
