# Networking Subnetting & Routing Lab

Hands-on networking lab covering subnetting, routing, IP addressing, DNS, default gateway configuration, and connectivity troubleshooting.

## Project Overview

This project documents a virtual networking lab built with imported virtual machines. The goal was to inspect network interfaces, identify addressing problems, correct Windows client configuration, verify Internet and DNS connectivity, and review subnetting and routing concepts.

This repository is intended to serve as both a portfolio project and a future study reference.

## Skills Demonstrated

- IPv4 addressing
- CIDR (Classless Inter-Domain Routing) notation
- Subnet masks and subnetting
- Network and broadcast address identification
- Default gateway configuration
- DNS (Domain Name System) configuration
- Linux network interface inspection with `ip a`
- Windows network troubleshooting with `ipconfig`
- Connectivity testing with `ping`
- Basic routing and DMZ (Demilitarized Zone) network planning

## Lab Topology

The lab contains three main systems:

- **Router VM**
- **Linux Server VM**
- **Windows Client VM**

### Router Interfaces

The router was inspected using:

```bash
ip a
```

Observed interfaces:

| Interface | IP Address | CIDR | Network |
|---|---:|---:|---:|
| enp0s3 | 192.168.10.65 | /26 | 192.168.10.64/26 |
| enp0s8 | 10.0.0.1 | /28 | 10.0.0.0/28 |
| enp0s9 | 10.0.4.15 | /24 | 10.0.4.0/24 |

The `enp0s3` interface connects to the Windows client network, while `enp0s8` is on the same segment as the server.

## Server Configuration

The server was also inspected using:

```bash
ip a
```

Observed server configuration:

- **Interface:** `enp0s3`
- **IP address:** `10.0.0.3/28`
- **Network:** `10.0.0.0/28`
- **Broadcast:** `10.0.0.15`

The router interface `10.0.0.1/28` and the server `10.0.0.3/28` are on the same network segment.

## Windows Client Troubleshooting

The Windows client initially had incorrect IPv4 settings.

### Original Configuration

- IP address: `192.168.10.35`
- Subnet mask: `255.255.255.0`
- Default gateway: `192.168.10.1`
- Preferred DNS: `208.67.222.222`
- Alternate DNS: `208.67.220.220`

The router interface connected to this segment was `192.168.10.65/26`, so the client was not correctly configured for the same subnet.

### Three Required Changes

The client configuration was corrected to:

- **IP address:** `192.168.10.66`
- **Subnet mask:** `255.255.255.192`
- **Default gateway:** `192.168.10.65`

The DNS settings were kept unchanged:

- Preferred DNS: `208.67.222.222`
- Alternate DNS: `208.67.220.220`

## Why /26 Was Required

A `/26` prefix corresponds to:

```text
255.255.255.192
```

The subnet containing the router interface `192.168.10.65/26` is:

```text
Network:   192.168.10.64/26
Hosts:     192.168.10.65 - 192.168.10.126
Broadcast: 192.168.10.127
```

Therefore, `192.168.10.66` is a valid client address on the same subnet as the router.

## Connectivity Verification

After correcting the Windows client settings, the following tests succeeded:

### Website Test

The client successfully opened:

```text
https://www.herzing.ca
```

### DNS and Ping Test

The following command was used:

```cmd
ping www.cisco.com
```

Result:

- Packets sent: **4**
- Packets received: **4**
- Packets lost: **0**
- Packet loss: **0%**

This confirmed that Internet connectivity and DNS resolution were working correctly.

## Adding Another Server to the DMZ Segment

The existing server network is:

```text
10.0.0.0/28
```

Important addresses:

- Network address: `10.0.0.0`
- Router / default gateway: `10.0.0.1`
- Existing server: `10.0.0.3`
- Broadcast address: `10.0.0.15`

A possible IP address for another server is:

```text
10.0.0.2
```

assuming that address is not already in use.

The `/28` subnet mask in full notation is:

```text
255.255.255.240
```

The default gateway for the new server would be:

```text
10.0.0.1
```

## Subnetting Review

### Useful Formulas

**Usable hosts:**

```text
2^(host bits) - 2
```

**Host bits:**

```text
32 - CIDR prefix
```

**Block size:**

```text
256 - interesting mask octet
```

### Quick Reference

| CIDR | Subnet Mask | Block Size | Usable Hosts |
|---:|---|---:|---:|
| /25 | 255.255.255.128 | 128 | 126 |
| /26 | 255.255.255.192 | 64 | 62 |
| /27 | 255.255.255.224 | 32 | 30 |
| /28 | 255.255.255.240 | 16 | 14 |
| /29 | 255.255.255.248 | 8 | 6 |
| /30 | 255.255.255.252 | 4 | 2 |

### /20 Example

A `/20` subnet mask is:

```text
255.255.240.0
```

Binary form:

```text
11111111.11111111.11110000.00000000
```

## Key Lessons Learned

- Devices must share the correct subnet to communicate directly.
- A correct IP address alone is not enough; the subnet mask must also match the network design.
- The default gateway must point to the router interface on the local subnet.
- DNS problems and routing problems can look similar, so testing by IP and by hostname helps isolate the issue.
- `ip a`, `ipconfig`, and `ping` are simple but powerful troubleshooting tools.
- Understanding CIDR notation makes subnetting, host ranges, and broadcast calculations much easier.

## Screenshots

A `screenshots` section will be added to document:

- Router `ip a`
- Server `ip a`
- Original Windows client network configuration
- Corrected IPv4 configuration
- Successful Herzing College website test
- Successful `ping www.cisco.com` test

---

## العربية — ملخص المشروع

هذا المشروع يوثق لاب عملي في الشبكات يشمل **Subnetting، Routing، IP Addressing، DNS، Default Gateway، واستكشاف مشاكل الاتصال**.

أهم ما تم تطبيقه:

- فحص واجهات الراوتر والسيرفر باستخدام `ip a`
- اكتشاف أن إعدادات Windows Client كانت على شبكة غير صحيحة
- تعديل عنوان IP والـSubnet Mask والـDefault Gateway
- التحقق من الوصول إلى الإنترنت ونجاح DNS
- استخدام `ping www.cisco.com` لإثبات الاتصال
- مراجعة شبكة `/28` الخاصة بالسيرفر وكيفية إضافة Server جديد عليها
- مراجعة قواعد الـSubnetting المهمة لاستخدامها كمرجع مستقبلي
