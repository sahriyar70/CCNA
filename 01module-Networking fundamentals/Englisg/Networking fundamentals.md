# Module 01: Networking Fundamentals

## Table of Contents
1. [Internetworking Concepts](#1-internetworking-concepts)
2. [Network Components](#2-network-components)
3. [Network Types](#3-network-types)
4. [Network Topologies](#4-network-topologies)
5. [OSI and TCP/IP Models](#5-osi-and-tcpip-models)
6. [Data Encapsulation and De-encapsulation](#6-data-encapsulation-and-de-encapsulation)
7. [Network Protocols Overview](#7-network-protocols-overview)
8. [Basic Network Design Principles](#8-basic-network-design-principles)
9. [Lab Exercises & Practical Steps](#9-lab-exercises--practical-steps)

---

## 1. Internetworking Concepts

* **Internetworking:** Connects different distinct networks using devices like routers and gateways to form a unified global network.
* **Goal:** Enables seamless end-to-end communication and resource sharing across heterogeneous platforms.
* **Fundamental Principles:**
  * **Packet Switching:** Breaking data into smaller chunks (packets) for individual routing across optimal paths.
  * **Standardized Addressing:** Logical addressing (IP addresses) uniquely identifies devices worldwide.
  * **Routing Protocols:** Rules used by devices to discover paths and forward data to its destination.

---

## 2. Network Components

### Router (Layer 3 Device)
* **Function:** Connects different networks (e.g., LAN to WAN) and determines the best path for data packets using IP routing tables.
* **Key Role:** Acts as a default gateway for host devices leaving their local subnet.

### L2 & L3 Switches
* **Layer 2 Switch:** Operates at the Data Link Layer using MAC addresses to forward frames within a single broadcast domain.
* **Layer 3 Switch:** Combines fast hardware switching with IP routing capability, performing inter-VLAN routing without external routers.

### Firewall
* **Function:** Monitors and controls incoming and outgoing network traffic based on configured security policies.
* **Types:** Stateful Packet Inspection (SPI), Next-Generation Firewalls (NGFW) with deep packet inspection and application recognition.

### Access Point (AP)
* **Function:** Enables wireless devices (Wi-Fi) to connect to a wired network infrastructure.
* **Key Concept:** Converts 802.11 wireless frames into 802.3 Ethernet frames.

### Server
* **Function:** Centralized host system offering services, resources, or application data to clients (e.g., DHCP, DNS, Web, Active Directory).

---

## 3. Network Types

| Network Type | Full Form | Scale / Coverage | Primary Use Case |
| :--- | :--- | :--- | :--- |
| **LAN** | Local Area Network | Small geographical area (Office, Building, Home) | High-speed local resource sharing and communication |
| **WAN** | Wide Area Network | Large geographical area (Cities, Countries, Global) | Connecting geographically dispersed LANs (e.g., Internet) |
| **SOHO** | Small Office / Home Office | Small business or home environment | Simplified setup combining Router, Switch, AP, & Firewall into one device |
| **Cloud** | Cloud Infrastructure | Virtualized resources distributed globally over Internet | Scalable computing, storage, and software access (AWS, Azure) |

---

## 4. Network Topologies

### 2-Tier Architecture (Collapsed Core)
* **Structure:** Combines the **Core** and **Distribution** layers into a single consolidated layer, which connects directly to the **Access** layer.
* **Advantage:** Cost-effective and easier to manage for small to medium-sized networks.

### 3-Tier Architecture (Traditional Enterprise)
1. **Core Layer:** High-speed backbone designed to transport traffic fast with minimal packet processing.
2. **Distribution Layer:** Applies policy-based routing, filtering, QoS, and VLAN termination.
3. **Access Layer:** End-user device connection point (PCs, IP Phones, APs).

### Spine-Leaf Architecture (Modern Data Center)
* **Leaf Layer:** Switches connect directly to servers and storage units.
* **Spine Layer:** Switches form a high-speed core interconnecting all Leaf switches.
* **Key Benefit:** Predictable low latency, high bandwidth, and consistent East-West traffic flow (server-to-server).

---

## 5. OSI and TCP/IP Models

### OSI vs. TCP/IP Layer Mapping

| OSI Layer Number | OSI Layer Name | Primary Function / Unit | TCP/IP Layer Name |
| :---: | :--- | :--- | :--- |
| 7 | Application | User Application Interface | **Application** |
| 6 | Presentation | Data Encryption, Compression, Encoding | (Combined) |
| 5 | Session | Session Setup, Management, Termination | (Combined) |
| 4 | Transport | End-to-End Reliability (TCP) / Speed (UDP) (Segment/Datagram) | **Transport** |
| 3 | Network | Logical Addressing & Routing (Packets) | **Internet** |
| 2 | Data Link | Physical Addressing (MAC) & Error Detection (Frames) | **Network Access** |
| 1 | Physical | Transmission of Raw Bits over Cable/Air (Bits) | (Combined) |

---

## 6. Data Encapsulation and De-encapsulation Process

### Encapsulation (Sender Side)
1. **Data:** Application generates raw data.
2. **Segment:** Transport layer adds Source & Destination Ports.
3. **Packet:** Network layer adds Source & Destination IP Addresses.
4. **Frame:** Data Link layer adds Source & Destination MAC Addresses + Frame Check Sequence (FCS).
5. **Bits:** Physical layer converts frames into electrical impulses, light pulses, or radio waves.

### De-encapsulation (Receiver Side)
* The process runs in reverse from **Physical (Bits)** to **Application (Data)**, stripping away headers at each layer to read the payload.

---

## 7. Network Protocols Overview

* **IP (Internet Protocol):** Unreliable, connectionless protocol responsible for addressing and routing (IPv4, IPv6).
* **TCP (Transmission Control Protocol):** Connection-oriented, reliable protocol providing flow control, sequencing, and error checking (e.g., HTTP, HTTPS, SSH).
* **UDP (User Datagram Protocol):** Connectionless, lightweight protocol prioritized for speed over reliability (e.g., DNS, VoIP, Streaming).
* **ICMP (Internet Control Message Protocol):** Operational and diagnostic reporting tool (used by `ping` and `traceroute`).
* **ARP (Address Resolution Protocol):** Resolves a known IPv4 address into a destination Physical MAC address.

---

## 8. Basic Network Design Principles

* **Redundancy:** Eliminates single points of failure using backup hardware links and protocols (e.g., STP, FHRP).
* **Scalability:** Ensures the network architecture can expand without requiring major redesigns.
* **Security:** Implements defense-in-depth strategies via network segmentation (VLANs), firewalls, and strict access controls.
* **Manageability:** Employs centralized monitoring, logging, and automated configuration tools (SNMP, Syslog, SSH).

---

## 9. Lab Exercises & Practical Steps

### Task 1: Build a Basic LAN using Switches & PCs
1. Add a Cisco 2960 Switch and 2 Host PCs into Cisco Packet Tracer / GNS3 / EVE-NG.
2. Connect PC-A (`Fa0/1`) and PC-B (`Fa0/2`) to the Switch using Straight-Through Ethernet cables.
3. Configure Static IP addresses:
   * **PC-A:** IP `192.168.1.10 /24`
   * **PC-B:** IP `192.168.1.20 /24`

### Task 2: Identify Devices, Interfaces, and Cabling
* **Straight-Through Cable:** Used for connecting different device types (e.g., PC to Switch, Switch to Router).
* **Crossover Cable:** Used for connecting similar device types (e.g., Switch to Switch, PC to PC). *(Note: Modern Auto-MDIX eliminates strict crossover requirements)*.
* Inspect interface status via CLI commands:
  ```bash
  show interfaces status
  show ip interface brief