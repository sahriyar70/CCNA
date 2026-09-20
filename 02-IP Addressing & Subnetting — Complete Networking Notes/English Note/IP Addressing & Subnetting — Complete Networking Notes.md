# IP Addressing & Subnetting — Complete Networking Notes

> **Purpose:**
> This note is designed for learning Networking and Cybersecurity. It covers IPv4, IPv6, CIDR, Subnetting, VLSM, Private/Public IP, NAT, DHCP, DNS, and other important IP addressing concepts.

---

## Table of Contents

1. Bit, Byte, and Octet Fundamentals
2. Decimal to Binary & Binary to Decimal Conversion
3. IPv4 Addressing Deep Dive
4. IPv4 Address Structure
5. Classful IP Addressing
6. Private, Public, Loopback & Special IPv4 Addresses
7. Subnet Mask
8. Classless Addressing & CIDR
9. Network Address, Host Address & Broadcast Address
10. Subnetting Step-by-Step
11. Subnetting Formulas
12. VLSM — Variable Length Subnet Mask
13. Supernetting & Route Aggregation
14. Default Gateway
15. DHCP and IP Address Assignment
16. NAT and IP Address Translation
17. IPv6 Addressing Overview
18. IPv6 Address Types
19. IPv6 Compression Rules
20. IPv4 vs IPv6 Comparison
21. IP Addressing & Cybersecurity
22. Quick Reference Tables
23. Practice Questions

---

# 1. Bit, Byte, and Octet Fundamentals

## Bit

**Bit** means **Binary Digit**.

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

This is an 8-bit value, or **1 Byte**.

## Octet

In networking, an 8-bit group is commonly called an **Octet**.

An IPv4 address contains four octets.

Example:

```text
192.168.1.10
```

Here:

```text
192 = Octet 1
168 = Octet 2
1   = Octet 3
10  = Octet 4
```

Therefore:

```text
4 × 8 bits = 32 bits
```

So, an IPv4 address is **32 bits** long.

---

# 2. Decimal to Binary & Binary to Decimal Conversion

## Binary Place Values

The place values of an 8-bit binary number are:

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

Using binary place values:

```text
128 + 32 + 8 = 168
```

Therefore:

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
128 + 32 + 8 + 2 = 170
```

Therefore:

```text
10101010 = 170
```

### Practice

Practice converting the following values into binary:

```text
192
168
10
255
172
16
31
224
```

---

# 3. IPv4 Addressing Deep Dive

## What is an IP Address?

An **IP Address** is a logical address used to identify a device or network interface on a network.

Example:

```text
192.168.1.10
```

An IPv4 address contains:

```text
32 bits
```

It is divided into four octets:

```text
192 . 168 . 1 . 10
```

Each octet has a range of:

```text
0 – 255
```

This is because:

```text
2^8 = 256
```

Therefore, an 8-bit octet can represent **256 values**, from 0 through 255.

---

# 4. IPv4 Address Structure

An IPv4 address can logically be divided into two parts:

```text
Network Portion + Host Portion
```

Example:

```text
192.168.1.10/24
```

The `/24` indicates that the first 24 bits are the network portion.

```text
Network bits = 24
Host bits    = 8
```

Binary representation:

```text
11000000.10101000.00000001.00001010
```

The `/24` mask is:

```text
11111111.11111111.11111111.00000000
```

The first 24 bits represent the network, while the remaining 8 bits represent hosts.

---

# 5. Classful IP Addressing

Before CIDR, IPv4 addresses were divided into Class A, B, C, D, and E.

| Class | First Octet | Default Mask | Common Use            |
| ----- | ----------: | ------------ | --------------------- |
| A     |       1–126 | /8           | Large networks        |
| B     |     128–191 | /16          | Medium networks       |
| C     |     192–223 | /24          | Small networks        |
| D     |     224–239 | N/A          | Multicast             |
| E     |     240–255 | N/A          | Experimental/Reserved |

> `127.x.x.x` is reserved for loopback and is not used as a normal host network range.

## Class A

Default prefix:

```text
/8
```

Subnet mask:

```text
255.0.0.0
```

## Class B

Default prefix:

```text
/16
```

Subnet mask:

```text
255.255.0.0
```

## Class C

Default prefix:

```text
/24
```

Subnet mask:

```text
255.255.255.0
```

## Why Classful Addressing Is Limited

Suppose an organization needs 500 hosts.

A Class C network provides only 254 traditional usable host addresses, so it is not enough.

A Class B network provides a much larger address space than necessary.

This causes address-space inefficiency.

**CIDR** was introduced to provide more flexible network allocation.

---

# 6. Private, Public, Loopback & Special IPv4 Addresses

## Private IPv4 Ranges

Private IP addresses are normally used inside local/internal networks.

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

Examples:

```text
192.168.1.10
10.0.0.25
172.16.5.20
```

## Loopback

```text
127.0.0.0/8
```

The most commonly known loopback address is:

```text
127.0.0.1
```

It is used to test the local device's network stack.

## Link-Local / APIPA

```text
169.254.0.0/16
```

If a device cannot obtain an IPv4 address from DHCP, the operating system may automatically configure an address from this range.

## Unspecified Address

```text
0.0.0.0
```

Depending on the context, `0.0.0.0` can represent an unspecified address or "any address."

## Limited Broadcast

```text
255.255.255.255
```

It is used for broadcast communication on the local network segment.

---

# 7. Subnet Mask

A **subnet mask** identifies which portion of an IPv4 address represents the network and which portion represents the host.

Example:

```text
IP Address:
192.168.1.10

Subnet Mask:
255.255.255.0
```

Binary:

```text
IP:
11000000.10101000.00000001.00001010

Mask:
11111111.11111111.11111111.00000000
```

In a subnet mask:

```text
1 = Network portion
0 = Host portion
```

---

# 8. Classless Addressing & CIDR

## CIDR

CIDR stands for:

**Classless Inter-Domain Routing**

CIDR notation is:

```text
IP Address / Prefix Length
```

Example:

```text
192.168.1.10/24
```

Here:

```text
/24 = First 24 bits are the network portion
```

## Common CIDR Values

| CIDR | Subnet Mask     | Total Addresses | Traditional Usable Hosts |
| ---- | --------------- | --------------: | -----------------------: |
| /8   | 255.0.0.0       |      16,777,216 |               16,777,214 |
| /16  | 255.255.0.0     |          65,536 |                   65,534 |
| /24  | 255.255.255.0   |             256 |                      254 |
| /25  | 255.255.255.128 |             128 |                      126 |
| /26  | 255.255.255.192 |              64 |                       62 |
| /27  | 255.255.255.224 |              32 |                       30 |
| /28  | 255.255.255.240 |              16 |                       14 |
| /29  | 255.255.255.248 |               8 |                        6 |
| /30  | 255.255.255.252 |               4 |                        2 |

> Traditional IPv4 subnet calculations exclude the network and directed broadcast addresses when calculating usable hosts. Special-purpose and point-to-point designs may have exceptions.

---

# 9. Network Address, Host Address & Broadcast Address

Consider:

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

Broadcast address:

```text
192.168.1.255
```

### Important Concepts

```text
Network Address
→ Identifies the entire subnet

Host Address
→ Identifies a device/interface within the subnet

Broadcast Address
→ Used to send traffic to all hosts in the subnet
```

---

# 10. Subnetting Step-by-Step

**Subnetting** means dividing a larger network into smaller networks.

Example:

```text
192.168.1.0/24
```

We want to divide it into `/26` subnets.

## Step 1 — Original Prefix

```text
/24
```

## Step 2 — New Prefix

```text
/26
```

## Step 3 — Borrowed Bits

```text
26 - 24 = 2 bits
```

## Step 4 — Number of Subnets

```text
2^2 = 4 subnets
```

## Step 5 — Host Bits

IPv4 contains 32 bits.

```text
32 - 26 = 6 host bits
```

## Step 6 — Addresses per Subnet

```text
2^6 = 64 addresses
```

Traditional usable hosts:

```text
64 - 2 = 62 hosts
```

## Result

### Subnet 1

```text
Network:   192.168.1.0/26
Hosts:     192.168.1.1 – 192.168.1.62
Broadcast: 192.168.1.63
```

### Subnet 2

```text
Network:   192.168.1.64/26
Hosts:     192.168.1.65 – 192.168.1.126
Broadcast: 192.168.1.127
```

### Subnet 3

```text
Network:   192.168.1.128/26
Hosts:     192.168.1.129 – 192.168.1.190
Broadcast: 192.168.1.191
```

### Subnet 4

```text
Network:   192.168.1.192/26
Hosts:     192.168.1.193 – 192.168.1.254
Broadcast: 192.168.1.255
```

---

# 11. Subnetting Formulas

## Number of Subnets

If `n` bits are borrowed:

```text
Number of Subnets = 2^n
```

## Number of Host Addresses

If `h` host bits remain:

```text
Total Addresses = 2^h
```

Traditional IPv4 subnet:

```text
Usable Hosts = 2^h - 2
```

The two excluded addresses are normally:

```text
1 address = Network
1 address = Broadcast
```

## Finding Host Bits

```text
Host Bits = 32 - Prefix Length
```

Example:

```text
/27

Host Bits = 32 - 27
          = 5
```

Therefore:

```text
2^5 = 32 addresses
```

Traditional usable hosts:

```text
32 - 2 = 30
```

---

# 12. VLSM — Variable Length Subnet Mask

VLSM stands for:

**Variable Length Subnet Mask**

VLSM allows different-sized subnets to be created inside the same larger network according to actual requirements.

Example:

```text
Department A → 100 hosts
Department B → 50 hosts
Department C → 20 hosts
Department D → 10 hosts
```

Using the same subnet size for every department can waste IP addresses.

With VLSM:

```text
100 hosts → /25
50 hosts  → /26
20 hosts  → /27
10 hosts  → /28
```

A practical VLSM design usually allocates the largest host requirement first, followed by smaller requirements.

---

# 13. Supernetting & Route Aggregation

**Supernetting** is the concept of representing multiple smaller contiguous networks using a larger prefix.

Example:

```text
192.168.0.0/24
192.168.1.0/24
192.168.2.0/24
192.168.3.0/24
```

With proper alignment, these can be aggregated as:

```text
192.168.0.0/22
```

This can reduce the number of routing-table entries.

This process is called:

```text
Route Aggregation
```

or:

```text
Route Summarization
```

---

# 14. Default Gateway

When a host needs to send a packet to a destination outside its local subnet, the packet is normally sent to the **default gateway**.

Example:

```text
PC:
192.168.1.10/24

Gateway:
192.168.1.1
```

If the destination is:

```text
192.168.1.50
```

The destination is in the same subnet:

```text
Same subnet → Direct communication
```

If the destination is:

```text
8.8.8.8
```

It is outside the local subnet.

Therefore:

```text
PC
 ↓
Default Gateway
 ↓
Router / Next Hop
 ↓
Destination
```

---

# 15. DHCP and IP Address Assignment

DHCP stands for:

**Dynamic Host Configuration Protocol**

DHCP can automatically provide network configuration to a client.

Common information includes:

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

DHCP normally uses UDP:

```text
Server → UDP 67
Client → UDP 68
```

---

# 16. NAT and IP Address Translation

NAT stands for:

**Network Address Translation**

NAT can translate between private and public IP addresses.

Example:

```text
Private:
192.168.1.10

      ↓
   Router/NAT
      ↓

Public:
203.0.113.x
```

Multiple private hosts in a LAN can share a public IPv4 address, especially when PAT/NAPT is used.

## Common NAT Concepts

### Static NAT

A specific private address is mapped to a specific public address.

### Dynamic NAT

A private address is mapped from a configured pool of public addresses.

### PAT / NAPT

Port numbers are used to multiplex multiple internal connections through a public address.

> NAT is not a complete firewall or security control. NAT performs address translation. Security policies should be implemented separately using firewalls, ACLs, and other controls.

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

IPv6 uses hexadecimal notation.

Example:

```text
2001:db8:1234:0000:0000:8a2e:0370:7334
```

An IPv6 address contains eight hexadecimal groups.

Each group represents:

```text
16 bits
```

Therefore:

```text
8 × 16 = 128 bits
```

---

# 18. IPv6 Address Types

## Unicast

A packet is sent to a single interface.

## Multicast

A packet can be received by multiple interfaces that belong to a multicast group.

## Anycast

The same anycast address can be assigned to multiple interfaces. Routing delivers the packet to an appropriate destination, typically according to network topology.

## IPv6 Link-Local

Normally uses:

```text
FE80::/10
```

An IPv6-enabled interface can use a link-local address for communication on the local network link.

## Global Unicast

Used for globally routable IPv6 communication.

---

# 19. IPv6 Compression Rules

IPv6 addresses can be shortened because they can become very long.

Original:

```text
2001:0db8:0000:0000:0000:0000:0000:0001
```

Leading zeros can be removed:

```text
2001:db8:0:0:0:0:0:1
```

Consecutive zero groups can be replaced once with `::`:

```text
2001:db8::1
```

## Important Rule

The `::` notation can appear **at most once** in an IPv6 address.

---

# 20. IPv4 vs IPv6 Comparison

| Feature               | IPv4                  | IPv6                                            |
| --------------------- | --------------------- | ----------------------------------------------- |
| Address Size          | 32-bit                | 128-bit                                         |
| Notation              | Decimal               | Hexadecimal                                     |
| Example               | 192.168.1.10          | 2001:db8::10                                    |
| Address Space         | Smaller               | Very large                                      |
| Broadcast             | Supported             | No broadcast                                    |
| Multicast             | Supported             | Supported                                       |
| Address Configuration | Manual/DHCP           | SLAAC/DHCPv6/Manual                             |
| Header                | Variable, 20–60 bytes | Fixed 40-byte base header                       |
| NAT                   | Commonly used         | Generally not required for address conservation |
| Neighbor Discovery    | ARP                   | NDP/ICMPv6                                      |

---

# 21. IP Addressing & Cybersecurity

IP addressing is important in cybersecurity because source and destination IP information helps security professionals understand network traffic.

## 21.1 Source IP

The source IP identifies the logical source address of a packet.

## 21.2 Destination IP

The destination IP identifies where the packet is intended to go.

## 21.3 Private vs Public IP

```text
Private IP → Internal network

Public IP → Internet-routable addressing context
```

## 21.4 Network Segmentation

A network can be divided into different subnets or VLANs so that systems are placed into separate logical segments.

Example:

```text
VLAN 10 → Employees
VLAN 20 → Servers
VLAN 30 → Guest
VLAN 40 → Management
```

Subnet/VLAN segmentation can be combined with firewalls or ACLs to control traffic.

## 21.5 ACL

ACL stands for:

**Access Control List**

ACL rules can permit or deny traffic based on:

```text
Source IP
Destination IP
Protocol
Port
```

Example concept:

```text
Allow:
10.10.10.0/24 → Web Server

Deny:
Guest Network → Internal Server
```

## 21.6 IP Spoofing

In IP spoofing, an attacker can forge the source IP address of a packet.

Therefore, source IP alone should not always be trusted as proof of identity.

## 21.7 Network Scanning

During an authorized security assessment, network scanning can help identify:

```text
Live Hosts
Open Ports
Services
Network Ranges
```

> **Important:** Always perform scanning or testing only against your own systems, labs, or environments where you have explicit authorization.

---

# 22. Quick Reference Tables

## IPv4 Special Ranges

| Range           | Purpose                                                       |
| --------------- | ------------------------------------------------------------- |
| 10.0.0.0/8      | Private                                                       |
| 172.16.0.0/12   | Private                                                       |
| 192.168.0.0/16  | Private                                                       |
| 127.0.0.0/8     | Loopback                                                      |
| 169.254.0.0/16  | Link-local / APIPA                                            |
| 0.0.0.0/0       | IPv4 default route / all IPv4 destinations in routing context |
| 255.255.255.255 | Limited broadcast                                             |

## Common Subnet Masks

| CIDR | Mask            | Addresses | Traditional Usable |
| ---- | --------------- | --------: | -----------------: |
| /24  | 255.255.255.0   |       256 |                254 |
| /25  | 255.255.255.128 |       128 |                126 |
| /26  | 255.255.255.192 |        64 |                 62 |
| /27  | 255.255.255.224 |        32 |                 30 |
| /28  | 255.255.255.240 |        16 |                 14 |
| /29  | 255.255.255.248 |         8 |                  6 |
| /30  | 255.255.255.252 |         4 |                  2 |

## Useful Powers of Two

| Power |         Value |
| ----- | ------------: |
| 2^1   |             2 |
| 2^2   |             4 |
| 2^3   |             8 |
| 2^4   |            16 |
| 2^5   |            32 |
| 2^6   |            64 |
| 2^7   |           128 |
| 2^8   |           256 |
| 2^10  |         1,024 |
| 2^16  |        65,536 |
| 2^24  |    16,777,216 |
| 2^32  | 4,294,967,296 |

---

# 23. Practice Questions

## Beginner

1. How many bits are in an IPv4 address?
2. How many bits are in one octet?
3. How many octets are in `192.168.1.10`?
4. What is the subnet mask for `/24`?
5. What is the network address of `192.168.1.0/24`?
6. What is the broadcast address of `192.168.1.0/24`?
7. What is `127.0.0.1`?
8. What are the three main private IPv4 ranges?
9. What is the purpose of a default gateway?
10. What does DHCP DORA stand for?

---

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

### Problem 4 — VLSM

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

Follow this order when learning IP Addressing:

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

---

# Core Things You Should Be Able to Do

By the end, you should be able to:

```text
✓ Perform Decimal ↔ Binary conversion

✓ Explain IPv4 structure

✓ Understand CIDR

✓ Calculate subnet masks

✓ Find network addresses

✓ Find broadcast addresses

✓ Find usable host ranges

✓ Calculate subnet counts

✓ Calculate host counts

✓ Design VLSM networks

✓ Identify Private/Public IP addresses

✓ Explain the purpose of a default gateway

✓ Explain DHCP DORA

✓ Explain NAT/PAT

✓ Read and compress IPv6 addresses

✓ Understand the security impact of IP addressing
```

---

# Recommended Learning Order

```text
1.  Binary

2.  IPv4

3.  Subnet Mask

4.  CIDR

5.  Network/Broadcast

6.  Subnetting

7.  VLSM

8.  Default Gateway

9.  DHCP

10. NAT

11. IPv6

12. VLAN + Inter-VLAN Routing

13. ACL

14. Routing

15. Network Security
```
