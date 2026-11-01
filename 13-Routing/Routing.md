# Routing Protocols & Configuration Guide (Cisco)

এই নোটে IP Routing, IGP, EGP, ASN, Static Routing, Dynamic Routing, OSPF, EIGRP, RIP, IS-IS, BGP এবং গুরুত্বপূর্ণ Cisco IOS কমান্ড আলোচনা করা হয়েছে।

---

## ১. Routing কী?

**Routing** হলো Source Network থেকে Destination Network-এ Packet পাঠানোর জন্য সর্বোত্তম পথ নির্বাচন করার প্রক্রিয়া।

Router একটি **Routing Table** ব্যবহার করে নির্ধারণ করে Packet কোন পথে Forward হবে।

### Routing Table

```bash
show ip route
```

IPv4 Routing Table প্রদর্শন করে।

| Code | অর্থ |
|---|---|
| C | Connected Route |
| L | Local Route |
| S | Static Route |
| R | RIP |
| D | EIGRP |
| O | OSPF |
| B | BGP |
| i | IS-IS |

---

## ২. Routing-এর প্রকারভেদ

### Static Routing

Network Administrator ম্যানুয়ালি Route Configure করেন।

**ব্যবহার:** ছোট নেটওয়ার্ক, নির্দিষ্ট Route এবং Default Route।

```bash
ip route <network> <subnet-mask> <next-hop>
```

উদাহরণ:

```bash
R1(config)# ip route 192.168.20.0 255.255.255.0 10.0.0.2
```

`192.168.20.0/24` Network-এর Traffic `10.0.0.2` Next-Hop দিয়ে পাঠাবে।

### Default Routing

Routing Table-এ নির্দিষ্ট Destination-এর Route না থাকলে Default Route ব্যবহার করা হয়।

```bash
R1(config)# ip route 0.0.0.0 0.0.0.0 10.0.0.2
```

এটি Gateway of Last Resort Configure করে।

### Dynamic Routing

Router Routing Protocol ব্যবহার করে স্বয়ংক্রিয়ভাবে Route শেখে এবং আদান-প্রদান করে।

**ব্যবহার:** বড় নেটওয়ার্ক, স্বয়ংক্রিয় Route Update এবং Network পরিবর্তনের সঙ্গে মানিয়ে নেওয়া।

---

## ৩. IGP (Interior Gateway Protocol)

**IGP** একটি Autonomous System (AS)-এর অভ্যন্তরে Routing Information আদান-প্রদানের জন্য ব্যবহৃত হয়।

AS হলো একটি নেটওয়ার্ক বা নেটওয়ার্কসমূহের গ্রুপ, যা একটি সাধারণ Routing Policy-এর অধীনে পরিচালিত হয়।

### IGP-এর প্রকারভেদ

| Protocol | পূর্ণরূপ | প্রধান কাজ |
|---|---|---|
| RIP | Routing Information Protocol | Hop Count ব্যবহার করে Routing |
| OSPF | Open Shortest Path First | Link-State Routing |
| EIGRP | Enhanced Interior Gateway Routing Protocol | Advanced Distance-Vector Routing |
| IS-IS | Intermediate System to Intermediate System | Link-State Routing |

### RIP

RIP **Hop Count** ব্যবহার করে Best Route নির্বাচন করে।

- সর্বোচ্চ ব্যবহারযোগ্য Hop Count: 15
- 16 Hop মানে Unreachable
- ছোট নেটওয়ার্কের জন্য উপযোগী

#### Configuration

```bash
R1(config)# router rip
R1(config-router)# version 2
R1(config-router)# network 192.168.10.0
R1(config-router)# no auto-summary
```

#### Verification

```bash
show ip protocols
show ip route rip
```

---

## ৪. OSPF (Open Shortest Path First)

OSPF একটি **Link-State IGP**, যা SPF Algorithm ব্যবহার করে Best Route নির্ধারণ করে।

### প্রধান বৈশিষ্ট্য

- Cost Routing Metric হিসেবে ব্যবহার করে
- Area-ভিত্তিক Network Design সমর্থন করে
- Area 0 হলো Backbone Area
- অনেক ক্ষেত্রে RIP-এর তুলনায় দ্রুত Convergence হয়

### OSPF Configuration

```bash
R1(config)# router ospf 1
R1(config-router)# router-id 1.1.1.1
R1(config-router)# network 192.168.10.0 0.0.0.255 area 0
```

**ব্যাখ্যা:**

- `1` = OSPF Process ID
- `1.1.1.1` = Router ID
- `0.0.0.255` = Wildcard Mask
- `area 0` = Backbone Area

### OSPF Verification

```bash
show ip ospf neighbor
show ip ospf
show ip route ospf
```

---

## ৫. EIGRP (Enhanced Interior Gateway Routing Protocol)

EIGRP একটি Advanced Distance-Vector Routing Protocol, যা **DUAL Algorithm** ব্যবহার করে Loop-Free Route নির্ধারণ করে।

### প্রধান বৈশিষ্ট্য

- Default হিসেবে Bandwidth ও Delay ব্যবহার করে
- দ্রুত Convergence সমর্থন করে
- Unequal-Cost Load Balancing সমর্থন করে
- Classic Configuration-এ AS Number ব্যবহার করে

### EIGRP Configuration

```bash
R1(config)# router eigrp 100
R1(config-router)# network 192.168.10.0 0.0.0.255
R1(config-router)# no auto-summary
```

এখানে `100` হলো EIGRP Autonomous System Number।

### EIGRP Verification

```bash
show ip eigrp neighbors
show ip eigrp topology
show ip route eigrp
```

---

## ৬. IS-IS (Intermediate System to Intermediate System)

IS-IS একটি Link-State Routing Protocol, যা বড় Service Provider Network-এ ব্যবহৃত হয়।

### প্রধান বৈশিষ্ট্য

- SPF Algorithm ব্যবহার করে
- Hierarchical Routing সমর্থন করে
- Level 1: একই Area-এর মধ্যে Routing
- Level 2: বিভিন্ন Area-এর মধ্যে Routing

### Basic Configuration

```bash
R1(config)# router isis
R1(config-router)# net 49.0001.0000.0000.0001.00
```

Interface-এ IS-IS চালু করা:

```bash
R1(config)# interface g0/0
R1(config-if)# ip router isis
```

### Verification

```bash
show isis neighbors
show isis database
```

---

## ৭. EGP (Exterior Gateway Protocol)

**EGP** বিভিন্ন Autonomous System-এর মধ্যে Routing Information আদান-প্রদানের জন্য ব্যবহৃত হয়।

আধুনিক Internet-এ প্রধানত **BGP (Border Gateway Protocol)** ব্যবহৃত হয়।

### IGP vs EGP

| বৈশিষ্ট্য | IGP | EGP |
|---|---|---|
| কাজের ক্ষেত্র | একটি AS-এর মধ্যে | বিভিন্ন AS-এর মধ্যে |
| প্রধান কাজ | Internal Routing | Inter-AS Routing |
| উদাহরণ | OSPF, RIP, EIGRP, IS-IS | BGP |

---

## ৮. ASN (Autonomous System Number)

**ASN** হলো একটি Autonomous System শনাক্ত করার জন্য ব্যবহৃত Unique Number।

### ASN-এর প্রকারভেদ

| ধরন | Range |
|---|---|
| Public 16-bit ASN | 1–64511 |
| Private 16-bit ASN | 64512–65534 |
| Public 32-bit ASN | 1–4294967295 |

Private ASN নির্দিষ্ট Private Use-এর জন্য সংরক্ষিত।

---

## ৯. BGP (Border Gateway Protocol)

BGP একটি **Path-Vector EGP**, যা বিভিন্ন Autonomous System-এর মধ্যে Routing Information আদান-প্রদান করে।

এটি Internet-এর প্রধান Routing Protocol।

### প্রধান বৈশিষ্ট্য

- TCP Port 179 ব্যবহার করে
- AS-PATH ব্যবহার করে Routing Loop প্রতিরোধ করে
- Policy-Based Routing Decision সমর্থন করে
- eBGP: বিভিন্ন AS-এর মধ্যে
- iBGP: একই AS-এর মধ্যে

### Basic BGP Configuration

উদাহরণ: R1-এর AS হলো 65001 এবং Neighbor-এর AS হলো 65002।

```bash
R1(config)# router bgp 65001
R1(config-router)# neighbor 10.0.0.2 remote-as 65002
R1(config-router)# network 192.168.10.0 mask 255.255.255.0
```

**Note:** Advertise করার Network-টি সঠিক Prefix-সহ Routing Table-এ থাকতে হবে।

### BGP Verification

```bash
show ip bgp summary
show ip bgp
show ip route bgp
```

---

## ১০. Administrative Distance (AD)

Administrative Distance নির্ধারণ করে একই Destination Prefix-এর জন্য একাধিক Routing Source থাকলে কোন Source-কে বেশি প্রাধান্য দেওয়া হবে।

**কম AD = বেশি Preferred**

| Route Source | Default AD |
|---|---:|
| Connected | 0 |
| Static | 1 |
| eBGP | 20 |
| EIGRP | 90 |
| OSPF | 110 |
| IS-IS | 115 |
| RIP | 120 |
| iBGP | 200 |

---

## ১১. Route Redistribution

**Redistribution** হলো একটি Routing Protocol থেকে শেখা Route অন্য Routing Protocol-এ যুক্ত করার প্রক্রিয়া।

উদাহরণ: OSPF Route-কে EIGRP-তে Redistribute করা।

```bash
R1(config)# router eigrp 100
R1(config-router)# redistribute ospf 1
```

Redistribution-এর ক্ষেত্রে Metric Configuration এবং Route Filtering প্রয়োজন হতে পারে, যাতে Routing Loop প্রতিরোধ করা যায়।

---

## ১২. গুরুত্বপূর্ণ Routing Commands

| Command | কাজ |
|---|---|
| `show ip route` | Routing Table দেখায় |
| `show ip protocols` | Active Routing Protocol দেখায় |
| `show ip interface brief` | Interface Status ও IP দেখায় |
| `show running-config` | বর্তমান Configuration দেখায় |
| `show ip route static` | Static Route দেখায় |
| `show ip ospf neighbor` | OSPF Neighbor পরীক্ষা করে |
| `show ip eigrp neighbors` | EIGRP Neighbor পরীক্ষা করে |
| `show ip bgp summary` | BGP Session পরীক্ষা করে |
| `ping <IP>` | Connectivity পরীক্ষা করে |
| `traceroute <IP>` | Packet-এর Path পরীক্ষা করে |
| `copy running-config startup-config` | Configuration Save করে |

---

## Summary

- **Routing:** Packet Forward করার জন্য Best Path নির্বাচন।
- **Static Routing:** ম্যানুয়ালি Configure করা Route।
- **Dynamic Routing:** স্বয়ংক্রিয়ভাবে Route শেখা ও আদান-প্রদান।
- **IGP:** একটি Autonomous System-এর অভ্যন্তরে Routing।
- **EGP:** বিভিন্ন Autonomous System-এর মধ্যে Routing।
- **OSPF:** Cost ও Area ব্যবহারকারী Link-State Protocol।
- **EIGRP:** DUAL ব্যবহারকারী Advanced Distance-Vector Protocol।
- **RIP:** Hop Count ব্যবহারকারী Distance-Vector Protocol।
- **IS-IS:** SPF ও Hierarchical Routing ব্যবহারকারী Link-State Protocol।
- **BGP:** Autonomous System-এর মধ্যে ব্যবহৃত Path-Vector Protocol।
- **ASN:** Autonomous System শনাক্ত করার Number।

### References

- [Cisco IP Routing Configuration Guide](https://www.cisco.com/c/en/us/td/docs/routers/ios-xe/ip-routing/b-ip-routing/m_iri-ip-prot-indep-0.html)
- [Cisco Basic Router Configuration](https://www.cisco.com/en/US/docs/routers/access/800/850/software/configuration/guide/routconf.html)

