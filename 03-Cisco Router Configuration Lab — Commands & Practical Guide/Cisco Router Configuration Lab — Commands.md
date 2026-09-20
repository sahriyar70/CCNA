# Cisco Router Configuration Lab — Commands & Practical Guide

এই নোটে Cisco Router-এর basic configuration, password setup, hostname, interface IP configuration, PC IP configuration, default gateway, configuration checking, saving configuration, Telnet এবং remote access-এর প্রয়োজনীয় command ও তাদের ব্যবহার ধাপে ধাপে দেখানো হয়েছে।

---

# Table of Contents

1. [Cisco IOS Command Modes](#1-cisco-ios-command-modes)
2. [Enter Privileged EXEC Mode — enable](#2-enter-privileged-exec-mode--enable)
3. [Enter Global Configuration Mode — configure terminal](#3-enter-global-configuration-mode--configure-terminal)
4. [Set Router Hostname](#4-set-router-hostname)
5. [Set Enable Password](#5-set-enable-password)
6. [Set Enable Secret](#6-set-enable-secret)
7. [Configure Console Password](#7-configure-console-password)
8. [Show Running Configuration](#8-show-running-configuration)
9. [Show Startup Configuration](#9-show-startup-configuration)
10. [Save Running Configuration](#10-save-running-configuration)
11. [Show IP Interface](#11-show-ip-interface)
12. [Configure Router Interface IP](#12-configure-router-interface-ip)
13. [no shutdown](#13-no-shutdown)
14. [do Command](#14-do-command)
15. [Configure PC IP Address](#15-configure-pc-ip-address)
16. [Default Gateway Configuration](#16-default-gateway-configuration)
17. [Verify Connectivity — ping](#17-verify-connectivity--ping)
18. [Telnet Configuration](#18-telnet-configuration)
19. [Remote Configuration Using Telnet](#19-remote-configuration-using-telnet)
20. [Useful Show Commands](#20-useful-show-commands)
21. [Complete Basic Router Configuration](#21-complete-basic-router-configuration)
22. [Complete Router + PC Lab Example](#22-complete-router--pc-lab-example)
23. [Command Mode Quick Reference](#23-command-mode-quick-reference)
24. [Important Lab Commands Cheat Sheet](#24-important-lab-commands-cheat-sheet)

---

# 1. Cisco IOS Command Modes

Cisco router-এ বিভিন্ন কাজের জন্য বিভিন্ন command mode থাকে।

## User EXEC Mode

Prompt:

```text
Router>
```

এই mode-এ basic monitoring command ব্যবহার করা যায়।

Example:

```text
Router> enable
```

---

## Privileged EXEC Mode

Prompt:

```text
Router#
```

এখানে router-এর advanced monitoring এবং configuration mode-এ প্রবেশ করা যায়।

Example:

```text
Router# configure terminal
```

---

## Global Configuration Mode

Prompt:

```text
Router(config)#
```

Router-এর global configuration এখানে করা হয়।

Example:

```text
Router(config)# hostname R1
```

---

## Interface Configuration Mode

Prompt:

```text
Router(config-if)#
```

Router-এর নির্দিষ্ট interface configure করার সময় এই mode ব্যবহার হয়।

Example:

```text
Router(config)# interface gigabitEthernet 0/0
Router(config-if)# ip address 192.168.1.1 255.255.255.0
```

---

## Line Configuration Mode

Console বা VTY line configure করার সময় ব্যবহার হয়।

Example:

```text
Router(config)# line console 0
Router(config-line)#
```

Telnet/remote access-এর জন্য:

```text
Router(config)# line vty 0 4
Router(config-line)#
```

---

# 2. Enter Privileged EXEC Mode — `enable`

## Command

```text
Router> enable
```

অথবা সংক্ষেপে:

```text
Router> en
```

## কাজ

User EXEC mode থেকে Privileged EXEC mode-এ নিয়ে যায়।

```text
Router>
     ↓
   enable
     ↓
Router#
```

## কেন দরকার?

অনেক গুরুত্বপূর্ণ command এবং configuration mode-এ প্রবেশ করতে `enable` ব্যবহার করতে হয়।

---

# 3. Enter Global Configuration Mode — `configure terminal`

## Command

```text
Router# configure terminal
```

সংক্ষেপে:

```text
Router# conf t
```

## কাজ

Privileged EXEC mode থেকে Global Configuration mode-এ প্রবেশ করে।

```text
Router#
   ↓
configure terminal
   ↓
Router(config)#
```

## Example

```text
Router> enable
Router# configure terminal
Router(config)#
```

---

# 4. Set Router Hostname

Router-এর default নাম সাধারণত:

```text
Router
```

নিজের router-এর নাম দেওয়ার জন্য:

```text
Router(config)# hostname R1
```

এরপর prompt হবে:

```text
R1(config)#
```

## কাজ

Router-কে সহজে শনাক্ত করার জন্য নাম দেওয়া হয়।

যেমন:

```text
hostname R1
```

অথবা অন্য router:

```text
hostname R2
```

Network-এ একাধিক router থাকলে hostname খুব গুরুত্বপূর্ণ।

---

# 5. Set Enable Password

Privileged EXEC mode-এ password দেওয়ার জন্য:

```text
R1(config)# enable password cisco
```

এখন:

```text
R1> enable
Password:
```

password চাইবে।

## কাজ

`enable` mode-এ প্রবেশ করার সময় password protection দেয়।

### Important

`enable password` পুরোনো/কম নিরাপদ পদ্ধতি। বাস্তব network-এ সাধারণত `enable secret` ব্যবহার করা ভালো।

---

# 6. Set Enable Secret

## Command

```text
R1(config)# enable secret class
```

এটি Privileged EXEC mode-এর জন্য password protection তৈরি করে।

```text
R1> enable
Password:
```

## Enable Password বনাম Enable Secret

| Command           | ব্যবহার                     |
| ----------------- | --------------------------- |
| `enable password` | Enable mode password        |
| `enable secret`   | বেশি নিরাপদ enable password |

যদি দুটোই configure করা থাকে:

```text
enable secret
```

সাধারণত `enable password`-এর চেয়ে অগ্রাধিকার পায়।

### Recommended

Lab-এ password concept বোঝার জন্য দুটোই শেখা ভালো, কিন্তু বাস্তব configuration-এ:

```text
enable secret <password>
```

ব্যবহার করা উচিত।

---

# 7. Configure Console Password

Router-এর Console port দিয়ে সরাসরি access করলে console password ব্যবহার করা যায়।

## Step 1 — Global Configuration Mode

```text
R1# configure terminal
```

## Step 2 — Console Line

```text
R1(config)# line console 0
```

## Step 3 — Password

```text
R1(config-line)# password cisco
```

## Step 4 — Enable Login

```text
R1(config-line)# login
```

## Complete Configuration

```text
R1(config)# line console 0
R1(config-line)# password cisco
R1(config-line)# login
```

## কাজ

Console connection-এর মাধ্যমে router-এ ঢোকার সময় password চাইবে।

---

# 8. Show Running Configuration

## Command

```text
R1# show running-config
```

সংক্ষেপে:

```text
R1# show run
```

## কাজ

Router বর্তমানে RAM-এ যে configuration ব্যবহার করছে সেটি দেখায়।

এটাকে বলা হয়:

**Running Configuration**

এখানে দেখা যেতে পারে:

* Hostname
* Password configuration
* Interface configuration
* IP address
* Console configuration
* VTY configuration
* অন্যান্য active configuration

---

# 9. Show Startup Configuration

## Command

```text
R1# show startup-config
```

সংক্ষেপে:

```text
R1# show start
```

## কাজ

NVRAM-এ সংরক্ষিত configuration দেখায়।

এটাকে বলা হয়:

**Startup Configuration**

Router restart/reload করার পর সাধারণত এই configuration ব্যবহার করে।

---

# Running Config vs Startup Config

| বিষয়                      | Running Config        | Startup Config        |
| ------------------------- | --------------------- | --------------------- |
| থাকে                      | RAM                   | NVRAM                 |
| Active configuration      | হ্যাঁ                 | না                    |
| Router restart-এর পর থাকে | না, save না করলে      | হ্যাঁ                 |
| Command                   | `show running-config` | `show startup-config` |

### সহজভাবে

```text
Running Config
      ↓
বর্তমানে router-এ কাজ করছে

Startup Config
      ↓
Restart হলে ব্যবহার হবে
```

---

# 10. Save Running Configuration

Router-এ configuration করার পর সেটি save করা খুব গুরুত্বপূর্ণ।

## Method 1

```text
R1# copy running-config startup-config
```

এরপর সাধারণত:

```text
Destination filename [startup-config]?
```

Enter চাপলে save হবে।

---

## Method 2

```text
R1# write memory
```

অথবা:

```text
R1# wr
```

এটি পুরোনো/shortcut style command।

### Recommended

```text
R1# copy running-config startup-config
```

ব্যবহার করা ভালো, কারণ command-টি পরিষ্কারভাবে বোঝা যায়।

---

# 11. Show IP Interface

Router-এর interface এবং IP-related status দেখার জন্য:

```text
R1# show ip interface
```

আরও সংক্ষিপ্ত তথ্যের জন্য:

```text
R1# show ip interface brief
```

## `show ip interface brief`

এটি অত্যন্ত গুরুত্বপূর্ণ lab command।

Example:

```text
R1# show ip interface brief
```

Output-এর মতো হতে পারে:

```text
Interface              IP-Address      OK? Method Status                Protocol
GigabitEthernet0/0     192.168.1.1     YES manual up                    up
GigabitEthernet0/1     unassigned      YES unset  administratively down down
```

## এখানে কী দেখব?

### IP-Address

Interface-এ কোন IP দেওয়া হয়েছে।

### Status

Physical/interface status।

### Protocol

Data link protocol status।

### গুরুত্বপূর্ণ

```text
Status: up
Protocol: up
```

সাধারণভাবে interface operational অবস্থায় আছে।

---

# 12. Configure Router Interface IP

Router-এর interface-এ IP address বসানোর জন্য।

ধরি:

```text
Interface: GigabitEthernet0/0
IP: 192.168.1.1
Subnet Mask: 255.255.255.0
```

## Commands

```text
R1# configure terminal
R1(config)# interface gigabitEthernet 0/0
R1(config-if)# ip address 192.168.1.1 255.255.255.0
```

এরপর interface চালু করতে:

```text
R1(config-if)# no shutdown
```

## সম্পূর্ণ configuration

```text
R1# configure terminal
R1(config)# interface gigabitEthernet 0/0
R1(config-if)# ip address 192.168.1.1 255.255.255.0
R1(config-if)# no shutdown
```

---

# 13. `no shutdown`

## Command

```text
R1(config-if)# no shutdown
```

সংক্ষেপে:

```text
R1(config-if)# no shut
```

## কাজ

Interface administratively disabled থাকলে সেটি enable করে।

Cisco router-এর interface অনেক সময় default অবস্থায় shutdown থাকতে পারে।

### যদি দেখো:

```text
administratively down
```

তাহলে:

```text
no shutdown
```

দিতে হবে।

তারপর:

```text
show ip interface brief
```

দিয়ে check করা যায়।

---

# 14. `do` Command

Global বা অন্য configuration mode থেকে privileged EXEC-এর `show` command চালানোর জন্য `do` ব্যবহার করা যায়।

ধরি তুমি আছ:

```text
R1(config)#
```

এখানে সরাসরি:

```text
show running-config
```

সবসময় কাজ করবে না।

তখন:

```text
R1(config)# do show running-config
```

অথবা:

```text
R1(config)# do show ip interface brief
```

## কাজ

Configuration mode থেকে বের না হয়েই EXEC-level command চালানো।

### Example

```text
R1(config)# interface gigabitEthernet 0/0
R1(config-if)# do show ip interface brief
```

---

# 15. Configure PC IP Address

PC-তে IP address দেওয়ার পদ্ধতি Cisco Packet Tracer এবং real Windows PC-তে আলাদা হতে পারে।

---

## Cisco Packet Tracer PC

PC select করো:

```text
PC → Desktop → IP Configuration
```

তারপর:

```text
IP Address:      192.168.1.10
Subnet Mask:     255.255.255.0
Default Gateway: 192.168.1.1
```

যদি DNS দরকার হয়:

```text
DNS Server:      8.8.8.8
```

---

## Example

Router:

```text
192.168.1.1/24
```

PC:

```text
192.168.1.10/24
```

Gateway:

```text
192.168.1.1
```

এখানে PC এবং Router একই subnet-এ আছে।

---

# 16. Default Gateway Configuration

Default Gateway সাধারণত PC/Host-এর জন্য router-এর সেই interface IP যেটি তাকে অন্য network-এ যেতে সাহায্য করে।

ধরি:

```text
PC IP:
192.168.1.10

Subnet Mask:
255.255.255.0

Default Gateway:
192.168.1.1
```

Router interface:

```text
GigabitEthernet0/0
192.168.1.1/24
```

## Network Diagram

```text
PC
192.168.1.10
     |
     |
     |
G0/0
192.168.1.1
   Router
     |
     |
Other Network
```

## Default Gateway-এর কাজ

যদি PC:

```text
192.168.1.20
```

-এ যেতে চায় এবং destination একই subnet-এ থাকে, তাহলে PC সরাসরি যোগাযোগ করতে পারে।

কিন্তু যদি PC:

```text
8.8.8.8
```

-এর মতো অন্য network-এর destination-এ যেতে চায়, তাহলে traffic default gateway-এর কাছে পাঠায়।

---

# 17. Verify Connectivity — `ping`

Network connection test করার জন্য:

```text
ping
```

ব্যবহার করা হয়।

## Router থেকে

```text
R1# ping 192.168.1.10
```

## PC থেকে

Packet Tracer PC:

```text
PC> ping 192.168.1.1
```

Windows CMD:

```text
ping 192.168.1.1
```

## কাজ

দুই device-এর মধ্যে network connectivity আছে কি না তা পরীক্ষা করা।

---

# 18. Telnet Configuration

Telnet হলো একটি remote access protocol, যার মাধ্যমে network-এর অন্য device থেকে router-এর CLI access করা যায়।

**Security Note:** Telnet password/plaintext communication-এর কারণে নিরাপদ নয়। বাস্তব network-এ SSH সাধারণত Telnet-এর পরিবর্তে ব্যবহার করা হয়। Lab ও learning-এর জন্য Telnet configuration শেখা যায়।

---

## Step 1 — Router-এ IP Address

প্রথমে router-এর interface-এ IP থাকতে হবে।

```text
R1(config)# interface gigabitEthernet 0/0
R1(config-if)# ip address 192.168.1.1 255.255.255.0
R1(config-if)# no shutdown
```

---

## Step 2 — Enable Password/Secret

```text
R1(config)# enable secret class
```

---

## Step 3 — VTY Line Configuration

```text
R1(config)# line vty 0 4
```

## Password

```text
R1(config-line)# password cisco
```

## Login

```text
R1(config-line)# login
```

## Complete

```text
R1(config)# line vty 0 4
R1(config-line)# password cisco
R1(config-line)# login
```

---

# 19. Remote Configuration Using Telnet

ধরি:

Router:

```text
192.168.1.1
```

PC:

```text
192.168.1.10
```

PC থেকে:

```text
PC> telnet 192.168.1.1
```

Password চাইবে:

```text
Password:
```

Password দেওয়ার পর router-এর CLI পাওয়া যাবে।

Example:

```text
R1>
```

তারপর:

```text
R1> enable
```

Enable password চাইতে পারে।

তারপর:

```text
R1#
```

এখন remote PC থেকে router configure করা সম্ভব।

---

# 20. Useful Show Commands

Cisco lab-এ নিচের commands খুব গুরুত্বপূর্ণ।

## Show running configuration

```text
show running-config
```

সংক্ষেপে:

```text
show run
```

কাজ:

বর্তমান active configuration দেখায়।

---

## Show startup configuration

```text
show startup-config
```

কাজ:

NVRAM-এ save করা configuration দেখায়।

---

## Show IP interface brief

```text
show ip interface brief
```

কাজ:

সব interface-এর IP এবং status দ্রুত দেখায়।

---

## Show IP interface

```text
show ip interface
```

কাজ:

Interface-এর বিস্তারিত IP information দেখায়।

---

## Show interfaces

```text
show interfaces
```

কাজ:

Interface-এর বিস্তারিত operational information দেখায়।

---

## Show version

```text
show version
```

কাজ:

Router-এর IOS/software, hardware এবং অন্যান্য system information দেখায়।

---

## Show running configuration from config mode

```text
do show running-config
```

কাজ:

Configuration mode থেকে বের না হয়ে running configuration দেখা।

---

# 21. Complete Basic Router Configuration

নিচে একটি basic router configuration একসাথে দেওয়া হলো।

```text
Router> enable

Router# configure terminal

Router(config)# hostname R1

R1(config)# enable secret class

R1(config)# line console 0
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# exit

R1(config)# interface gigabitEthernet 0/0
R1(config-if)# ip address 192.168.1.1 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit

R1(config)# line vty 0 4
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# exit

R1(config)# end

R1# show ip interface brief

R1# show running-config

R1# copy running-config startup-config
```

---

# 22. Complete Router + PC Lab Example

## Network Topology

```text
        Ethernet Cable
PC ------------------------ Router
                              |
                              |
                         G0/0 Interface
```

## Router Configuration

Router-এর IP:

```text
192.168.1.1
```

Subnet Mask:

```text
255.255.255.0
```

Commands:

```text
Router> enable

Router# configure terminal

Router(config)# hostname R1

R1(config)# enable secret class

R1(config)# line console 0
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# exit

R1(config)# interface gigabitEthernet 0/0
R1(config-if)# ip address 192.168.1.1 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit

R1(config)# line vty 0 4
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# exit

R1(config)# end
```

---

## PC Configuration

Packet Tracer:

```text
PC
→ Desktop
→ IP Configuration
```

Set:

```text
IP Address:      192.168.1.10
Subnet Mask:     255.255.255.0
Default Gateway: 192.168.1.1
```

---

## Test Connection

PC থেকে:

```text
ping 192.168.1.1
```

যদি reply পাওয়া যায়, তাহলে PC এবং Router-এর মধ্যে connectivity কাজ করছে।

---

# 23. Command Mode Quick Reference

| Command                              | Mode              | কাজ                            |
| ------------------------------------ | ----------------- | ------------------------------ |
| `enable`                             | User EXEC         | Privileged mode-এ যায়          |
| `configure terminal`                 | Privileged EXEC   | Global configuration mode      |
| `hostname R1`                        | Global Config     | Router-এর নাম পরিবর্তন         |
| `enable password cisco`              | Global Config     | Enable password                |
| `enable secret class`                | Global Config     | Secure enable password         |
| `line console 0`                     | Global Config     | Console configuration          |
| `password cisco`                     | Line Config       | Line password                  |
| `login`                              | Line Config       | Password authentication enable |
| `line vty 0 4`                       | Global Config     | Remote access line             |
| `interface g0/0`                     | Global Config     | Interface configuration        |
| `ip address ...`                     | Interface Config  | Interface IP address           |
| `no shutdown`                        | Interface Config  | Interface enable               |
| `show running-config`                | Privileged EXEC   | Active configuration           |
| `show startup-config`                | Privileged EXEC   | Saved configuration            |
| `show ip interface brief`            | Privileged EXEC   | Interface/IP summary           |
| `copy running-config startup-config` | Privileged EXEC   | Configuration save             |
| `do show ...`                        | Config Mode       | Config mode থেকে show command  |
| `ping`                               | EXEC              | Connectivity test              |
| `telnet`                             | PC/Network Device | Remote CLI access              |

---

# 24. Important Lab Commands Cheat Sheet

## Basic Navigation

```text
enable
configure terminal
exit
end
```

---

## Router Name

```text
hostname R1
```

---

## Enable Password

```text
enable password cisco
```

Recommended:

```text
enable secret class
```

---

## Console Password

```text
line console 0
password cisco
login
```

---

## Remote VTY/Telnet Password

```text
line vty 0 4
password cisco
login
```

---

## Interface IP

```text
interface gigabitEthernet 0/0
ip address 192.168.1.1 255.255.255.0
no shutdown
```

---

## Check Interface

```text
show ip interface brief
```

---

## Check Current Configuration

```text
show running-config
```

---

## Check Saved Configuration

```text
show startup-config
```

---

## Save Configuration

```text
copy running-config startup-config
```

---

## Test Connectivity

```text
ping 192.168.1.1
```

---

## Telnet

```text
telnet 192.168.1.1
```

---

# Important Concepts to Remember

### 1. `enable`

```text
Router> → Router#
```

Privileged EXEC mode-এ যায়।

### 2. `configure terminal`

```text
Router# → Router(config)#
```

Configuration mode-এ যায়।

### 3. `interface`

```text
Router(config)# → Router(config-if)#
```

নির্দিষ্ট interface configure করে।

### 4. `ip address`

Interface-এ IP address বসায়।

```text
ip address 192.168.1.1 255.255.255.0
```

### 5. `no shutdown`

Interface চালু করে।

### 6. `show running-config`

বর্তমানে RAM-এ থাকা active configuration দেখায়।

### 7. `show startup-config`

NVRAM-এ save করা configuration দেখায়।

### 8. `copy running-config startup-config`

বর্তমান configuration save করে।

```text
Running Config
      ↓
copy running-config startup-config
      ↓
Startup Config
```

### 9. `do`

Configuration mode থেকে বের না হয়ে EXEC command চালাতে সাহায্য করে।

```text
do show ip interface brief
```

### 10. `line console 0`

Console access configure করে।

### 11. `line vty 0 4`

Remote VTY access configure করে।

### 12. Default Gateway

PC-এর অন্য network-এ যাওয়ার জন্য router-এর local interface address সাধারণত default gateway হিসেবে দেওয়া হয়।

Example:

```text
PC IP:       192.168.1.10
Mask:        255.255.255.0
Gateway:     192.168.1.1
```

---

# Recommended Lab Practice Order

একটি Cisco Router lab করলে এই order অনুসরণ করলে শেখা সহজ হবে:

```text
1. enable
      ↓
2. configure terminal
      ↓
3. hostname
      ↓
4. enable secret
      ↓
5. console password
      ↓
6. interface নির্বাচন
      ↓
7. IP address configure
      ↓
8. no shutdown
      ↓
9. show ip interface brief
      ↓
10. PC-তে IP configure
      ↓
11. Default Gateway configure
      ↓
12. ping test
      ↓
13. running-config check
      ↓
14. startup-config check
      ↓
15. running-config → startup-config save
      ↓
16. VTY/Telnet configuration
      ↓
17. Remote access test
```

---

# Final Mental Model

একটি basic Cisco router lab-কে এভাবে মনে রাখতে পারো:

```text
Access Router
     ↓
enable
     ↓
Enter Configuration
     ↓
configure terminal
     ↓
Give Router Identity
     ↓
hostname R1
     ↓
Protect Router
     ↓
enable secret
     ↓
Protect Console
     ↓
line console 0
     ↓
Configure Interface
     ↓
interface g0/0
     ↓
Assign IP
     ↓
ip address
     ↓
Turn Interface On
     ↓
no shutdown
     ↓
Configure PC
     ↓
IP + Subnet Mask + Gateway
     ↓
Test
     ↓
ping
     ↓
Verify
     ↓
show ip interface brief
     ↓
Save
     ↓
copy running-config startup-config
     ↓
Remote Access
     ↓
line vty 0 4
     ↓
Telnet / SSH
```

> **Lab Rule:** Configuration করার পর সবসময় `show ip interface brief` দিয়ে interface status check করো এবং configuration শেষ হলে `copy running-config startup-config` দিয়ে save করো।

> **Security Note:** Telnet শেখার জন্য lab-এ ব্যবহার করা যায়, কিন্তু production network-এ plaintext authentication-এর কারণে SSH সাধারণত নিরাপদ remote-management protocol হিসেবে ব্যবহৃত হয়।
