# IP Services – CCNA Notes

## 1. IP Services কী?

IP Services হলো Network-এর বিভিন্ন Service, যা Device-গুলোর মধ্যে Communication, IP Configuration, Monitoring এবং Data Transfer করতে সাহায্য করে।

## 2. Client-Server Model

- **Client:** যে Service-এর জন্য Request পাঠায়।
- **Server:** যে Request গ্রহণ করে এবং Service প্রদান করে।

উদাহরণ: DHCP Client, DHCP Server-এর কাছ থেকে IP Address সংগ্রহ করে।

## 3. UDP & TCP

| বিষয় | UDP | TCP |
|---|---|---|
| পূর্ণরূপ | User Datagram Protocol | Transmission Control Protocol |
| Connection | Connectionless | Connection-oriented |
| Reliability | Delivery নিশ্চিত করে না | Reliable |
| ব্যবহার | DHCP, DNS, NTP | HTTP, HTTPS, FTP |

## 4. DHCP (Dynamic Host Configuration Protocol)

DHCP স্বয়ংক্রিয়ভাবে Client-কে IP Configuration প্রদান করে।

DHCP প্রদান করে:
- IP Address
- Subnet Mask
- Default Gateway
- DNS Server
- Lease Time

**DHCP Ports:**
- Server: UDP 67
- Client: UDP 68

### DORA Process

| Step | কাজ |
|---|---|
| Discover | Client DHCP Server খোঁজে |
| Offer | Server IP Address অফার করে |
| Request | Client IP Address গ্রহণের Request পাঠায় |
| Acknowledge | Server IP Configuration নিশ্চিত করে |

### Cisco DHCP Server Configuration

```cisco
enable
configure terminal

ip dhcp excluded-address 192.168.10.1 192.168.10.10

ip dhcp pool LAN
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 dns-server 8.8.8.8
 domain-name lab.local
exit

interface gigabitEthernet 0/0
 ip address 192.168.10.1 255.255.255.0
 no shutdown
```

### DHCP Verification

```cisco
show ip dhcp pool
show ip dhcp binding
show ip dhcp server statistics
```

### DHCP Client Configuration

```cisco
interface gigabitEthernet 0/0
 ip address dhcp
 no shutdown
```

Windows Client:

```powershell
ipconfig /release
ipconfig /renew
ipconfig /all
```

Linux Client:

```bash
sudo dhclient -r
sudo dhclient
ip addr
```

## 5. DHCP Relay Agent

DHCP Client এবং Server আলাদা Subnet-এ থাকলে Relay Agent DHCP Request Server-এর কাছে Forward করে।

Cisco Configuration:

```cisco
interface gigabitEthernet 0/1
 ip helper-address 192.168.20.10
```

এখানে `192.168.20.10` হলো DHCP Server-এর IP Address।

## 6. DNS (Domain Name System)

DNS Domain Name-কে IP Address-এ Resolve করে।

উদাহরণ: `google.com` → IP Address

**Port:** TCP/UDP 53

Commands:

```bash
nslookup google.com
dig google.com
```

Windows:

```powershell
ipconfig /flushdns
nslookup google.com
```

## 7. NAT (Network Address Translation)

NAT Private IP Address-কে Public IP Address-এ Translate করে।

Types:
- Static NAT
- Dynamic NAT
- PAT (Port Address Translation)

### Cisco PAT Configuration

```cisco
access-list 1 permit 192.168.10.0 0.0.0.255

interface gigabitEthernet 0/0
 ip nat inside

interface gigabitEthernet 0/1
 ip nat outside

ip nat inside source list 1 interface gigabitEthernet 0/1 overload
```

Verification:

```cisco
show ip nat translations
show ip nat statistics
```

## 8. NTP (Network Time Protocol)

NTP Network Device-গুলোর Clock Synchronize করে।

**Port:** UDP 123

Cisco Configuration:

```cisco
ntp server 192.168.10.10
```

Verification:

```cisco
show ntp status
show ntp associations
```

## 9. SNMP (Simple Network Management Protocol)

SNMP Network Device Monitor ও Manage করতে ব্যবহৃত হয়।

- UDP 161: SNMP Queries
- UDP 162: SNMP Traps

Components:
- SNMP Manager
- SNMP Agent
- MIB (Management Information Base)

Cisco Example:

```cisco
snmp-server community public RO
snmp-server host 192.168.10.10 version 2c public
```

**Note:** `public` শুধু Lab Example। Production Network-এ নিরাপদ Authentication এবং SNMPv3 ব্যবহার করা ভালো।

## 10. Syslog

Syslog Network Device-এর Log Message Centralized Server-এ পাঠায়।

**Common Port:** UDP 514

Cisco Configuration:

```cisco
logging host 192.168.10.10
logging trap informational
```

Verification:

```cisco
show logging
```

## 11. TFTP & FTP

| Protocol | Port | কাজ |
|---|---|---|
| TFTP | UDP 69 | Simple File Transfer |
| FTP Data | TCP 20 | File Data Transfer |
| FTP Control | TCP 21 | Command & Control |
| SFTP | TCP 22 | Secure File Transfer |

Cisco TFTP Backup:

```cisco
copy running-config tftp:
```

Cisco TFTP Restore:

```cisco
copy tftp: running-config
```

## 12. SSH & Telnet

| Protocol | Port | Security |
|---|---|---|
| SSH | TCP 22 | Encrypted |
| Telnet | TCP 23 | Unencrypted |

### Cisco SSH Configuration

```cisco
hostname R1
ip domain-name lab.local

username admin secret StrongPassword123

crypto key generate rsa
```

RSA Key তৈরি করার সময় IOS চাইলে 1024 বা 2048 বিট নির্বাচন করো।

```cisco
line vty 0 4
 login local
 transport input ssh
```

Verification:

```cisco
show ip ssh
show crypto key mypubkey rsa
```

## 13. RADIUS & TACACS+

এই Protocol-গুলো Network Access Authentication, Authorization এবং Accounting (AAA) পরিচালনায় ব্যবহৃত হয়।

| Protocol | Port | Transport |
|---|---:|---|
| RADIUS Authentication | 1812 | UDP |
| RADIUS Accounting | 1813 | UDP |
| TACACS+ | 49 | TCP |

## 14. Important IP Services Ports

| Service | Protocol | Port |
|---|---|---:|
| FTP Data | TCP | 20 |
| FTP Control | TCP | 21 |
| SSH | TCP | 22 |
| Telnet | TCP | 23 |
| DNS | TCP/UDP | 53 |
| DHCP Server | UDP | 67 |
| DHCP Client | UDP | 68 |
| TFTP | UDP | 69 |
| HTTP | TCP | 80 |
| NTP | UDP | 123 |
| SNMP | UDP | 161 |
| SNMP Trap | UDP | 162 |
| HTTPS | TCP | 443 |
| Syslog | UDP (Common) | 514 |
| RADIUS Authentication | UDP | 1812 |
| RADIUS Accounting | UDP | 1813 |
| TACACS+ | TCP | 49 |

## 15. Important Cisco Verification Commands

```cisco
show ip interface brief
show running-config
show ip route
show ip dhcp binding
show ip dhcp pool
show ip nat translations
show ip nat statistics
show logging
show ntp status
show ip ssh
show access-lists
```

## 16. Quick Revision

- **DHCP:** Automatically IP Address প্রদান করে।
- **DORA:** Discover → Offer → Request → Acknowledge।
- **UDP:** Connectionless Protocol।
- **DNS:** Domain Name Resolve করে।
- **NAT:** Private ও Public IP Translate করে।
- **NTP:** Time Synchronize করে।
- **SNMP:** Network Monitoring করে।
- **Syslog:** Device Logs সংগ্রহ করে।
- **TFTP:** Simple File Transfer।
- **SSH:** Secure Remote Access।
- **DHCP Relay:** অন্য Subnet-এর DHCP Server-এ Request Forward করে।
- **AAA:** Authentication, Authorization, Accounting।

## 17. CCNA Lab Practice

Packet Tracer-এ নিচের Lab তৈরি করে অনুশীলন করো:

1. একটি Router, একটি Switch, তিনটি PC এবং একটি Server নাও।
2. Router-এ DHCP Pool তৈরি করো।
3. PC-গুলোতে DHCP চালু করো।
4. `ipconfig` দিয়ে IP Address যাচাই করো।
5. আলাদা Subnet তৈরি করে DHCP Relay (`ip helper-address`) পরীক্ষা করো।
6. `show ip dhcp binding` দিয়ে Client-এর IP Lease যাচাই করো।

**Goal:** DHCP, DORA, Client-Server Communication এবং DHCP Relay বাস্তবে বুঝতে পারা।