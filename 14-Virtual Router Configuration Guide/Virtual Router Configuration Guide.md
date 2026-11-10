# FHRP, HSRP, VRRP & GLBP – Virtual Router Configuration Guide

## 1. FHRP (First Hop Redundancy Protocol)

**FHRP কী?**

FHRP হলো এমন একটি Network Protocol Group, যা Default Gateway-এর High Availability নিশ্চিত করে।

সাধারণত একটি LAN-এর ডিভাইসগুলো Internet বা অন্য Network-এ যাওয়ার জন্য একটি Default Gateway ব্যবহার করে। সেই Router নষ্ট হলে পুরো Network-এর যোগাযোগ বন্ধ হয়ে যেতে পারে।

এই সমস্যা সমাধানে FHRP ব্যবহার করা হয়।

**কীভাবে কাজ করে?**

- একাধিক Physical Router একসঙ্গে কাজ করে।
- তারা একটি Virtual IP Address তৈরি করে।
- LAN-এর PC-গুলো Virtual IP-কে Default Gateway হিসেবে ব্যবহার করে।
- Active Router নষ্ট হলে অন্য Router দায়িত্ব গ্রহণ করে।

### FHRP-এর প্রধান ৩টি Protocol

| Protocol | Full Form | বিশেষত্ব |
|---|---|---|
| HSRP | Hot Standby Router Protocol | Active ও Standby Router |
| VRRP | Virtual Router Redundancy Protocol | Master ও Backup Router |
| GLBP | Gateway Load Balancing Protocol | Load Balancing ও Redundancy |

---

## 2. HSRP (Hot Standby Router Protocol)

HSRP হলো Cisco-এর তৈরি একটি FHRP Protocol। এটি একাধিক Router-এর মধ্যে একটি Active এবং অন্যটিকে Standby হিসেবে নির্বাচন করে।

**কাজের পদ্ধতি:**

- Active Router: মূল Traffic Forward করে।
- Standby Router: Active Router-এর ব্যর্থতার অপেক্ষায় থাকে।
- Virtual IP: সব Client-এর Default Gateway।
- Active Router Failure হলে Standby Router Active হয়ে যায়।

### HSRP Configuration (Cisco)

ধরি:

- Router 1: `192.168.10.2`
- Router 2: `192.168.10.3`
- Virtual IP: `192.168.10.1`

**Router 1 (Active):**

```bash
enable
configure terminal

interface gigabitEthernet 0/0
ip address 192.168.10.2 255.255.255.0
standby 1 ip 192.168.10.1
standby 1 priority 110
standby 1 preempt
no shutdown
```

**Router 2 (Standby):**

```bash
enable
configure terminal

interface gigabitEthernet 0/0
ip address 192.168.10.3 255.255.255.0
standby 1 ip 192.168.10.1
standby 1 priority 100
standby 1 preempt
no shutdown
```

**Verification Commands:**

```bash
show standby
show standby brief
```

---

## 3. VRRP (Virtual Router Redundancy Protocol)

VRRP হলো একটি Open Standard FHRP Protocol। এটি HSRP-এর মতো কাজ করে, তবে Cisco ছাড়াও বিভিন্ন Vendor-এর Router-এ ব্যবহার করা যায়।

**কাজের পদ্ধতি:**

- Master Router: Traffic Forward করে।
- Backup Router: Master Router-এর ব্যর্থতার অপেক্ষা করে।
- Virtual IP: Client-এর Default Gateway হিসেবে কাজ করে।
- Master Failure হলে Backup Router দায়িত্ব গ্রহণ করে।

### VRRP Configuration (Cisco)

**Router 1 (Master):**

```bash
enable
configure terminal

interface gigabitEthernet 0/0
ip address 192.168.10.2 255.255.255.0
vrrp 1 ip 192.168.10.1
vrrp 1 priority 110
vrrp 1 preempt
no shutdown
```

**Router 2 (Backup):**

```bash
enable
configure terminal

interface gigabitEthernet 0/0
ip address 192.168.10.3 255.255.255.0
vrrp 1 ip 192.168.10.1
vrrp 1 priority 100
vrrp 1 preempt
no shutdown
```

**Verification Commands:**

```bash
show vrrp
show vrrp brief
```

---

## 4. GLBP (Gateway Load Balancing Protocol)

GLBP হলো Cisco-এর একটি FHRP Protocol। এটি শুধু Gateway Redundancy নয়, একই সঙ্গে Load Balancing-ও প্রদান করে।

HSRP ও VRRP-তে সাধারণত একটি Router Traffic Forward করে। কিন্তু GLBP-তে একাধিক Router একই সঙ্গে Traffic Forward করতে পারে।

**কাজের পদ্ধতি:**

- AVG (Active Virtual Gateway): Virtual Gateway পরিচালনা করে।
- AVF (Active Virtual Forwarder): Client-এর Traffic Forward করে।
- একাধিক Router Traffic ভাগ করে নেয়।
- কোনো Router Failure হলে অন্য Router Traffic Forward করতে পারে।

### GLBP Configuration (Cisco)

**Router 1:**

```bash
enable
configure terminal

interface gigabitEthernet 0/0
ip address 192.168.10.2 255.255.255.0
glbp 1 ip 192.168.10.1
glbp 1 priority 110
glbp 1 preempt
no shutdown
```

**Router 2:**

```bash
enable
configure terminal

interface gigabitEthernet 0/0
ip address 192.168.10.3 255.255.255.0
glbp 1 ip 192.168.10.1
glbp 1 priority 100
glbp 1 preempt
no shutdown
```

**Verification Commands:**

```bash
show glbp
show glbp brief
```

---

## 5. HSRP vs VRRP vs GLBP

| Feature | HSRP | VRRP | GLBP |
|---|---|---|---|
| Type | Cisco Proprietary | Open Standard | Cisco Proprietary |
| Active Role | Active | Master | AVG / AVF |
| Backup Role | Standby | Backup | Other Forwarders |
| Load Balancing | No (Normally) | No (Normally) | Yes |
| Redundancy | Yes | Yes | Yes |

---

## 6. Important Commands

```bash
show standby brief
show vrrp brief
show glbp brief
show ip interface brief
show running-config
```

**Common Configuration Terms:**

- `ip`: Virtual IP Address নির্ধারণ করে।
- `priority`: Router-এর Election Priority নির্ধারণ করে।
- `preempt`: বেশি Priority-এর Router ফিরে এলে আবার Active/Master হওয়ার সুযোগ দেয়।
- `no shutdown`: Interface চালু করে।

**মনে রাখবে:**

FHRP = Gateway Redundancy

HSRP = Active + Standby

VRRP = Master + Backup

GLBP = Gateway Redundancy + Load Balancing

**Note:** Configuration Command-গুলো Cisco IOS সমর্থিত Router ও Interface অনুযায়ী ব্যবহার করতে হবে। Packet Tracer-এর সব Device বা IOS Image-এ সব Protocol সমর্থিত নাও হতে পারে।