# Part 1 — Hardware Preparation

This guide documents the hardware preparation required to convert a **Lenovo ThinkCentre Tiny** into a dedicated pfSense firewall/router.

This is the first stage of the pfSense homelab project.

Before installing any software, the computer was prepared and upgraded to provide a reliable platform for routing, firewalling, VLANs, VPN services, and future network expansion.

> **Public Portfolio Note:** Hardware identifiers, serial numbers, MAC addresses, IP addresses, hostnames, usernames, and other infrastructure information shown in this repository are sanitized examples and do not represent the live homelab environment.

---

# 1. Hardware Used

The firewall platform was built using:

| Component                    | Configuration                       |
| ---------------------------- | ----------------------------------- |
| Computer                     | Lenovo ThinkCentre Tiny             |
| Memory                       | 16 GB RAM                           |
| Storage                      | Kingston 500 GB SSD                 |
| Built-in Network Interface   | 1 × Ethernet                        |
| Additional Network Interface | Intel I226-V 2.5 GbE                |
| Additional NIC Interface     | M.2 A+E Key                         |
| Final Purpose                | Dedicated pfSense Firewall / Router |

---

# 2. Why I Used a Lenovo ThinkCentre

The Lenovo ThinkCentre Tiny was repurposed instead of purchasing a dedicated firewall appliance.

Small-form-factor business computers are useful for homelab infrastructure because they provide:

* x86-64 architecture
* Low power consumption
* Replaceable memory
* Replaceable storage
* USB boot support
* Expansion options
* Compact dimensions
* Enough processing capability for firewall and routing workloads

The ThinkCentre also fits inside the custom **10-inch 5U rack** built for the homelab.

The physical rack project is documented here:

[Homelab Physical Infrastructure](https://github.com/Acoz98/homelab-physical-infrastructure)

---

# 3. Memory Upgrade

The ThinkCentre originally contained:

```text
8 GB RAM
```

The system was upgraded to:

```text
16 GB RAM
```

The additional memory provides more headroom as the firewall environment grows.

Future workloads may include:

* Additional VLANs
* VPN services
* Firewall packages
* Network monitoring
* Additional firewall policies
* Increased inter-VLAN traffic

The upgrade was not strictly required for a basic pfSense installation, but it provides additional capacity for future expansion.

---

# 4. Storage Upgrade

The original storage device was replaced with a **Kingston 500 GB SSD**.

The SSD would become the dedicated storage device for pfSense.

It provides local storage for:

* pfSense
* Configuration files
* System logs
* Packages
* Firewall services

Because the ThinkCentre would become a dedicated network appliance, the previous operating system and data were no longer required.

The SSD would later be wiped during the pfSense installation.

---

# 5. Why a Second Ethernet Interface Is Required

A basic firewall/router needs at least two network interfaces.

One interface connects toward the Internet.

The second connects toward the internal network.

```text
Internet
   │
   │
  WAN
   │
   ▼
┌─────────────────┐
│                 │
│     pfSense     │
│   ThinkCentre   │
│                 │
└────────┬────────┘
         │
        LAN
         │
         ▼
 Internal Network
```

The ThinkCentre already contained one built-in Ethernet interface.

A second Ethernet interface was therefore added.

---

# 6. Additional Network Interface

For the second Ethernet connection, I installed an **M.2 A+E Key 2.5 GbE network adapter using the Intel I226-V Ethernet controller**.

## NIC Specifications

| Specification       | Configuration                |
| ------------------- | ---------------------------- |
| Interface           | M.2 A+E Key                  |
| Ethernet Controller | Intel I226-V                 |
| Ethernet Port       | 1 × RJ45                     |
| Maximum Speed       | 2.5 GbE                      |
| Supported Speeds    | 100 Mbps / 1 Gbps / 2.5 Gbps |
| Media               | Copper Ethernet              |

### Product Used

[Amazon — Intel I226-V M.2 2.5 GbE NIC](https://www.amazon.com/dp/B0GHDJDMN2)

The exact adapter used in this project is not required.

Someone reproducing this build can use another compatible Ethernet adapter as long as it is supported by the pfSense/FreeBSD version being installed.

---

# 7. Why I Selected an Intel-Based NIC

pfSense is based on FreeBSD.

Network-interface driver compatibility is therefore an important consideration when selecting hardware.

Intel Ethernet controllers are commonly used for pfSense systems because of their FreeBSD driver support and reliability.

Before purchasing a network adapter, verify compatibility with the version of pfSense you intend to install.

Official guidance:

[pfSense Hardware Documentation](https://docs.netgate.com/pfsense/en/latest/hardware/)

---

# 8. Install the Additional NIC

Before modifying the ThinkCentre:

1. Shut down the computer.
2. Disconnect the power adapter.
3. Disconnect Ethernet and peripheral cables.
4. Open the ThinkCentre chassis.
5. Locate the compatible M.2 slot.
6. Verify that the slot supports the required interface.
7. Install the M.2 Ethernet adapter.
8. Secure the adapter.
9. Route the Ethernet connection carefully through the chassis.
10. Install the external RJ45 interface.
11. Inspect the cable routing.
12. Reassemble the computer.

After installation, the ThinkCentre now had two physical Ethernet interfaces.

```text
Lenovo ThinkCentre
│
├── Built-in Ethernet
│
└── Intel I226-V 2.5 GbE Ethernet
```

---

# 9. Hardware Evidence

The ThinkCentre was opened to inspect and upgrade the internal hardware.

![Lenovo ThinkCentre internal hardware](../images/01-thinkcentre-internal-hardware.jpg)

*Internal view of the Lenovo ThinkCentre during hardware preparation.*

The system now provides two physical Ethernet interfaces.

![Lenovo ThinkCentre network interfaces](../images/02-thinkcentre-network-ports.jpg)

*Network interfaces available after adding the second Ethernet connection.*

---

# 10. Hardware Validation

Before installing pfSense, verify that the upgraded computer is functioning correctly.

Check:

* [ ] ThinkCentre powers on
* [ ] BIOS/UEFI can be accessed
* [ ] 16 GB RAM is detected
* [ ] Kingston 500 GB SSD is detected
* [ ] Built-in Ethernet interface is detected
* [ ] Intel I226-V interface is detected
* [ ] Additional NIC is physically secure
* [ ] USB ports are functional
* [ ] USB boot is available
* [ ] Cooling fan is functioning
* [ ] Ventilation is unobstructed

Do not proceed with the pfSense installation until the required hardware is working correctly.

---

# Hardware Preparation Complete

At this point, the Lenovo ThinkCentre has been transformed into a suitable dual-interface firewall platform.

```text
Lenovo ThinkCentre
        │
        ├── 16 GB RAM
        │
        ├── Kingston 500 GB SSD
        │
        ├── Built-in Ethernet
        │
        └── Intel I226-V Ethernet
        │
        ▼
Hardware Ready
        │
        ▼
pfSense Installation
```

The next step is preparing the pfSense installation media and installing the firewall operating system.

Continue to:

**[Part 2 — pfSense Installation](02-pfsense-installation.md)**
