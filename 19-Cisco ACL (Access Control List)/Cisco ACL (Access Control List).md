# Cisco ACL (Access Control List) – Complete Guide

## 1. ACL কী?

**ACL (Access Control List)** হলো Cisco Router ও Switch-এর একটি Security Feature, যার মাধ্যমে Network Traffic নিয়ন্ত্রণ করা যায়।

ACL ব্যবহার করে নির্ধারণ করা যায়:
- কোন IP Network-এ প্রবেশ করতে পারবে।
- কোন IP Network-এ প্রবেশ করতে পারবে না।
- কোন Protocol (TCP, UDP, ICMP) Allow বা Deny হবে।
- কোন Port ব্যবহার করা যাবে।
- নির্দিষ্ট Host-এর Communication বন্ধ করা যাবে।

**ACL-এর মূল কাজ:** Traffic Filter করা (Permit / Deny)।

---

## 2. ACL কত প্রকার?

Cisco ACL প্রধানত 4 ধরনের:

| ACL Type | কাজ |
|---|---|
| Standard ACL | শুধু Source IP দিয়ে Traffic Filter করে |
| Extended ACL | Source, Destination, Protocol ও Port দিয়ে Filter করে |
| Named ACL | Number-এর পরিবর্তে Name দিয়ে ACL তৈরি করা |
| Dynamic ACL | Authentication-এর মাধ্যমে সাময়িক Access দেওয়া |

এছাড়াও Time-Based ACL, Reflexive ACL এবং IPv6 ACL রয়েছে।

### 2.1 Standard ACL

শুধুমাত্র Source IP Address পরীক্ষা করে।

**ACL Number:**
- 1–99
- 1300–1999

**Configuration:**

```cisco
Router(config)# access-list 10 permit 192.168.10.0 0.0.0.255
Router(config)# access-list 10 deny any
```

**Interface-এ Apply:**

```cisco
Router(config)# interface g0/0
Router(config-if)# ip access-group 10 in
```

এখানে:
- `10` = ACL Number
- `permit` = Traffic Allow
- `deny` = Traffic Block
- `in` = Interface-এ প্রবেশের সময় Filter

### 2.2 Extended ACL

Source IP, Destination IP, Protocol এবং Port Number অনুযায়ী Traffic Filter করে।

**ACL Number:**
- 100–199
- 2000–2699

**Configuration:**

```cisco
Router(config)# access-list 110 permit tcp 192.168.10.0 0.0.0.255 any eq 80
Router(config)# access-list 110 permit tcp 192.168.10.0 0.0.0.255 any eq 443
Router(config)# access-list 110 deny ip any any
```

এখানে:
- TCP Port 80 = HTTP
- TCP Port 443 = HTTPS
- `any` = যেকোনো IP
- `eq` = Equal / নির্দিষ্ট Port

**Interface-এ Apply:**

```cisco
Router(config)# interface g0/0
Router(config-if)# ip access-group 110 in
```

### 2.3 Named ACL

Number-এর পরিবর্তে নিজের পছন্দমতো Name দিয়ে ACL তৈরি করা যায়।

**Standard Named ACL:**

```cisco
Router(config)# ip access-list standard BLOCK_PC
Router(config-std-nacl)# deny host 192.168.10.10
Router(config-std-nacl)# permit any
```

**Extended Named ACL:**

```cisco
Router(config)# ip access-list extended BLOCK_WEB
Router(config-ext-nacl)# deny tcp any any eq 80
Router(config-ext-nacl)# permit ip any any
```

Apply:

```cisco
Router(config)# interface g0/0
Router(config-if)# ip access-group BLOCK_WEB in
```

### 2.4 Dynamic ACL

Dynamic ACL-কে Lock-and-Key ACL-ও বলা হয়।

এখানে User Authentication-এর মাধ্যমে নির্দিষ্ট Network Access দেওয়া হয়।

সাধারণত:
- প্রথমে User-এর Traffic Block থাকে।
- User Authentication করে।
- Authentication সফল হলে সাময়িক Access দেওয়া হয়।

এটি সাধারণ ACL-এর তুলনায় Advanced Configuration।

---

## 3. Wildcard Mask

Wildcard Mask দিয়ে নির্ধারণ করা হয় কোন IP Address-এর কোন অংশ Match করতে হবে।

| Subnet Mask | Wildcard Mask |
|---|---|
| 255.255.255.0 | 0.0.0.255 |
| 255.255.0.0 | 0.0.255.255 |
| 255.0.0.0 | 0.255.255.255 |
| 255.255.255.255 | 0.0.0.0 |
| 0.0.0.0 | 255.255.255.255 |

**মনে রাখার নিয়ম:**

Wildcard Mask = 255.255.255.255 − Subnet Mask

উদাহরণ:

```cisco
access-list 10 permit 192.168.10.0 0.0.0.255
```

এটি 192.168.10.0/24 Network-এর সব Host Match করবে।

Special Keywords:

```cisco
host 192.168.10.10
```
শুধু একটি নির্দিষ্ট IP Match করবে।

```cisco
any
```
সব IP Address Match করবে।

---

## 4. ACL দিয়ে কী কী করা যায়?

### 4.1 নির্দিষ্ট IP Block করা

```cisco
access-list 10 deny host 192.168.10.10
access-list 10 permit any
```

### 4.2 নির্দিষ্ট Network Allow করা

```cisco
access-list 10 permit 192.168.10.0 0.0.0.255
```

### 4.3 নির্দিষ্ট Website-এর HTTP Traffic Block করা

```cisco
access-list 110 deny tcp any host 192.168.20.10 eq 80
access-list 110 permit ip any any
```

### 4.4 HTTPS Block করা

```cisco
access-list 110 deny tcp any any eq 443
access-list 110 permit ip any any
```

### 4.5 ICMP / Ping Block করা

```cisco
access-list 110 deny icmp any any
access-list 110 permit ip any any
```

### 4.6 FTP Block করা

```cisco
access-list 110 deny tcp any any eq 21
access-list 110 permit ip any any
```

### 4.7 Telnet Block করা

```cisco
access-list 110 deny tcp any any eq 23
access-list 110 permit ip any any
```

### 4.8 নির্দিষ্ট PC থেকে নির্দিষ্ট Server Access Block

```cisco
access-list 110 deny ip host 192.168.10.10 host 192.168.20.10
access-list 110 permit ip any any
```

### 4.9 SSH Allow করা

```cisco
access-list 110 permit tcp any any eq 22
```

**মনে রাখবে:** উপরের প্রতিটি ACL আলাদা উদাহরণ। একই ACL-এ একাধিক Rule ব্যবহার করলে Rule-এর ক্রম গুরুত্বপূর্ণ।

---

## 5. ACL Interface Direction

ACL দুইভাবে Interface-এ Apply করা যায়।

### IN (Inbound)

Router Interface দিয়ে Packet প্রবেশ করার সময় ACL পরীক্ষা করে।

```cisco
interface g0/0
ip access-group 10 in
```

### OUT (Outbound)

Router Interface দিয়ে Packet বের হওয়ার সময় ACL পরীক্ষা করে।

```cisco
interface g0/1
ip access-group 10 out
```

**মনে রাখার কৌশল:**
- IN = Router-এর দিকে আসছে।
- OUT = Router থেকে বের হচ্ছে।

---

## 6. ACL-এর গুরুত্বপূর্ণ Rules

1. ACL Rules Top-to-Bottom পরীক্ষা করা হয়।
2. প্রথম যে Rule Match করবে, সেটিই কার্যকর হবে।
3. প্রতিটি ACL-এর শেষে Implicit `deny any` থাকে।
4. তাই প্রয়োজন অনুযায়ী শেষে `permit ip any any` দিতে হয়।
5. Standard ACL সাধারণত Destination-এর কাছাকাছি Apply করা হয়।
6. Extended ACL সাধারণত Source-এর কাছাকাছি Apply করা হয়।
7. একটি Interface-এ প্রতি Protocol ও Direction-এর জন্য একটি ACL Apply করা যায়।
8. ACL-এ Rule-এর Order খুব গুরুত্বপূর্ণ।

---

## 7. ACL Verification Commands

```cisco
show access-lists
```
সব ACL দেখাবে।

```cisco
show access-lists 10
```
নির্দিষ্ট ACL দেখাবে।

```cisco
show ip interface g0/0
```
Interface-এ ACL Apply আছে কি না দেখাবে।

```cisco
show running-config
```
বর্তমান Configuration দেখাবে।

```cisco
show ip access-lists
```
IP ACL-এর বিস্তারিত দেখাবে।

---

## 8. ACL Modify ও Delete Commands

### ACL থেকে নির্দিষ্ট Rule Remove

Named ACL:

```cisco
ip access-list extended BLOCK_WEB
no 10
```

### সম্পূর্ণ ACL Delete

```cisco
no access-list 10
```

Named ACL:

```cisco
no ip access-list extended BLOCK_WEB
```

### Interface থেকে ACL Remove

```cisco
interface g0/0
no ip access-group 10 in
```

---

## 9. Time-Based ACL

নির্দিষ্ট সময় অনুযায়ী Traffic Allow বা Deny করা যায়।

উদাহরণ:

```cisco
Router(config)# time-range OFFICE
Router(config-time-range)# periodic weekdays 8:00 to 17:00

Router(config)# ip access-list extended TIME_ACL
Router(config-ext-nacl)# permit tcp any any eq 80 time-range OFFICE
```

এখানে Weekdays সকাল ৮টা থেকে বিকেল ৫টা পর্যন্ত HTTP Traffic Allow হবে।

---

## 10. IPv6 ACL

IPv6 Network-এর Traffic Filter করতে IPv6 ACL ব্যবহার করা হয়।

```cisco
Router(config)# ipv6 access-list IPV6_FILTER
Router(config-ipv6-acl)# deny ipv6 host 2001:DB8:10::10 any
Router(config-ipv6-acl)# permit ipv6 any any
```

Apply:

```cisco
Router(config)# interface g0/0
Router(config-if)# ipv6 traffic-filter IPV6_FILTER in
```

---

## 11. ACL Important Protocol & Port

| Protocol | Port | কাজ |
|---|---:|---|
| FTP | 20, 21 | File Transfer |
| SSH | 22 | Secure Remote Access |
| Telnet | 23 | Remote Access |
| SMTP | 25 | Email Sending |
| DNS | 53 | Domain Resolution |
| DHCP | 67, 68 | IP Assignment |
| HTTP | 80 | Web Traffic |
| POP3 | 110 | Email Receiving |
| NTP | 123 | Time Synchronization |
| HTTPS | 443 | Secure Web Traffic |
| SMB | 445 | File Sharing |
| RDP | 3389 | Remote Desktop |

---

## 12. ACL Configuration Practice Lab

**Topology:**

- Router R1
- LAN 1: 192.168.10.0/24
- LAN 2: 192.168.20.0/24
- PC1: 192.168.10.10
- Server: 192.168.20.10

**Task:**

PC1 যেন Server-কে Ping করতে না পারে, কিন্তু অন্য Network Communication চালু থাকে।

Configuration:

```cisco
Router(config)# ip access-list extended BLOCK_PING
Router(config-ext-nacl)# deny icmp host 192.168.10.10 host 192.168.20.10
Router(config-ext-nacl)# permit ip any any

Router(config)# interface g0/0
Router(config-if)# ip access-group BLOCK_PING in
```

**Verification:**

```cisco
show access-lists
ping 192.168.20.10
```

PC1 থেকে Server-এ Ping Block হবে, তবে অন্যান্য IP Traffic Allow থাকবে।

---

## 13. Quick Revision

| বিষয় | মনে রাখবে |
|---|---|
| Standard ACL | Source IP |
| Extended ACL | Source + Destination + Protocol + Port |
| Named ACL | নিজের দেওয়া Name |
| Dynamic ACL | Authentication-based Access |
| IN | Incoming Traffic |
| OUT | Outgoing Traffic |
| Permit | Allow |
| Deny | Block |
| Wildcard | IP Matching |
| Implicit Deny | Default Block |
| Time-Based ACL | নির্দিষ্ট সময়ের Filter |
| IPv6 ACL | IPv6 Traffic Filter |

**Final Note:** ACL মূলত Network Security, Traffic Filtering, Access Control এবং Unauthorized Communication বন্ধ করার জন্য ব্যবহৃত হয়।