# IP Addressing & Subnetting — Complete Networking Notes

> **Purpose:** এই নোটটি Networking ও Cybersecurity শেখার জন্য IPv4, IPv6, CIDR, Subnetting, VLSM, Private/Public IP, NAT, DHCP, DNS এবং গুরুত্বপূর্ণ IP addressing concepts এক জায়গায় রাখার জন্য তৈরি করা হয়েছে।

---

## Table of Contents

1. [Bit, Byte, and Octet Fundamentals](#1-bit-byte-and-octet-fundamentals)
2. [Decimal to Binary & Binary to Decimal Conversion](#2-decimal-to-binary--binary-to-decimal-conversion)
3. [IPv4 Addressing Deep Dive](#3-ipv4-addressing-deep-dive)
4. [IPv4 Address Structure](#4-ipv4-address-structure)
5. [Classful IP Addressing](#5-classful-ip-addressing)
6. [Private, Public, Loopback & Special IPv4 Addresses](#6-private-public-loopback--special-ipv4-addresses)
7. [Subnet Mask](#7-subnet-mask)
8. [Classless Addressing & CIDR](#8-classless-addressing--cidr)
9. [Network Address, Host Address & Broadcast Address](#9-network-address-host-address--broadcast-address)
10. [Subnetting Step-by-Step](#10-subnetting-step-by-step)
11. [Subnetting Formulas](#11-subnetting-formulas)
12. [VLSM — Variable Length Subnet Mask](#12-vlsm--variable-length-subnet-mask)
13. [Supernetting & Route Aggregation](#13-supernetting--route-aggregation)
14. [Default Gateway](#14-default-gateway)
15. [DHCP and IP Address Assignment](#15-dhcp-and-ip-address-assignment)
16. [NAT and IP Address Translation](#16-nat-and-ip-address-translation)
17. [IPv6 Addressing Overview](#17-ipv6-addressing-overview)
18. [IPv6 Address Types](#18-ipv6-address-types)
19. [IPv6 Compression Rules](#19-ipv6-compression-rules)
20. [IPv4 vs IPv6 Comparison](#20-ipv4-vs-ipv6-comparison)
21. [IP Addressing & Cybersecurity](#21-ip-addressing--cybersecurity)
22. [Quick Reference Tables](#22-quick-reference-tables)
23. [Practice Questions](#23-practice-questions)

---

# 1. Bit, Byte, and Octet Fundamentals

## Bit

**Bit** = Binary Digit.

A bit can have only two values:

```text
0 or 1
```

## Byte

```text
1 Byte = 8 bits
```

Example:

```text
10101100
```

এটি 8-bit বা 1 Byte।

## Octet

Networking-এ 8-bit group-কে সাধারণত **Octet** বলা হয়।

IPv4 address-এ মোট 4টি octet থাকে।

Example:

```text
192.168.1.10
```

এখানে:

```text
192 = Octet 1
168 = Octet 2
1   = Octet 3
10  = Octet 4
```

IPv4 মোট:

```text
4 × 8 bits = 32 bits
```

---

# 2. Decimal to Binary & Binary to Decimal Conversion

## Binary Place Values

8-bit binary-এর place values:

```text
128  64  32  16  8  4  2  1
```

Example:

```text
11000000
```

Calculation:

```text
128 + 64 = 192
```

Therefore:

```text
11000000 = 192
```

## Decimal to Binary

Example:

```text
168
```

Place values ব্যবহার করে:

```text
128 + 32 + 8 = 168
```

তাই:

```text
168 = 10101000
```

## Binary to Decimal

Example:

```text
10101010
```

Calculation:

```text
128 + 32 + 8 + 2
= 170
```

Therefore:

```text
10101010 = 170
```

> **Practice:** 192, 168, 10, 255, 172, 16, 31 এবং 224-কে binary-তে convert করার অভ্যাস করুন।

---

# 3. IPv4 Addressing Deep Dive

## What is an IP Address?

IP Address হলো network-এ একটি device/interface শনাক্ত করার জন্য ব্যবহৃত logical address।

Example:

```text
192.168.1.10
```

IPv4 address:

```text
32 bits
```

এবং 4টি octet:

```text
192 . 168 . 1 . 10
```

প্রতিটি octet-এর range:

```text
0 – 255
```

কারণ:

```text
2^8 = 256
```

অর্থাৎ 0 থেকে 255 পর্যন্ত মোট 256টি value।

---

# 4. IPv4 Address Structure

একটি IPv4 address সাধারণত দুইটি logical অংশে দেখা হয়:

```text
Network Portion + Host Portion
```

Example:

```text
192.168.1.10/24
```

এখানে `/24` বলে দেয় প্রথম 24 bits network portion।

```text
Network bits = 24
Host bits    = 8
```

Binary:

```text
11000000.10101000.00000001.00001010
```

/24 mask:

```text
11111111.11111111.11111111.00000000
```

---

# 5. Classful IP Addressing

CIDR-এর আগে IPv4 address-কে Class A, B, C, D এবং E হিসেবে ভাগ করা হতো।

| Class | First Octet | Default Mask | সাধারণ ব্যবহার |
|---|---:|---|---|
| A | 1–126 | /8 | বড় network |
| B | 128–191 | /16 | মাঝারি network |
| C | 192–223 | /24 | ছোট network |
| D | 224–239 | N/A | Multicast |
| E | 240–255 | N/A | Experimental/Reserved |

> `127.x.x.x` সাধারণ IPv4 class range-এর মধ্যে host network হিসেবে ব্যবহার করা হয় না; এটি loopback-এর জন্য reserved।

## Class A

Default:

```text
/8
255.0.0.0
```

## Class B

Default:

```text
/16
255.255.0.0
```

## Class C

Default:

```text
/24
255.255.255.0
```

### Why Classful Addressing Is Limited

একটি organization-এর 500টি host দরকার হলে Class C-এর 254 usable host যথেষ্ট নয়, আবার Class B-এর বিশাল address space প্রয়োজনের তুলনায় অনেক বেশি।

এই inefficiency কমানোর জন্য **CIDR** ব্যবহৃত হয়।

---

# 6. Private, Public, Loopback & Special IPv4 Addresses

## Private IPv4 Ranges

Private IP সাধারণত internal/local network-এ ব্যবহৃত হয়।

### 10.0.0.0/8

```text
10.0.0.0 – 10.255.255.255
```

### 172.16.0.0/12

```text
172.16.0.0 – 172.31.255.255
```

### 192.168.0.0/16

```text
192.168.0.0 – 192.168.255.255
```

Example:

```text
192.168.1.10
10.0.0.25
172.16.5.20
```

## Loopback

```text
127.0.0.0/8
```

সবচেয়ে পরিচিত:

```text
127.0.0.1
```

এটি নিজের device-এর network stack test করতে ব্যবহৃত হয়।

## Link-Local / APIPA

```text
169.254.0.0/16
```

কোনো device DHCP থেকে IPv4 address না পেলে operating system এই range থেকে address configure করতে পারে।

## Unspecified Address

```text
0.0.0.0
```

Context অনুযায়ী "unspecified" বা "any address" বোঝাতে ব্যবহৃত হয়।

## Limited Broadcast

```text
255.255.255.255
```

Local network segment-এ broadcast-এর জন্য ব্যবহৃত হয়।

---

# 7. Subnet Mask

Subnet mask বলে দেয় IPv4 address-এর কোন অংশ network এবং কোন অংশ host।

Example:

```text
IP:
192.168.1.10

Mask:
255.255.255.0
```

Binary:

```text
IP:
11000000.10101000.00000001.00001010

Mask:
11111111.11111111.11111111.00000000
```

Mask-এর:

```text
1 = Network portion
0 = Host portion
```

---

# 8. Classless Addressing & CIDR

## CIDR

CIDR = **Classless Inter-Domain Routing**

CIDR notation:

```text
IP Address / Prefix Length
```

Example:

```text
192.168.1.10/24
```

এখানে:

```text
/24 = প্রথম 24 bits network portion
```

## Common CIDR Values

| CIDR | Subnet Mask | Total Addresses | Usable Hosts* |
|---|---|---:|---:|
| /8 | 255.0.0.0 | 16,777,216 | 16,777,214 |
| /16 | 255.255.0.0 | 65,536 | 65,534 |
| /24 | 255.255.255.0 | 256 | 254 |
| /25 | 255.255.255.128 | 128 | 126 |
| /26 | 255.255.255.192 | 64 | 62 |
| /27 | 255.255.255.224 | 32 | 30 |
| /28 | 255.255.255.240 | 16 | 14 |
| /29 | 255.255.255.248 | 8 | 6 |
| /30 | 255.255.255.252 | 4 | 2 |

\*Traditional IPv4 subnet calculation-এ network ও directed broadcast address বাদ দিলে usable host count দেখানো হয়েছে। বিশেষ-purpose এবং point-to-point designs-এ ব্যতিক্রম থাকতে পারে।

---

# 9. Network Address, Host Address & Broadcast Address

ধরা যাক:

```text
192.168.1.10/24
```

Subnet mask:

```text
255.255.255.0
```

Network address:

```text
192.168.1.0
```

Usable host range:

```text
192.168.1.1 – 192.168.1.254
```

Broadcast:

```text
192.168.1.255
```

### Important

```text
Network Address → পুরো subnet শনাক্ত করে
Host Address    → subnet-এর device/interface
Broadcast       → subnet-এর সব host-কে পাঠাতে ব্যবহৃত হয়
```

---

# 10. Subnetting Step-by-Step

Subnetting মানে একটি বড় network-কে ছোট ছোট network-এ ভাগ করা।

Example:

```text
192.168.1.0/24
```

আমরা এটিকে `/26` করতে চাই।

## Step 1 — Original Prefix

```text
/24
```

## Step 2 — New Prefix

```text
/26
```

Borrowed bits:

```text
26 - 24 = 2 bits
```

## Step 3 — Number of Subnets

```text
2^2 = 4 subnets
```

## Step 4 — Host Bits

IPv4-এ মোট 32 bits।

```text
32 - 26 = 6 host bits
```

## Step 5 — Addresses Per Subnet

```text
2^6 = 64 addresses
```

Traditional usable hosts:

```text
64 - 2 = 62
```

## Result

```text
Subnet 1:
192.168.1.0/26
Hosts: 192.168.1.1 – 192.168.1.62
Broadcast: 192.168.1.63

Subnet 2:
192.168.1.64/26
Hosts: 192.168.1.65 – 192.168.1.126
Broadcast: 192.168.1.127

Subnet 3:
192.168.1.128/26
Hosts: 192.168.1.129 – 192.168.1.190
Broadcast: 192.168.1.191

Subnet 4:
192.168.1.192/26
Hosts: 192.168.1.193 – 192.168.1.254
Broadcast: 192.168.1.255
```

---

# 11. Subnetting Formulas

## Number of Subnets

যদি `n` bits borrow করা হয়:

```text
Number of subnets = 2^n
```

## Number of Host Addresses

যদি `h` host bits থাকে:

```text
Total addresses = 2^h
```

Traditional IPv4 subnet-এর ক্ষেত্রে:

```text
Usable hosts = 2^h - 2
```

কারণ সাধারণভাবে:

```text
1 address = Network
1 address = Broadcast
```

## Host Bits বের করা

```text
Host bits = 32 - Prefix Length
```

Example:

```text
/27

Host bits = 32 - 27
          = 5
```

Total:

```text
2^5 = 32 addresses
```

Traditional usable:

```text
30 hosts
```

---

# 12. VLSM — Variable Length Subnet Mask

VLSM = **Variable Length Subnet Mask**

VLSM-এর মাধ্যমে একই বড় network-এর ভিতরে প্রয়োজন অনুযায়ী বিভিন্ন size-এর subnet তৈরি করা যায়।

Example requirement:

```text
Department A → 100 hosts
Department B → 50 hosts
Department C → 20 hosts
Department D → 10 hosts
```

সব department-কে একই size subnet দিলে address waste হতে পারে।

VLSM ব্যবহার করলে:

```text
100 hosts → /25
50 hosts  → /26
20 hosts  → /27
10 hosts  → /28
```

> VLSM design করার সময় বড় host requirement থেকে ছোট requirement-এর দিকে subnet allocate করা সুবিধাজনক।

---

# 13. Supernetting & Route Aggregation

Supernetting হলো একাধিক ছোট contiguous network-কে একটি বড় prefix দিয়ে represent করার ধারণা।

Example:

```text
192.168.0.0/24
192.168.1.0/24
192.168.2.0/24
192.168.3.0/24
```

এগুলো appropriate alignment থাকলে aggregate করা যায়:

```text
192.168.0.0/22
```

এতে routing table-এর entry কমানো সম্ভব।

এটিকে:

```text
Route Aggregation
```

বা

```text
Route Summarization
```

বলা হয়।

---

# 14. Default Gateway

একটি host যখন নিজের local subnet-এর বাইরে কোনো destination-এ packet পাঠায়, তখন সাধারণত packetটি **default gateway**-এর দিকে পাঠানো হয়।

Example:

```text
PC:
192.168.1.10/24

Gateway:
192.168.1.1
```

যদি PC `192.168.1.50`-এ পাঠায়:

```text
Same subnet → Direct communication
```

যদি destination হয়:

```text
8.8.8.8
```

তাহলে এটি local subnet-এর বাইরে, তাই:

```text
PC → Default Gateway → Router/Next Hop → Destination
```

---

# 15. DHCP and IP Address Assignment

DHCP = **Dynamic Host Configuration Protocol**

DHCP client-কে automatically network configuration দিতে পারে।

সাধারণত:

```text
IP Address
Subnet Mask
Default Gateway
DNS Server
```

## DHCP DORA Process

```text
D = Discover
O = Offer
R = Request
A = Acknowledgment
```

Flow:

```text
Client
  ↓
DHCP Discover
  ↓
DHCP Offer
  ↓
DHCP Request
  ↓
DHCP ACK
```

DHCP সাধারণত UDP ব্যবহার করে:

```text
Server → UDP 67
Client → UDP 68
```

---

# 16. NAT and IP Address Translation

NAT = **Network Address Translation**

NAT private IP এবং public IP-এর মধ্যে address translation করতে পারে।

Example:

```text
Private:
192.168.1.10

Router/NAT

Public:
203.0.113.x
```

একটি LAN-এর অনেক private host একটি public IPv4 address share করতে পারে, বিশেষ করে PAT/NAPT ব্যবহারের মাধ্যমে।

## Common NAT Concepts

### Static NAT

একটি private address-এর জন্য নির্দিষ্ট public mapping।

### Dynamic NAT

একটি configured public address pool থেকে mapping।

### PAT / NAPT

Port number ব্যবহার করে multiple internal connections-কে public address-এর মাধ্যমে multiplex করে।

> NAT নিজে পূর্ণাঙ্গ firewall বা security control নয়। এটি address translation-এর কাজ করে; security policy আলাদাভাবে firewall/ACL ইত্যাদির মাধ্যমে করা হয়।

---

# 17. IPv6 Addressing Overview

IPv4:

```text
32 bits
```

IPv6:

```text
128 bits
```

IPv6 hexadecimal notation ব্যবহার করে।

Example:

```text
2001:db8:1234:0000:0000:8a2e:0370:7334
```

এখানে মোট 8টি hexadecimal group আছে।

প্রতিটি group:

```text
16 bits
```

তাই:

```text
8 × 16 = 128 bits
```

---

# 18. IPv6 Address Types

## Unicast

একটি interface-এর দিকে packet যায়।

## Multicast

একটি multicast group-এর একাধিক interface packet receive করতে পারে।

## Anycast

একই anycast address একাধিক interface-এ থাকতে পারে; routing সাধারণত topology অনুযায়ী একটি উপযুক্ত/নিকটবর্তী destination-এ packet পৌঁছে দেয়।

## IPv6 Link-Local

সাধারণত:

```text
FE80::/10
```

একটি IPv6-enabled interface local link-এ communication-এর জন্য link-local address ব্যবহার করতে পারে।

## Global Unicast

Global IPv6 communication-এর জন্য ব্যবহৃত address space।

---

# 19. IPv6 Compression Rules

IPv6 address বড় হওয়ায় shorthand ব্যবহার করা যায়।

Original:

```text
2001:0db8:0000:0000:0000:0000:0000:0001
```

Leading zeros বাদ দেওয়া যায়:

```text
2001:db8:0:0:0:0:0:1
```

Consecutive zero groups একবার `::` দিয়ে replace করা যায়:

```text
2001:db8::1
```

### Important Rule

একটি IPv6 address-এ `::` সর্বোচ্চ একবার ব্যবহার করা যায়।

---

# 20. IPv4 vs IPv6 Comparison

| Feature | IPv4 | IPv6 |
|---|---|---|
| Address size | 32-bit | 128-bit |
| Notation | Decimal | Hexadecimal |
| Example | 192.168.1.10 | 2001:db8::10 |
| Address space | Smaller | Very large |
| Broadcast | Supported | No broadcast |
| Multicast | Supported | Supported |
| Address configuration | Manual/DHCP | SLAAC/DHCPv6/Manual |
| Header | Variable 20–60 bytes | Fixed 40-byte base header |
| NAT | Commonly used | Generally not required for address conservation |
| Neighbor discovery | ARP | NDP/ICMPv6 |

---

# 21. IP Addressing & Cybersecurity

IP addressing cybersecurity-এর জন্য গুরুত্বপূর্ণ কারণ network traffic-এর source এবং destination বুঝতে IP information ব্যবহার করা হয়।

## 21.1 Source IP

Packet কোথা থেকে এসেছে তা বোঝাতে source IP ব্যবহৃত হয়।

## 21.2 Destination IP

Packet কোথায় যাবে তা বোঝাতে destination IP ব্যবহৃত হয়।

## 21.3 Private vs Public IP

```text
Private IP → Internal network
Public IP  → Internet-routable addressing context
```

## 21.4 Network Segmentation

একটি network-কে বিভিন্ন subnet/VLAN-এ ভাগ করলে different systems আলাদা logical segment-এ রাখা যায়।

Example:

```text
VLAN 10 → Employees
VLAN 20 → Servers
VLAN 30 → Guest
VLAN 40 → Management
```

Subnet/VLAN segmentation-এর সাথে firewall বা ACL policy ব্যবহার করে traffic control করা যায়।

## 21.5 ACL

ACL = **Access Control List**

Source/destination IP, protocol এবং port-এর ভিত্তিতে traffic permit বা deny করার rules তৈরি করা যায়।

Example concept:

```text
Allow:
10.10.10.0/24 → Web Server

Deny:
Guest Network → Internal Server
```

## 21.6 IP Spoofing

IP spoofing-এ attacker packet-এর source IP address forged করতে পারে।

তাই শুধু source IP-কে identity হিসেবে বিশ্বাস করা নিরাপদ নয়।

## 21.7 Network Scanning

Security assessment-এ authorized network scanning করে:

```text
Live hosts
Open ports
Services
Network ranges
```

সম্পর্কে তথ্য সংগ্রহ করা যায়।

> **Important:** Scanning বা testing সবসময় নিজের system, lab বা অনুমোদিত environment-এ করুন।

---

# 22. Quick Reference Tables

## IPv4 Special Ranges

| Range | Purpose |
|---|---|
| 10.0.0.0/8 | Private |
| 172.16.0.0/12 | Private |
| 192.168.0.0/16 | Private |
| 127.0.0.0/8 | Loopback |
| 169.254.0.0/16 | Link-local / APIPA |
| 0.0.0.0/0 | IPv4 default route / all IPv4 destinations in routing context |
| 255.255.255.255 | Limited broadcast |

## Common Subnet Masks

| CIDR | Mask | Addresses | Traditional Usable |
|---|---|---:|---:|
| /24 | 255.255.255.0 | 256 | 254 |
| /25 | 255.255.255.128 | 128 | 126 |
| /26 | 255.255.255.192 | 64 | 62 |
| /27 | 255.255.255.224 | 32 | 30 |
| /28 | 255.255.255.240 | 16 | 14 |
| /29 | 255.255.255.248 | 8 | 6 |
| /30 | 255.255.255.252 | 4 | 2 |

## Useful Powers of Two

| Power | Value |
|---|---:|
| 2^1 | 2 |
| 2^2 | 4 |
| 2^3 | 8 |
| 2^4 | 16 |
| 2^5 | 32 |
| 2^6 | 64 |
| 2^7 | 128 |
| 2^8 | 256 |
| 2^10 | 1024 |
| 2^16 | 65,536 |
| 2^24 | 16,777,216 |
| 2^32 | 4,294,967,296 |

---

# 23. Practice Questions

## Beginner

1. IPv4 address কত bits?
2. একটি octet-এ কত bits থাকে?
3. `192.168.1.10`-এ কয়টি octet আছে?
4. `/24` subnet mask কী?
5. `192.168.1.0/24`-এর network address কী?
6. `192.168.1.0/24`-এর broadcast address কী?
7. `127.0.0.1` কী?
8. Private IPv4-এর তিনটি প্রধান range কী?
9. Default gateway কী কাজে ব্যবহৃত হয়?
10. DHCP DORA-এর full form কী?

## Subnetting Practice

### Problem 1

```text
Network: 192.168.10.0/24
New Prefix: /26
```

Find:

```text
Number of subnets
Addresses per subnet
Usable hosts
Each network address
Each broadcast address
Host ranges
```

### Problem 2

```text
Network: 10.0.0.0/24
New Prefix: /27
```

Find:

```text
Number of subnets
Addresses per subnet
Usable hosts
All subnet ranges
```

### Problem 3

```text
IP: 172.16.20.75/28
```

Find:

```text
Network address
First usable host
Last usable host
Broadcast address
```

### Problem 4

A company needs:

```text
Department A → 100 hosts
Department B → 50 hosts
Department C → 20 hosts
Department D → 10 hosts
```

Design an appropriate VLSM plan.

---

# Final Mental Model

IP Addressing শেখার সময় এই order-টা follow করুন:

```text
Bit
 ↓
Byte / Octet
 ↓
Binary
 ↓
IPv4
 ↓
Network + Host
 ↓
Subnet Mask
 ↓
CIDR
 ↓
Network Address
 ↓
Broadcast Address
 ↓
Subnetting
 ↓
VLSM
 ↓
Routing / Default Gateway
 ↓
DHCP
 ↓
NAT
 ↓
IPv6
 ↓
Network Security
```

## Core Things You Should Be Able to Do

শেষে আপনার অন্তত এগুলো করতে পারা উচিত:

```text
✓ Decimal ↔ Binary conversion
✓ IPv4 structure explain করা
✓ CIDR বুঝতে পারা
✓ Subnet mask বের করা
✓ Network address বের করা
✓ Broadcast address বের করা
✓ Usable host range বের করা
✓ Subnet count calculate করা
✓ Host count calculate করা
✓ VLSM design করা
✓ Private/Public IP আলাদা করা
✓ Default gateway explain করা
✓ DHCP DORA explain করা
✓ NAT/PAT explain করা
✓ IPv6 address পড়া ও compress করা
✓ IP addressing-এর security impact বুঝতে পারা
```

---

## Recommended Learning Order

```text
1. Binary
2. IPv4
3. Subnet Mask
4. CIDR
5. Network/Broadcast
6. Subnetting
7. VLSM
8. Default Gateway
9. DHCP
10. NAT
11. IPv6
12. VLAN + Inter-VLAN Routing
13. ACL
14. Routing
15. Network Security
```

> **Note:** এই README-টি শেখার নোট হিসেবে তৈরি। Cisco/CCNA-level networking শেখার সময় subnetting, routing, VLAN, ACL, DHCP, NAT এবং IPv6-এর সাথে hands-on lab practice করলে concepts অনেক বেশি পরিষ্কার হবে।
