# Cisco Switch: STP, CDP & LLDP Guide

## 1. STP (Spanning Tree Protocol)

**STP** হলো একটি Layer 2 protocol, যা সুইচের মধ্যে redundant connection থেকে তৈরি হওয়া switching loop প্রতিরোধ করে।

### STP Port States

| State      | কাজ                                              |
| ---------- | ------------------------------------------------ |
| Blocking   | সাধারণ ডেটা ফরওয়ার্ড করে না; BPDU পর্যবেক্ষণ করে |
| Listening  | BPDU প্রসেস করে এবং লুপ প্রতিরোধে কাজ করে        |
| Learning   | MAC Address Table শেখে                           |
| Forwarding | স্বাভাবিক ডেটা ফরওয়ার্ড করে                      |
| Disabled   | পোর্ট STP-তে অংশ নেয় না                          |

**Note:** উপরের পাঁচটি state হলো Legacy STP-এর state। RSTP-তে `Discarding`, `Learning` ও `Forwarding` ব্যবহৃত হয়।

### STP Commands

**সব VLAN-এর STP তথ্য দেখতে:**

```text
Switch# show spanning-tree
```

**নির্দিষ্ট VLAN-এর STP দেখতে:**

```text
Switch# show spanning-tree vlan 1
```

**কীভাবে বুঝবে:**

* `FWD` — Forwarding state
* `BLK` — Blocking state
* `Root ID` — Root Bridge-এর তথ্য
* `Bridge ID` — স্থানীয় সুইচের Bridge তথ্য

---

## 2. CDP (Cisco Discovery Protocol)

**CDP** হলো Cisco-এর proprietary Layer 2 protocol। এটি সরাসরি সংযুক্ত প্রতিবেশী ডিভাইসের তথ্য শনাক্ত করতে সাহায্য করে।

### CDP থেকে যে তথ্য পাওয়া যায়

* Device ID / Hostname
* Local Interface ও Remote Port
* Device Platform
* IP Address
* Device Capabilities

### CDP Commands

**CDP চালু করতে:**

```text
Switch(config)# cdp run
```

**প্রতিবেশী ডিভাইস দেখতে:**

```text
Switch# show cdp neighbors
```

**বিস্তারিত তথ্য দেখতে:**

```text
Switch# show cdp neighbors detail
```

**নির্দিষ্ট Interface-এ CDP বন্ধ করতে:**

```text
Switch(config)# interface gigabitEthernet 0/1
Switch(config-if)# no cdp enable
```

**Note:** CDP সাধারণত Cisco ডিভাইসে ডিফল্টভাবে চালু থাকে, তবে কনফিগারেশন অনুযায়ী ভিন্ন হতে পারে।

---

## 3. LLDP (Link Layer Discovery Protocol)

**LLDP** হলো IEEE 802.1AB-ভিত্তিক একটি vendor-neutral Layer 2 discovery protocol। এটি বিভিন্ন নির্মাতার নেটওয়ার্ক ডিভাইসের প্রতিবেশী তথ্য শনাক্ত করতে সাহায্য করে।

### LLDP Commands

**LLDP গ্লোবালি চালু করতে:**

```text
Switch(config)# lldp run
```

**প্রতিবেশী ডিভাইস দেখতে:**

```text
Switch# show lldp neighbors
```

**বিস্তারিত তথ্য দেখতে:**

```text
Switch# show lldp neighbors detail
```

**নির্দিষ্ট Interface-এ LLDP বন্ধ করতে:**

```text
Switch(config)# interface gigabitEthernet 0/1
Switch(config-if)# no lldp transmit
Switch(config-if)# no lldp receive
```

* `no lldp transmit` — LLDP বিজ্ঞপ্তি পাঠানো বন্ধ করে।
* `no lldp receive` — LLDP বিজ্ঞপ্তি গ্রহণ বন্ধ করে।

**Note:** LLDP সাধারণত ডিফল্টভাবে বন্ধ থাকে, তবে ডিভাইস ও কনফিগারেশন অনুযায়ী ভিন্ন হতে পারে।

---

## 4. CDP vs LLDP

| বিষয়          | CDP                      | LLDP                          |
| ------------- | ------------------------ | ----------------------------- |
| পূর্ণরূপ      | Cisco Discovery Protocol | Link Layer Discovery Protocol |
| ধরন           | Proprietary              | Open Standard                 |
| Standard      | Cisco-specific           | IEEE 802.1AB                  |
| Compatibility | প্রধানত Cisco ডিভাইস     | বিভিন্ন নির্মাতার ডিভাইস      |
| কাজ           | Neighbor Discovery       | Neighbor Discovery            |

---

## 5. Quick Verification Commands

| Command                      | Purpose                 |
| ---------------------------- | ----------------------- |
| `show spanning-tree`         | STP-এর অবস্থা দেখা      |
| `show spanning-tree vlan 1`  | VLAN 1-এর STP দেখা      |
| `show cdp neighbors`         | CDP প্রতিবেশী দেখা      |
| `show cdp neighbors detail`  | CDP-এর বিস্তারিত তথ্য   |
| `show lldp neighbors`        | LLDP প্রতিবেশী দেখা     |
| `show lldp neighbors detail` | LLDP-এর বিস্তারিত তথ্য  |
| `show running-config`        | বর্তমান কনফিগারেশন দেখা |

**Important:** সব কমান্ড প্রতিটি Cisco মডেল বা IOS সংস্করণে সমর্থিত নাও হতে পারে। ল্যাব বা নেটওয়ার্কে পরিবর্তন করার আগে বর্তমান কনফিগারেশন যাচাই করো।
