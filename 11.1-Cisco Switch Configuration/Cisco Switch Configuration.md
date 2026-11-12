# Cisco Switch Configuration – Complete Guide

এই নোটে Cisco Switch-এর গুরুত্বপূর্ণ Configuration, Networking Features এবং প্রয়োজনীয় Cisco IOS Commands দেওয়া হয়েছে।

## 1. Basic Switch Configuration

সুইচের প্রাথমিক সেটআপ ও পরিচিতি নির্ধারণ।

```bash
enable
configure terminal

hostname SW1
no ip domain-lookup
enable secret 12345
service password-encryption

banner motd # Unauthorized Access Prohibited #

```

### Console Password

```bash
line console 0
password cisco
login
logging synchronous
exit
```

### VTY Password (Remote Access)

```bash
line vty 0 15
password cisco
login
transport input telnet
exit
```

### SSH Configuration

নিরাপদভাবে Remote Access করার জন্য SSH ব্যবহার করা হয়।

```bash
ip domain-name lab.local
username admin secret Admin123
crypto key generate rsa
```

RSA Key তৈরি করার সময় 1024 বা 2048 Bit নির্বাচন করো।

```bash
ip ssh version 2

line vty 0 15
login local
transport input ssh
exit
```

---

## 2. VLAN Configuration

VLAN (Virtual LAN) একটি Physical Switch-কে একাধিক Logical Network-এ ভাগ করে।

### Create VLAN

```bash
configure terminal

vlan 10
name HR
exit

vlan 20
name IT
exit

vlan 30
name Guest
exit
```

### Assign VLAN to Access Port

```bash
interface fastEthernet 0/1
switchport mode access
switchport access vlan 10
exit

interface fastEthernet 0/2
switchport mode access
switchport access vlan 20
exit
```

### Assign Multiple Ports

```bash
interface range fastEthernet 0/3-10
switchport mode access
switchport access vlan 30
exit
```

### Delete VLAN

```bash
no vlan 10
```

---

## 3. Trunk Configuration

Trunk Port একাধিক VLAN-এর Traffic বহন করতে পারে। সাধারণত Switch-to-Switch বা Switch-to-Router সংযোগে ব্যবহৃত হয়।

```bash
interface gigabitEthernet 0/1
switchport mode trunk
switchport trunk allowed vlan 10,20,30
exit
```

### Allow All VLANs

```bash
switchport trunk allowed vlan all
```

### Remove VLAN from Trunk

```bash
switchport trunk allowed vlan remove 30
```

### Native VLAN

```bash
switchport trunk native vlan 99
```

দুই পাশের Trunk Port-এ Native VLAN একই রাখতে হবে।

---

## 4. Inter-VLAN Routing

এক VLAN থেকে অন্য VLAN-এ Communication করার ব্যবস্থা।

এটি মূলত Layer 3 Switch অথবা Router দিয়ে করা যায়।

### Method 1: Router-on-a-Stick

Router-এর একটি Physical Interface-এর মাধ্যমে একাধিক VLAN-এর Routing করা হয়।

**Router Configuration:**

```bash
enable
configure terminal

interface gigabitEthernet 0/0
no shutdown
exit

interface gigabitEthernet 0/0.10
encapsulation dot1Q 10
ip address 192.168.10.1 255.255.255.0
exit

interface gigabitEthernet 0/0.20
encapsulation dot1Q 20
ip address 192.168.20.1 255.255.255.0
exit
```

**Switch Configuration:**

```bash
interface gigabitEthernet 0/1
switchport mode trunk
```

### Method 2: Layer 3 Switch

Layer 3 Switch নিজেই Inter-VLAN Routing করতে পারে।

```bash
configure terminal

ip routing

interface vlan 10
ip address 192.168.10.1 255.255.255.0
no shutdown
exit

interface vlan 20
ip address 192.168.20.1 255.255.255.0
no shutdown
exit
```

PC Default Gateway:

- VLAN 10 → 192.168.10.1
- VLAN 20 → 192.168.20.1

---

## 5. SVI (Switch Virtual Interface)

SVI হলো VLAN-এর জন্য তৈরি Virtual Interface। এটি Management এবং Layer 3 Routing-এ ব্যবহৃত হয়।

### Management IP Configuration

```bash
interface vlan 1
ip address 192.168.1.2 255.255.255.0
no shutdown
exit
```

### Default Gateway (Layer 2 Switch)

```bash
ip default-gateway 192.168.1.1
```

---

## 6. EtherChannel Configuration

একাধিক Physical Link একত্র করে একটি Logical Link তৈরি করা হয়। এতে Bandwidth এবং Redundancy বাড়ে।

### LACP Configuration

**Switch 1 এবং Switch 2 উভয় পাশে:**

```bash
interface range gigabitEthernet 0/1-2
switchport mode trunk
channel-group 1 mode active
exit

interface port-channel 1
switchport mode trunk
exit
```

### LACP Modes

- Active: LACP Negotiation শুরু করে।
- Passive: অপর পাশ থেকে LACP-এর অপেক্ষা করে।

---

## 7. STP (Spanning Tree Protocol)

Network-এ Switching Loop প্রতিরোধ করে।

### Check STP

```bash
show spanning-tree
show spanning-tree vlan 10
```

### Set Root Bridge

```bash
spanning-tree vlan 10 root primary
```

### Set Secondary Root Bridge

```bash
spanning-tree vlan 10 root secondary
```

### PortFast

End Device-এর সঙ্গে যুক্ত Access Port দ্রুত Forwarding State-এ যেতে সাহায্য করে।

```bash
interface fastEthernet 0/1
spanning-tree portfast
```

### BPDU Guard

Access Port-এ অনাকাঙ্ক্ষিত BPDU এলে Port-কে Err-disabled করে সুরক্ষা দেয়।

```bash
interface fastEthernet 0/1
spanning-tree bpduguard enable
```

---

## 8. Port Security

Switch Port-এ কোন MAC Address-এর Device Connect হতে পারবে তা নিয়ন্ত্রণ করে।

```bash
interface fastEthernet 0/1
switchport mode access
switchport port-security
switchport port-security maximum 2
switchport port-security mac-address sticky
switchport port-security violation shutdown
```

### Violation Modes

- Shutdown: Port বন্ধ করে দেয়।
- Restrict: Traffic Drop করে এবং Violation Count বাড়ায়।
- Protect: অননুমোদিত MAC-এর Traffic Drop করে।

---

## 9. DHCP Snooping

অননুমোদিত DHCP Server থেকে আসা DHCP Offer প্রতিরোধ করে।

```bash
ip dhcp snooping
ip dhcp snooping vlan 10,20
```

Trusted Uplink Port:

```bash
interface gigabitEthernet 0/1
ip dhcp snooping trust
```

Untrusted Access Port:

```bash
interface range fastEthernet 0/1-10
ip dhcp snooping limit rate 10
```

---

## 10. Dynamic ARP Inspection (DAI)

ARP Spoofing বা ARP Poisoning প্রতিরোধে সাহায্য করে।

```bash
ip arp inspection vlan 10,20
```

Trusted Uplink:

```bash
interface gigabitEthernet 0/1
ip arp inspection trust
```

এটি DHCP Snooping Binding Database-এর সঙ্গে কাজ করতে পারে।

---

## 11. Port Management

### Enable Port

```bash
interface fastEthernet 0/1
no shutdown
```

### Disable Port

```bash
interface fastEthernet 0/1
shutdown
```

### Description

```bash
interface fastEthernet 0/1
description Connected-to-PC1
```

### Disable Unused Ports

```bash
interface range fastEthernet 0/10-24
shutdown
```

---

## 12. Port Mirroring (SPAN)

একটি Port-এর Traffic অন্য Port-এ Monitor করার জন্য পাঠানো হয়। Wireshark দিয়ে Network Analysis করা যায়।

```bash
monitor session 1 source interface fastEthernet 0/1
monitor session 1 destination interface fastEthernet 0/24
```

এখানে:

- Source: যে Port-এর Traffic Monitor করবে।
- Destination: যেখানে Traffic পাঠানো হবে।

---

## 13. CDP (Cisco Discovery Protocol)

Cisco Network Device-এর প্রতিবেশী Device সম্পর্কে তথ্য সংগ্রহ করে।

```bash
show cdp neighbors
show cdp neighbors detail
```

### Disable CDP

```bash
no cdp run
```

### Enable CDP

```bash
cdp run
```

---

## 14. LLDP (Link Layer Discovery Protocol)

বিভিন্ন Vendor-এর Network Device-এর মধ্যে Neighbor Information আদান-প্রদান করে।

```bash
lldp run
```

### Check LLDP

```bash
show lldp neighbors
show lldp neighbors detail
```

---

## 15. NTP Configuration

Network Device-এর Clock Time Synchronize করে।

```bash
ntp server 192.168.1.100
```

Check:

```bash
show clock
show ntp status
```

---

## 16. SNMP Configuration

Network Monitoring System দিয়ে Switch Monitor করার জন্য SNMP ব্যবহৃত হয়।

### SNMPv2c Example

```bash
snmp-server community Monitor123 RO
snmp-server location Server-Room
snmp-server contact admin
```

**Note:** বাস্তব Network-এ SNMPv3 ব্যবহার করা বেশি নিরাপদ।

---

## 17. Switch Configuration Verification Commands

| Command | কাজ |
|---|---|
| `show running-config` | বর্তমান Configuration |
| `show startup-config` | Saved Configuration |
| `show vlan brief` | VLAN List |
| `show interfaces status` | Port Status |
| `show interfaces trunk` | Trunk Information |
| `show mac address-table` | MAC Address Table |
| `show ip interface brief` | Interface IP Status |
| `show spanning-tree` | STP Information |
| `show etherchannel summary` | EtherChannel Status |
| `show port-security` | Port Security Status |
| `show cdp neighbors` | CDP Neighbors |
| `show lldp neighbors` | LLDP Neighbors |
| `show ip dhcp snooping` | DHCP Snooping Status |
| `show arp` | ARP Table |

---

## 18. Save Configuration

Running Configuration-কে Startup Configuration-এ Save করতে:

```bash
copy running-config startup-config
```

অথবা:

```bash
write memory
```

### Erase Configuration

```bash
erase startup-config
reload
```

---

## Important Switching Concepts

একটি Cisco Switch-এ আরও যেসব বিষয় শেখা প্রয়োজন:

1. VLAN – Logical Network Segmentation
2. Access Port – End Device Connection
3. Trunk Port – Multiple VLAN Traffic
4. Inter-VLAN Routing – VLAN-এর মধ্যে Communication
5. SVI – Virtual Interface
6. EtherChannel – Link Aggregation
7. STP / RSTP – Loop Prevention
8. Port Security – MAC Address Control
9. DHCP Snooping – Rogue DHCP Protection
10. Dynamic ARP Inspection – ARP Spoofing Protection
11. BPDU Guard – STP Protection
12. CDP / LLDP – Neighbor Discovery
13. SPAN – Traffic Monitoring
14. SNMP – Network Monitoring
15. NTP – Time Synchronization
16. SSH – Secure Remote Management
17. VTP – VLAN Information Management
18. Private VLAN – VLAN-এর মধ্যে Port Isolation
19. Voice VLAN – IP Phone Traffic Separation
20. QoS – Traffic Prioritization
21. Storm Control – Broadcast/Multicast/Unicast Storm Control
22. 802.1X – Port-Based Network Access Control
23. Switch Stacking – একাধিক Switch-কে একটি Logical System হিসেবে পরিচালনা
24. Layer 2 vs Layer 3 Switching

**Note:** সব Cisco Switch-এ সব Feature বা Command Support করে না। Switch Model, IOS Version এবং Layer 2/Layer 3 Capability অনুযায়ী Command পরিবর্তিত হতে পারে। Packet Tracer-এও কিছু Feature সীমিত থাকতে পারে।