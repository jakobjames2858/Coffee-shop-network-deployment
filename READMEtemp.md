# Build a Coffee Shop Network in Cisco Packet Tracer

A professional, real-world network deployment simulation designed for a small business ("Mike's Coffee Shop") [0.1.3, 0.1.5]. This project separates business infrastructure from guest access to ensure security, high performance, and future scalability [0.1.3, 0.1.18].

## 🛠️ Project Scope & Technologies Learned
* **Network Planning & Topology Design:** Translating physical business spaces into a logical network design [0.1.3].
* **VLAN Segmentation:** Isolating critical networks (Management, POS, Guest Wi-Fi, Infrastructure Management) [0.1.3, 0.1.58].
* **Inter-VLAN Routing:** Using **Router-on-a-Stick** configuration with sub-interfaces [0.1.3, 0.1.109, 0.1.112].
* **Trunk & Access Port Configuration:** Restricting traffic crossing critical links [0.1.3, 0.1.85].
* **Dynamic Host Configuration Protocol (DHCP):** Creating automated IP pools with static exclusions [0.1.3, 0.1.125, 0.1.126].
* **Network Security (ACLs):** Standard Access Control Lists blocking guest access to corporate resources [0.1.3, 0.1.141].
* **Secure Device Management:** SSH remote terminal access implementation instead of insecure Telnet [0.1.3, 0.1.91, 0.1.92].

---

## 📐 Network Architecture & VLAN Design
The infrastructure separates the physical locations into four distinct broadcast domains [0.1.57, 0.1.58]:

| VLAN ID | Network Name | Purpose / Assigned Devices | IP Subnet |
| :---: | :--- | :--- | :--- |
| **10** | Management Office | Manager PC, Confidential Office Printer [0.1.10, 0.1.11] | `192.168.10.0/24` |
| **20** | Point of Sale (POS) | Register Terminal, Receipt Printer [0.1.14, 0.1.16] | `192.168.20.0/24` |
| **30** | Guest Wi-Fi | Ceiling Access Point, Wireless Laptops [0.1.19, 0.1.40] | `192.168.30.0/24` |
| **99** | Network Management | Layer 2 Switch remote management interface [0.1.63, 0.1.89] | `192.168.99.0/24` |

---

## 🚀 Key Configurations & Enhancements

### 1. Advanced Security (Access Control Lists)
An Access Control List (ACL) is applied inbound on the Guest Wi-Fi interface [0.1.148, 0.1.149]. It allows guests to obtain local IP configurations via DHCP and route to the internet while strictly **denying traffic** to corporate VLANs [0.1.141, 0.1.145]:
* Explicit **DHCP Permit** statements ensure seamless connectivity [0.1.145].
* Block rules isolate corporate management and POS registers completely [0.1.141].

### 2. Industry Best Practices Implemented
* **Static Exclusions:** The first 20 IP addresses of every subnet are reserved for critical infrastructure (gateways, printers) [0.1.126].
* **Spanning Tree Enhancements:** Enabled `portfast` on edge ports to facilitate immediate link transitions while protecting switch interconnects from switching loops [0.1.71, 0.1.72].
* **CLI Quality of Life:** Disabled DNS lookups to eliminate terminal execution delays on typos [0.1.49, 0.1.52].
* **Hardened Configurations:** Configured message-of-the-day (MOTD) compliance banners, password encryption, and absolute VTY transport restrictions to enforce SSH [0.1.53, 0.1.54, 0.1.95].

---

## 🗺️ Topology Diagram
*(Add your Packet Tracer screenshot here: Drag your screenshot into this line or save as `images/topology.png`)*
![Coffee Shop Network Topology](images/topology.png)

---

## 🧪 Verification & Testing
To validate compliance with the design requirements, the following network tests were conducted [0.1.174]:
1. **Intra-VLAN Success:** Management Office PC successfully reaches the Office Printer [0.1.176].
2. **Inter-VLAN Success:** Management Office PC routes to the Point of Sale Terminal [0.1.176, 0.1.177].
3. **ACL Security Success:** Guest Laptops receive DHCP configurations successfully but pings to Corporate Gateway subnets return a hard drop, confirming network isolation [0.1.165, 0.1.180, 0.1.181].

*(Add verification screenshots showing successful pings and blocked guest traffic here)*
![Ping Verification](images/verification.png)

---

## 🎬 Credits
This deployment was built following the instructional tutorial: **[Build a Coffee Shop Network in Cisco Packet Tracer](https://www.youtube.com/watch?v=pKnmaU1zhUI)** by the original creator [0.1.2].

---
*This repository serves as documentation of practical implementation of CCNA-level networking capabilities, network planning, and infrastructure security [0.1.3].*