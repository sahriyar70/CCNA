# DHCP, LAN Configuration & ARP — Networking Notes

## Table of Contents

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

# 1. LAN Configuration

## What is LAN?

**LAN (Local Area Network)** is a network that connects devices within a limited geographical area.

Examples:

* Home network
* Office network
* School network
* Computer lab
* Small company network

Example:

```text
PC ─────┐
Laptop ─┤
Phone ──┼── Switch ─── Router ─── Internet
Printer ┤
Server ─┘
```

---

# 2. LAN Components

A basic LAN can contain:

| Device       | Purpose                        |
| ------------ | ------------------------------ |
| PC           | End device                     |
| Laptop       | End device                     |
| Switch       | Connects devices inside LAN    |
| Router       | Connects different networks    |
| Access Point | Provides wireless connectivity |
| Server       | Provides network services      |
| Printer      | Network resource               |

### Important Network Information

Each device may have:

* IP Address
* MAC Address
* Subnet Mask
* Default Gateway
* DNS Server

Example:

```text
IP Address      : 192.168.1.10
Subnet Mask     : 255.255.255.0
Default Gateway : 192.168.1.1
DNS Server      : 8.8.8.8
```

---

# 3. Basic LAN Configuration

Suppose we have this network:

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

Example configuration:

| Device | IP Address   | Subnet Mask   | Gateway     |
| ------ | ------------ | ------------- | ----------- |
| Router | 192.168.1.1  | 255.255.255.0 | —           |
| PC1    | 192.168.1.10 | 255.255.255.0 | 192.168.1.1 |
| PC2    | 192.168.1.20 | 255.255.255.0 | 192.168.1.1 |
| PC3    | 192.168.1.30 | 255.255.255.0 | 192.168.1.1 |

All devices belong to:

```text
192.168.1.0/24
```

---

## Testing LAN Connectivity

Use:

```bash
ping 192.168.1.1
```

To test another PC:

```bash
ping 192.168.1.20
```

If the devices can communicate successfully, the basic LAN connectivity is working.

---

# 4. DHCP

## What is DHCP?

**DHCP (Dynamic Host Configuration Protocol)** automatically provides network configuration information to client devices.

Instead of manually configuring every device, a DHCP server can provide:

* IP Address
* Subnet Mask
* Default Gateway
* DNS Server
* Lease information

Example:

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

# 5. DHCP DORA Process

DHCP commonly uses the **DORA** process.

```text
D → Discover
O → Offer
R → Request
A → Acknowledge
```

### 1. DHCP Discover

The client searches for available DHCP servers.

```text
Client → DHCP Server
"Is there any DHCP server available?"
```

### 2. DHCP Offer

The DHCP server offers network configuration.

```text
DHCP Server → Client
"You can use 192.168.1.10"
```

### 3. DHCP Request

The client requests the offered configuration.

```text
Client → DHCP Server
"I want to use this IP address."
```

### 4. DHCP ACK

The DHCP server confirms the configuration.

```text
DHCP Server → Client
"Your configuration is approved."
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

# 6. DHCP Configuration

A DHCP server maintains a pool of available IP addresses.

Example:

```text
Network: 192.168.1.0/24

DHCP Pool:
192.168.1.100
192.168.1.101
192.168.1.102
...
192.168.1.200
```

When a client requests an IP address, the DHCP server can assign an available address from the pool.

### Example

```text
PC1 → 192.168.1.100
PC2 → 192.168.1.101
PC3 → 192.168.1.102
```

---

## DHCP Reservation

A DHCP server can reserve a specific IP address for a particular device.

This is commonly based on the device's MAC address.

Example:

```text
MAC Address → 00:11:22:33:44:55

Reserved IP → 192.168.1.50
```

This allows the same device to receive the same IP address when requesting DHCP configuration.

---

# 7. DHCP Important Ports

DHCP uses **UDP**.

| Protocol | Port | Purpose     |
| -------- | ---: | ----------- |
| UDP      |   67 | DHCP Server |
| UDP      |   68 | DHCP Client |

```text
Client → UDP 68
Server → UDP 67
```

---

# 8. ARP

## What is ARP?

**ARP (Address Resolution Protocol)** is used in IPv4 networks to find the **MAC address associated with an IPv4 address** on the local network.

In simple terms:

```text
IP Address → MAC Address
```

Example:

```text
IP: 192.168.1.20

ARP asks:
"Who has 192.168.1.20?"
```

The device owning that IP responds with its MAC address.

---

# 9. How ARP Works

Suppose PC1 wants to communicate with PC2.

```text
PC1
IP: 192.168.1.10
MAC: AA:AA:AA:AA:AA:AA

PC2
IP: 192.168.1.20
MAC: BB:BB:BB:BB:BB:BB
```

PC1 knows PC2's IP address but does not know its MAC address.

### Step 1 — ARP Request

PC1 sends an ARP request:

```text
Who has 192.168.1.20?
```

The request is broadcast on the local network.

### Step 2 — ARP Reply

PC2 responds:

```text
192.168.1.20 is at BB:BB:BB:BB:BB:BB
```

### Step 3 — Communication

PC1 can now send Ethernet frames to PC2's MAC address.

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

# 10. ARP Cache

Devices maintain an **ARP cache/table** containing recently learned IP-to-MAC mappings.

Example:

```text
IP Address       MAC Address
192.168.1.1      AA:BB:CC:DD:EE:01
192.168.1.20     AA:BB:CC:DD:EE:02
192.168.1.30     AA:BB:CC:DD:EE:03
```

The cache prevents the device from performing a new ARP request every time it communicates with a known local device.

ARP entries can expire and be learned again.

---

# 11. ARP Commands

## Windows

View the ARP cache:

```cmd
arp -a
```

Delete an ARP entry:

```cmd
arp -d <IP>
```

Example:

```cmd
arp -d 192.168.1.20
```

---

## Linux

View ARP/neighbour information:

```bash
ip neigh
```

Example output:

```text
192.168.1.1 dev eth0 lladdr aa:bb:cc:dd:ee:ff REACHABLE
```

---

# 12. ARP Security Risks

ARP does not provide strong authentication for ARP messages.

One important attack is:

## ARP Spoofing / ARP Poisoning

An attacker may send false ARP information to a device.

Example:

```text
Real Router:
192.168.1.1 → AA:AA:AA:AA:AA:AA

Attacker:
192.168.1.1 → XX:XX:XX:XX:XX:XX
```

The victim may incorrectly associate the router's IP address with the attacker's MAC address.

This can potentially enable:

* Man-in-the-Middle attacks
* Traffic interception
* Traffic redirection
* Network disruption

### Defensive Measures

Organizations can use security controls such as:

* Dynamic ARP Inspection (DAI)
* DHCP Snooping
* Static ARP entries where appropriate
* Network segmentation
* Monitoring for unusual ARP activity

---

# 13. DHCP vs ARP

| Feature      | DHCP                                | ARP                          |
| ------------ | ----------------------------------- | ---------------------------- |
| Full Name    | Dynamic Host Configuration Protocol | Address Resolution Protocol  |
| Main Purpose | Provides network configuration      | Resolves IPv4 address to MAC |
| Works With   | IP configuration                    | IPv4 + MAC                   |
| Transport    | UDP                                 | ARP protocol                 |
| Common Ports | UDP 67/68                           | No TCP/UDP port              |
| Main Use     | Assign IP configuration             | Find local MAC address       |

### Simple Difference

```text
DHCP:
"Which IP configuration should I use?"

ARP:
"Which MAC address belongs to this IP?"
```

---

# 14. Network Communication Flow

A simple LAN communication process can look like this:

```text
1. Device connects to LAN
          ↓
2. DHCP provides IP configuration
          ↓
3. Device knows its IP, subnet mask and gateway
          ↓
4. Device wants to communicate with another local device
          ↓
5. ARP finds the destination MAC address
          ↓
6. Ethernet frame is created
          ↓
7. Switch forwards the frame
          ↓
8. Destination device receives it
```

Example:

```text
          DHCP
           ↓
       IP Configuration
           ↓
Client ── ARP ──→ Find MAC
           ↓
       Ethernet Frame
           ↓
         Switch
           ↓
      Destination
```

---

# 15. Quick Reference

## LAN

```text
LAN = Local Area Network
```

Common components:

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

DORA:

```text
Discover
Offer
Request
ACK
```

Ports:

```text
UDP 67 → Server
UDP 68 → Client
```

---

## ARP

```text
ARP = Address Resolution Protocol
```

Main purpose:

```text
IPv4 Address → MAC Address
```

Windows:

```cmd
arp -a
```

Linux:

```bash
ip neigh
```

Security concern:

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
Get IP Configuration    Find MAC Address
       │                     │
       └──────────┬──────────┘
                  ↓
             Communication
                  ↓
                Switch
                  ↓
             Destination
```

### Remember

> **DHCP gives the device its network configuration.**

> **ARP helps the device find the MAC address associated with a local IPv4 address.**

> **LAN connects devices within a local network.**
