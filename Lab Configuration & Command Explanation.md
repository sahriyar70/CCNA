# Cisco Router Basic Configuration

## 1. Enter Privileged Mode

```bash
Sahriyar> enable
```

**কাজ:** User EXEC mode থেকে Privileged EXEC mode-এ যায়।

```text
Sahriyar> → Sahriyar#
```

**Enable Password:** `1234`

---

## 2. Check Saved Configuration

```bash
show startup-config
```

**কাজ:** Router-এর NVRAM-এ save করা configuration দেখায়।

---

## 3. Router Hostname

```bash
hostname Sahriyar
```

**কাজ:** Router-এর নাম `Sahriyar` সেট করে।

---

## 4. Enable Password

```bash
enable password 1234
```

**কাজ:** `enable` mode-এ প্রবেশ করার সময় password চায়।

**Password:** `1234`

---

## 5. Console Password

```bash
line console 0
password 123
login
```

**কাজ:** Console port দিয়ে router-এ login করার জন্য password সেট করে।

**Password:** `123`

* `line console 0` → Console configuration
* `password 123` → Password সেট
* `login` → Password authentication চালু

---

## 6. Router Interface IP

### Interface 1

```bash
interface GigabitEthernet0/0/0
ip address 198.168.10.1 255.255.255.0
```

**কাজ:** প্রথম GigabitEthernet interface-এ IP address সেট করে।

```text
IP: 198.168.10.1
Mask: 255.255.255.0
Network: 198.168.10.0/24
```

### Interface 2

```bash
interface GigabitEthernet0/0/1
ip address 198.168.20.1 255.255.255.0
```

**কাজ:** দ্বিতীয় interface-এ IP address সেট করে।

```text
IP: 198.168.20.1
Mask: 255.255.255.0
Network: 198.168.20.0/24
```

---

## 7. Other Interfaces

```bash
interface GigabitEthernet0/0/2
no ip address
```

```bash
interface GigabitEthernet0/0/3
no ip address
```

**কাজ:** এই দুই interface-এ কোনো IP address configure করা হয়নি।

---

## 8. VTY / Telnet Password

```bash
line vty 0 4
password abc
login
```

**কাজ:** Remote access/Telnet-এর জন্য VTY line password সেট করে।

**Password:** `abc`

* `line vty 0 4` → Remote terminal lines নির্বাচন
* `password abc` → Password সেট
* `login` → Authentication চালু

---

## 9. Check Interface Status

```bash
show ip interface brief
```

**কাজ:** সব interface-এর IP address এবং status দ্রুত দেখায়।

---

## 10. Check Running Configuration

```bash
show running-config
```

**কাজ:** বর্তমানে router-এ active configuration দেখায়।

---

## 11. Save Configuration

```bash
copy running-config startup-config
```

**কাজ:** বর্তমানে থাকা Running Configuration-কে Startup Configuration হিসেবে save করে।

```text
Running Config
      ↓
copy running-config startup-config
      ↓
Startup Config
```

---

# Quick Summary

| Configuration        | Command / Value                      |
| -------------------- | ------------------------------------ |
| Hostname             | `Sahriyar`                           |
| Enable Password      | `1234`                               |
| Console Password     | `123`                                |
| VTY/Telnet Password  | `abc`                                |
| G0/0/0               | `198.168.10.1/24`                    |
| G0/0/1               | `198.168.20.1/24`                    |
| G0/0/2               | No IP                                |
| G0/0/3               | No IP                                |
| Check Interface      | `show ip interface brief`            |
| Check Running Config | `show running-config`                |
| Check Startup Config | `show startup-config`                |
| Save Config          | `copy running-config startup-config` |
