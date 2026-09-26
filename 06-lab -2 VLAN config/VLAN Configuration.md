# VLAN Configuration Lab — Faculty, Student & Guest Network

## ১. Lab Overview

এই Lab-এ একটি ছোট প্রতিষ্ঠানের Network তৈরি করা হয়েছে যেখানে বিভিন্ন ধরনের User-কে
আলাদা VLAN-এর মাধ্যমে ভাগ করা হয়েছে।

এই Lab-এ মোট ৩টি VLAN ব্যবহার করা হয়েছে:

| VLAN | নাম | Network |  
|---|---|---|
| VLAN 10 | Faculty | 192.168.10.0/24 |
| VLAN 20 | Student | 192.168.20.0/24 |
| VLAN 30 | Guest | 192.168.30.0/24 |

### VLAN-এর উদ্দেশ্য

- **VLAN 10 → Faculty**
- **VLAN 20 → Student**
- **VLAN 30 → Guest**

প্রতিটি VLAN-এর জন্য আলাদা IP Network ব্যবহার করা হয়েছে।

---

# ২. Lab Topology

এই Lab-এ ব্যবহার করা হয়েছে:

- 3 × Cisco 2960-24TT Switch
- Faculty-এর জন্য একাধিক Laptop
- Student-এর জন্য একাধিক Laptop
- Guest-এর জন্য 2টি Laptop
- Switchগুলোর মধ্যে Trunk Link

Topology:

```text
                    ┌─────────────────┐
                    │    Switch 1     │
                    │   2960-24TT     │
                    └───────┬─────────┘
                            │
                     TRUNK │ TRUNK
                            │
              ┌─────────────┴─────────────┐
              │                           │
       ┌──────┴──────┐             ┌──────┴──────┐
       │  Switch 2   │             │  Switch 3   │
       │ 2960-24TT   │             │ 2960-24TT   │
       └─────────────┘             └─────────────┘