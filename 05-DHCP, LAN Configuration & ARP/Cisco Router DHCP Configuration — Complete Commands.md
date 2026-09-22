# Cisco Router DHCP Configuration — Complete Commands

## 1. DHCP Server কী?

Cisco Router-কে DHCP Server হিসেবে Configure করলে Router LAN-এর Client Device-গুলোকে স্বয়ংক্রিয়ভাবে দিতে পারে:

* IP Address
* Subnet Mask
* Default Gateway
* DNS Server
* Lease Information

---

# 2. Basic DHCP Configuration

ধরা যাক আমাদের LAN:

```text
Network      : 192.168.10.0/24
Router       : 192.168.10.1
DHCP Range   : 192.168.10.11 - 192.168.10.254
Default GW   : 192.168.10.1
DNS           : 8.8.8.8
```

### Step 1 — Privileged EXEC Mode

```cisco
Router> enable
```

`enable` কমান্ড দিয়ে Privileged EXEC Mode-এ যাওয়া হয়।

```text
Router#
```

---

# 3. Enter Global Configuration Mode

```cisco
Router# configure terminal
```

অথবা:

```cisco
Router# conf t
```

এখন:

```text
Router(config)#
```

---

# 4. Configure Router Interface

প্রথমে LAN Interface নির্বাচন করুন:

```cisco
Router(config)# interface gigabitEthernet 0/0
```

Interface-এ IP Address দিন:

```cisco
Router(config-if)# ip address 192.168.10.1 255.255.255.0
```

Interface চালু করুন:

```cisco
Router(config-if)# no shutdown
```

সংক্ষেপে:

```cisco
Router(config)# interface gigabitEthernet 0/0
Router(config-if)# ip address 192.168.10.1 255.255.255.0
Router(config-if)# no shutdown
```

---

# 5. Exclude IP Addresses

DHCP Pool থেকে কিছু IP বাদ দিতে:

```cisco
Router(config)# ip dhcp excluded-address 192.168.10.1
```

একটি Range বাদ দিতে:

```cisco
Router(config)# ip dhcp excluded-address 192.168.10.1 192.168.10.10
```

এর ফলে:

```text
192.168.10.1
      ↓
192.168.10.10
```

এই IP-গুলো DHCP Client-কে দেওয়া হবে না।

### সাধারণত কেন Exclude করা হয়?

যেমন:

```text
192.168.10.1  → Router
192.168.10.2  → Server
192.168.10.3  → Printer
192.168.10.4  → Access Point
```

এগুলো Static IP হিসেবে ব্যবহার করা যেতে পারে।

---

# 6. Create DHCP Pool

DHCP Pool তৈরি করতে:

```cisco
Router(config)# ip dhcp pool LAN_POOL
```

এখন:

```text
Router(dhcp-config)#
```

এখানে `LAN_POOL` হলো DHCP Pool-এর নাম।

---

# 7. Configure DHCP Network

```cisco
Router(dhcp-config)# network 192.168.10.0 255.255.255.0
```

এটি DHCP Server-কে বলে কোন Network-এর Client-দের IP Address দিতে হবে।

---

# 8. Configure Default Gateway

```cisco
Router(dhcp-config)# default-router 192.168.10.1
```

এটি Client Device-এর Default Gateway হিসেবে `192.168.10.1` প্রদান করবে।

---

# 9. Configure DNS Server

```cisco
Router(dhcp-config)# dns-server 8.8.8.8
```

একাধিক DNS Server দিতে:

```cisco
Router(dhcp-config)# dns-server 8.8.8.8 1.1.1.1
```

---

# 10. Configure DHCP Lease Time

DHCP Client কতদিন IP Address ব্যবহার করতে পারবে তা Configure করা যায়।

```cisco
Router(dhcp-config)# lease 7
```

এখানে:

```text
7 = 7 Days
```

Hours এবং Minutes-ও দেওয়া যায়:

```cisco
Router(dhcp-config)# lease 7 12
```

অর্থ:

```text
7 Days 12 Hours
```

আরও নির্দিষ্টভাবে:

```cisco
Router(dhcp-config)# lease 7 12 30
```

অর্থ:

```text
7 Days 12 Hours 30 Minutes
```

---

# 11. Complete DHCP Configuration

একটি সম্পূর্ণ Basic Configuration:

```cisco
Router> enable
Router# configure terminal

Router(config)# interface gigabitEthernet 0/0
Router(config-if)# ip address 192.168.10.1 255.255.255.0
Router(config-if)# no shutdown
Router(config-if)# exit

Router(config)# ip dhcp excluded-address 192.168.10.1 192.168.10.10

Router(config)# ip dhcp pool LAN_POOL
Router(dhcp-config)# network 192.168.10.0 255.255.255.0
Router(dhcp-config)# default-router 192.168.10.1
Router(dhcp-config)# dns-server 8.8.8.8
Router(dhcp-config)# lease 7
Router(dhcp-config)# exit
```

এখন DHCP Server Client-দের IP Address দেওয়ার জন্য প্রস্তুত।

---

# 12. Configure Client PC

Cisco Packet Tracer-এ PC-তে:

```text
PC
 ↓
Desktop
 ↓
IP Configuration
 ↓
DHCP
```

PC DHCP Server থেকে স্বয়ংক্রিয়ভাবে IP Configuration পাবে।

উদাহরণ:

```text
IP Address      : 192.168.10.11
Subnet Mask     : 255.255.255.0
Default Gateway : 192.168.10.1
DNS Server      : 8.8.8.8
```

পরের PC পেতে পারে:

```text
192.168.10.12
```

তারপর:

```text
192.168.10.13
```

ইত্যাদি।

---

# 13. Verify DHCP Configuration

## Show DHCP Pool Information

```cisco
Router# show ip dhcp pool
```

এতে DHCP Pool-এর তথ্য দেখা যায়।

---

## Show DHCP Assigned Addresses

```cisco
Router# show ip dhcp binding
```

এটি কোন Client-কে কোন IP Address দেওয়া হয়েছে তা দেখায়।

উদাহরণ:

```text
IP address       Client-ID
192.168.10.11    0100.xxxx.xxxx.xxxx
192.168.10.12    0100.xxxx.xxxx.xxxx
```

---

## Show DHCP Statistics

```cisco
Router# show ip dhcp server statistics
```

DHCP Server-এর বিভিন্ন statistics দেখা যায়।

---

# 14. Check Interface Status

```cisco
Router# show ip interface brief
```

উদাহরণ:

```text
Interface              IP-Address      Status
GigabitEthernet0/0     192.168.10.1   up
```

যদি Interface বন্ধ থাকে:

```cisco
Router(config)# interface gigabitEthernet 0/0
Router(config-if)# no shutdown
```

---

# 15. Check Running Configuration

```cisco
Router# show running-config
```

এখানে বর্তমান Running Configuration দেখা যায়।

DHCP অংশে এমন কিছু দেখতে পারো:

```text
ip dhcp excluded-address 192.168.10.1 192.168.10.10

ip dhcp pool LAN_POOL
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 dns-server 8.8.8.8
 lease 7
```

---

# 16. Check Startup Configuration

```cisco
Router# show startup-config
```

এতে NVRAM-এ Save করা Configuration দেখা যায়।

---

# 17. Save DHCP Configuration

Configuration Save করতে:

```cisco
Router# copy running-config startup-config
```

অথবা:

```cisco
Router# write memory
```

Save করার পর Router Restart হলেও Configuration থাকার কথা।

---

# 18. Test DHCP Client Connectivity

Client IP পাওয়ার পর Router-এর Gateway-তে Ping করুন:

```bash
ping 192.168.10.1
```

তারপর Internet/DNS connectivity থাকলে:

```bash
ping 8.8.8.8
```

DNS Test করতে:

```bash
ping google.com
```

---

# 19. Remove DHCP Configuration

DHCP Pool Delete করতে:

```cisco
Router(config)# no ip dhcp pool LAN_POOL
```

Excluded Address Remove করতে:

```cisco
Router(config)# no ip dhcp excluded-address 192.168.10.1 192.168.10.10
```

---

# 20. Important DHCP Commands

| Command                              | কাজ                          |
| ------------------------------------ | ---------------------------- |
| `ip dhcp excluded-address`           | IP বাদ দেয়                  |
| `ip dhcp pool`                       | DHCP Pool তৈরি করে           |
| `network`                            | DHCP Network নির্ধারণ করে    |
| `default-router`                     | Default Gateway নির্ধারণ করে |
| `dns-server`                         | DNS Server নির্ধারণ করে      |
| `lease`                              | DHCP Lease Time নির্ধারণ করে |
| `show ip dhcp pool`                  | DHCP Pool দেখায়             |
| `show ip dhcp binding`               | Assigned IP দেখায়           |
| `show ip dhcp server statistics`     | DHCP Statistics দেখায়       |
| `show ip interface brief`            | Interface Status দেখায়      |
| `show running-config`                | বর্তমান Configuration দেখায় |
| `show startup-config`                | Saved Configuration দেখায়   |
| `copy running-config startup-config` | Configuration Save করে       |

---

# 21. DHCP Configuration Flow

```text
Router
  │
  ├── Configure LAN Interface
  │
  ├── Assign Router IP
  │
  ├── Exclude Reserved IPs
  │
  ├── Create DHCP Pool
  │
  ├── Configure Network
  │
  ├── Configure Default Gateway
  │
  ├── Configure DNS
  │
  └── Configure Lease
           ↓
       DHCP Server
           ↓
         Client
           ↓
     Gets IP Address
```

---

# 22. Complete Lab Example

### Network

```text
Router
G0/0
192.168.10.1/24
      │
      │
    Switch
   ┌──┼───┐
   │  │   │
  PC1 PC2 PC3
```

### Router Configuration

```cisco
Router> enable
Router# configure terminal

Router(config)# interface gigabitEthernet 0/0
Router(config-if)# ip address 192.168.10.1 255.255.255.0
Router(config-if)# no shutdown
Router(config-if)# exit

Router(config)# ip dhcp excluded-address 192.168.10.1 192.168.10.10

Router(config)# ip dhcp pool LAN_POOL
Router(dhcp-config)# network 192.168.10.0 255.255.255.0
Router(dhcp-config)# default-router 192.168.10.1
Router(dhcp-config)# dns-server 8.8.8.8
Router(dhcp-config)# lease 7
Router(dhcp-config)# exit

Router(config)# end

Router# show ip dhcp pool
Router# show ip dhcp binding
Router# show ip interface brief
Router# copy running-config startup-config
```

### Expected Client IPs

```text
PC1 → 192.168.10.11
PC2 → 192.168.10.12
PC3 → 192.168.10.13
```

Gateway:

```text
192.168.10.1
```

DNS:

```text
8.8.8.8
```

---

# Quick Revision

```text
DHCP Pool
    ↓
Network
    ↓
Default Gateway
    ↓
DNS Server
    ↓
Lease Time
    ↓
Client gets IP
```

### সবচেয়ে গুরুত্বপূর্ণ Commands

```cisco
ip dhcp excluded-address <start-ip> <end-ip>

ip dhcp pool <pool-name>

network <network-address> <subnet-mask>

default-router <gateway-ip>

dns-server <dns-ip>

lease <days>

show ip dhcp pool

show ip dhcp binding

show ip dhcp server statistics
```

> **DHCP-এর মূল কাজ হলো Client Device-কে স্বয়ংক্রিয়ভাবে IP Configuration প্রদান করা।**
