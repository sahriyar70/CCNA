# Cisco ACL (Access Control List) – Complete Guide

## 1. What is ACL?

**ACL (Access Control List)** is a Cisco Router and Switch security feature used to control network traffic.

ACL determines:
- Which IP addresses can access a network.
- Which IP addresses should be blocked.
- Which protocols (TCP, UDP, ICMP) are allowed or denied.
- Which ports can be accessed.
- Which hosts can communicate with other networks.

**Main Purpose:** Traffic Filtering (Permit / Deny).

---

## 2. Types of ACL

Cisco ACL has four commonly discussed types:

| ACL Type | Function |
|---|---|
| Standard ACL | Filters traffic using Source IP only |
| Extended ACL | Filters using Source, Destination, Protocol and Port |
| Named ACL | Uses a custom name instead of a number |
| Dynamic ACL | Provides temporary access through authentication |

Other ACL variations include Time-Based ACL, Reflexive ACL and IPv6 ACL.

### 2.1 Standard ACL

Standard ACL filters traffic based only on the Source IP Address.

**ACL Number Range:**
- 1–99
- 1300–1999

**Configuration:**

```cisco
Router(config)# access-list 10 permit 192.168.10.0 0.0.0.255
Router(config)# access-list 10 deny any
```

**Apply to Interface:**

```cisco
Router(config)# interface g0/0
Router(config-if)# ip access-group 10 in
```

- `10` = ACL Number
- `permit` = Allow Traffic
- `deny` = Block Traffic
- `in` = Filter Incoming Traffic

### 2.2 Extended ACL

Extended ACL filters traffic based on Source IP, Destination IP, Protocol and Port Number.

**ACL Number Range:**
- 100–199
- 2000–2699

**Configuration:**

```cisco
Router(config)# access-list 110 permit tcp 192.168.10.0 0.0.0.255 any eq 80
Router(config)# access-list 110 permit tcp 192.168.10.0 0.0.0.255 any eq 443
Router(config)# access-list 110 deny ip any any
```

- TCP Port 80 = HTTP
- TCP Port 443 = HTTPS
- `any` = Any IP Address
- `eq` = Equal / Specific Port

**Apply to Interface:**

```cisco
Router(config)# interface g0/0
Router(config-if)# ip access-group 110 in
```

### 2.3 Named ACL

Named ACL uses a custom name instead of a numeric ACL number.

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

Dynamic ACL is also known as Lock-and-Key ACL.

It provides temporary network access after user authentication.

Working Process:
- Initially, access is blocked.
- The user performs authentication.
- After successful authentication, temporary access is granted.

It is an advanced ACL configuration.

---

## 3. Wildcard Mask

A Wildcard Mask determines which parts of an IP Address should be matched.

| Subnet Mask | Wildcard Mask |
|---|---|
| 255.255.255.0 | 0.0.0.255 |
| 255.255.0.0 | 0.0.255.255 |
| 255.0.0.0 | 0.255.255.255 |
| 255.255.255.255 | 0.0.0.0 |
| 0.0.0.0 | 255.255.255.255 |

**Formula:**

Wildcard Mask = 255.255.255.255 − Subnet Mask

Example:

```cisco
access-list 10 permit 192.168.10.0 0.0.0.255
```

This matches all hosts in the `192.168.10.0/24` network.

Special Keywords:

```cisco
host 192.168.10.10
```
Matches only one specific IP address.

```cisco
any
```
Matches any IP address.

---

## 4. What Can We Do with ACL?

### 4.1 Block a Specific IP

```cisco
access-list 10 deny host 192.168.10.10
access-list 10 permit any
```

### 4.2 Allow a Specific Network

```cisco
access-list 10 permit 192.168.10.0 0.0.0.255
```

### 4.3 Block HTTP Traffic

```cisco
access-list 110 deny tcp any host 192.168.20.10 eq 80
access-list 110 permit ip any any
```

### 4.4 Block HTTPS Traffic

```cisco
access-list 110 deny tcp any any eq 443
access-list 110 permit ip any any
```

### 4.5 Block ICMP / Ping

```cisco
access-list 110 deny icmp any any
access-list 110 permit ip any any
```

### 4.6 Block FTP

```cisco
access-list 110 deny tcp any any eq 21
access-list 110 permit ip any any
```

### 4.7 Block Telnet

```cisco
access-list 110 deny tcp any any eq 23
access-list 110 permit ip any any
```

### 4.8 Block Communication Between Two Hosts

```cisco
access-list 110 deny ip host 192.168.10.10 host 192.168.20.10
access-list 110 permit ip any any
```

### 4.9 Allow SSH

```cisco
access-list 110 permit tcp any any eq 22
```

**Note:** Each example is a separate ACL example. When multiple rules are configured in the same ACL, rule order is important.

---

## 5. ACL Interface Direction

ACL can be applied in two directions.

### IN (Inbound)

Filters packets when they enter a router interface.

```cisco
interface g0/0
ip access-group 10 in
```

### OUT (Outbound)

Filters packets when they leave a router interface.

```cisco
interface g0/1
ip access-group 10 out
```

**Remember:**
- IN = Traffic entering the router.
- OUT = Traffic leaving the router.

---

## 6. Important ACL Rules

1. ACL rules are processed from top to bottom.
2. The first matching rule is applied.
3. Every ACL has an implicit `deny any` at the end.
4. Use `permit ip any any` when other traffic should be allowed.
5. Standard ACLs are generally placed close to the destination.
6. Extended ACLs are generally placed close to the source.
7. Only one IPv4 ACL can be applied per interface, per direction.
8. Rule order is very important.

---

## 7. ACL Verification Commands

```cisco
show access-lists
```
Displays all ACLs.

```cisco
show access-lists 10
```
Displays a specific ACL.

```cisco
show ip interface g0/0
```
Shows whether an ACL is applied to the interface.

```cisco
show running-config
```
Displays the current configuration.

```cisco
show ip access-lists
```
Displays detailed IPv4 ACL information.

---

## 8. ACL Modify and Delete Commands

### Remove a Specific Rule

For Named ACL:

```cisco
ip access-list extended BLOCK_WEB
no 10
```

### Delete an Entire ACL

Numbered ACL:

```cisco
no access-list 10
```

Named ACL:

```cisco
no ip access-list extended BLOCK_WEB
```

### Remove ACL from an Interface

```cisco
interface g0/0
no ip access-group 10 in
```

---

## 9. Time-Based ACL

Time-Based ACL controls network traffic according to a specific time schedule.

Example:

```cisco
Router(config)# time-range OFFICE
Router(config-time-range)# periodic weekdays 8:00 to 17:00

Router(config)# ip access-list extended TIME_ACL
Router(config-ext-nacl)# permit tcp any any eq 80 time-range OFFICE
```

HTTP traffic is permitted on weekdays from 8:00 AM to 5:00 PM.

---

## 10. IPv6 ACL

IPv6 ACL is used to filter traffic in IPv6 networks.

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

## 11. Important Protocols and Ports

| Protocol | Port | Function |
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

**Network Topology:**

- Router: R1
- LAN 1: 192.168.10.0/24
- LAN 2: 192.168.20.0/24
- PC1: 192.168.10.10
- Server: 192.168.20.10

**Task:**

Block PC1 from pinging the Server while allowing other network communication.

**Configuration:**

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

PC1 cannot ping the Server, but other IP traffic is allowed.

---

## 13. Quick Revision

| Topic | Key Concept |
|---|---|
| Standard ACL | Source IP |
| Extended ACL | Source + Destination + Protocol + Port |
| Named ACL | Custom ACL Name |
| Dynamic ACL | Authentication-Based Access |
| IN | Incoming Traffic |
| OUT | Outgoing Traffic |
| Permit | Allow |
| Deny | Block |
| Wildcard Mask | IP Matching |
| Implicit Deny | Default Block |
| Time-Based ACL | Time-Scheduled Filtering |
| IPv6 ACL | IPv6 Traffic Filtering |

---

**Final Note:** ACL is an essential networking feature used for Network Security, Traffic Filtering, Access Control and preventing unauthorized communication.