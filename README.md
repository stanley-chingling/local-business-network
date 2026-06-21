# Local Business Network Simulation

A comprehensive small-to-medium enterprise (SME) network built and documented in **Cisco Packet Tracer 9.0**. This project demonstrates router configuration, switch management, IP subnetting, wireless networking, and server service deployment — with full troubleshooting documentation.

---

## Project Overview

| Metric | Value |
|--------|-------|
| **Total Devices** | 12 |
| **Network Segment** | 192.168.1.0 /24 |
| **Router Platform** | Cisco 2911 |
| **Switch Platforms** | Cisco 2960 (×2) |
| **Wireless Standard** | 802.11n (Access Point-PT) |
| **Server Services** | HTTP, DHCP, DNS, FTP |
| **Connectivity Test Result** | 100% (0% packet loss) |

---

## Network Topology

```
                    [Router: 192.168.1.1]
                           |
                      [Switch0]
              |    |    |    |    |    |
            PC0  PC1  PC2   AP  Printer Switch1
                                         |
                                      [Server]
                                         |
                                    [Laptop0-3]
                                    (Wireless)
```

**Design Rationale:**
- Hierarchical star topology with router as gateway
- Primary switch for distribution, secondary switch for port expansion
- Wireless access point for mobile device connectivity
- Server isolated on secondary switch for organizational clarity

---

## Device Inventory & IP Addressing

| Device | Type | IP Address | Connection | Notes |
|--------|------|------------|------------|-------|
| Router0 | Cisco 2911 | 192.168.1.1 | Wired (Fa0/0) | Default gateway, DHCP server |
| Switch0 | Cisco 2960 | — | Wired | Primary distribution switch |
| Switch1 | Cisco 2960 | — | Wired (Trunk) | Secondary switch, daisy-chained |
| Server0 | Server-PT | 192.168.1.10 | Wired (Switch1) | HTTP, DHCP, DNS, FTP services |
| Printer0 | Printer-PT | 192.168.1.20 | Wired (Switch0) | Network-shared printer |
| PC0 | PC-PT | 192.168.1.101 | Wired (Switch0) | Static assignment |
| PC1 | PC-PT | 192.168.1.102 | Wired (Switch0) | Static assignment |
| PC2 | PC-PT | 192.168.1.103 | Wired (Switch0) | Static assignment |
| Laptop0 | Laptop-PT | 192.168.1.104 | Wireless (AP) | DHCP or static |
| Laptop1 | Laptop-PT | 192.168.1.105 | Wireless (AP) | DHCP or static |
| Laptop2 | Laptop-PT | 192.168.1.106 | Wireless (AP) | DHCP or static |
| Laptop3 | Laptop-PT | 192.168.1.107 | Wireless (AP) | DHCP or static |

---

## Router Configuration

### Cisco IOS Commands

```cisco
Router> enable
Router# configure terminal
Router(config)# hostname Business-Router
Business-Router(config)# interface GigabitEthernet0/0
Business-Router(config-if)# ip address 192.168.1.1 255.255.255.0
Business-Router(config-if)# no shutdown
Business-Router(config-if)# exit
Business-Router(config)# exit
Business-Router# copy running-config startup-config
```

### Verification

```cisco
Business-Router# show ip interface brief

Interface              IP-Address      OK? Method Status                Protocol
GigabitEthernet0/0     192.168.1.1     YES manual up                    up
```

---

## Switch Configuration

### Interconnection

Switch1 was connected to Switch0 via FastEthernet0/24 on both ends using a **Copper Cross-Over** cable, creating an extended broadcast domain.

```
Switch0 Fa0/24  <--->  Switch1 Fa0/24
Cable: Copper Cross-Over
Status: Green (up/up)
```

### Port Allocation (Switch0)

| Port | Device |
|------|--------|
| Fa0/1 | Router (GigabitEthernet0/0) |
| Fa0/2 | PC0 |
| Fa0/3 | PC1 |
| Fa0/4 | PC2 |
| Fa0/5 | Access Point |
| Fa0/6 | Printer |
| Fa0/24 | Switch1 (Trunk) |

> **Note:** Port exhaustion on Switch0 (all FastEthernet ports utilized) necessitated the addition of Switch1 — a realistic scenario in growing business networks.

---

## Server Services

| Service | Status | Configuration | Purpose |
|---------|--------|---------------|---------|
| HTTP | ✅ Enabled | Default web page on port 80 | Internal company website |
| DHCP | ✅ Enabled | Pool: 192.168.1.100 – 192.168.1.200 | Auto-assign IPs to endpoints |
| DNS | ✅ Enabled | A record: www.localbusiness.co.za → 192.168.1.10 | Internal domain resolution |
| FTP | ✅ Enabled | Authentication: Username/Password | File sharing and transfer |

---

## Wireless Network

### Access Point Configuration

| Parameter | Value |
|-----------|-------|
| SSID | BusinessWiFi |
| Channel | 1 |
| Security | WPA2-Personal |
| Authentication | PSK (Pre-Shared Key) |
| Status | Active |

### Wireless Clients

Four Laptop-PT devices connected wirelessly, each receiving an IP within the 192.168.1.0/24 subnet.

---

## Connectivity Testing

### Ping Results (from PC0)

```
C:\> ping 192.168.1.1
Reply from 192.168.1.1: bytes=32 time=3ms TTL=255
Reply from 192.168.1.1: bytes=32 time=5ms TTL=255
Reply from 192.168.1.1: bytes=32 time<1ms TTL=255
Reply from 192.168.1.1: bytes=32 time=82ms TTL=255
Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
Average = 22ms

C:\> ping 192.168.1.10
Reply from 192.168.1.10: bytes=32 time=21ms TTL=128
Reply from 192.168.1.10: bytes=32 time=3ms TTL=128
Reply from 192.168.1.10: bytes=32 time=17ms TTL=128
Reply from 192.168.1.10: bytes=32 time<1ms TTL=128
Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
Average = 10ms
```

### Test Matrix

| Source | Destination | Result |
|--------|-------------|--------|
| PC0 | Router (192.168.1.1) | ✅ Success — 0% loss |
| PC0 | Server (192.168.1.10) | ✅ Success — 0% loss |
| PC0 | Printer (192.168.1.20) | ✅ Success — 0% loss |
| PC0 | Laptop0 (192.168.1.104) | ✅ Success — 0% loss |
| Laptop0 | Server (192.168.1.10) | ✅ Success — 0% loss |
| Laptop0 | Router (192.168.1.1) | ✅ Success — 0% loss |

---

## Troubleshooting Log

| Issue | Cause | Resolution |
|-------|-------|------------|
| **Printer disconnected — "No Available Ports"** | Switch0 port exhaustion (all FastEthernet ports utilized) | Added secondary Cisco 2960 switch (Switch1) and daisy-chained via Fa0/24 trunk port |
| **Smartphone wireless connection failed** | Smartphone-PT device lacks PC Wireless utility; Packet Tracer mobile device limitation | Replaced with additional Laptop-PT devices for wireless testing; smartphone removed from topology |
| **Initial router interface down** | GigabitEthernet0/0 administratively shutdown by default | Executed `no shutdown` command in interface configuration mode |

---

## Skills Demonstrated

- ✅ **Cisco IOS CLI:** Router hostname configuration, interface IP addressing, administrative state management
- ✅ **Switch Management:** Port allocation, daisy-chaining, trunk connections, port exhaustion troubleshooting
- ✅ **IP Subnetting:** Design and implementation of a /24 network with structured host addressing
- ✅ **DHCP Service:** Configuration of address pools, default gateways, and DNS integration
- ✅ **DNS Service:** A-record creation for internal domain resolution
- ✅ **Wireless Networking:** Access Point deployment, SSID configuration, WPA2 security, client association
- ✅ **Network Troubleshooting:** Systematic diagnosis using ping, visual inspection, and port analysis
- ✅ **Technical Documentation:** Comprehensive documentation with topology diagrams, configuration logs, and test results

---

## Project Files

| File | Description |
|------|-------------|
| `Local Business Network.pkt` | Cisco Packet Tracer 9.0 project file |
| `Local Business Network Documentation.pdf` | Full technical documentation |
| `README.md` | This file |

---

## Author

**Mosala Stanley Kgadimonyane**
- Junior IT Support & Helpdesk Specialist
- Google IT Support Professional Certificate
- AZ-900 (In Progress)
- 📧 mosalakgadimonyane@gmail.com
- 🔗 [LinkedIn](https://linkedin.com/in/mosala-stanley-kgadimonyane-8663173ab)
- 💻 [GitHub](https://github.com/stanley-chingling)

---

## License

This project is shared for educational and portfolio purposes.
