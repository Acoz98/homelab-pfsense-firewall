# Homelab pfSense Firewall

Deployment of a dedicated **pfSense firewall/router** using a repurposed Lenovo ThinkCentre Tiny.

This project documents the process from hardware preparation through a functional pfSense deployment, including memory and storage upgrades, installation of a second Ethernet interface, pfSense installation, WAN/LAN configuration, web-based administration, and connectivity validation.

The documentation is written both as a **technical portfolio project** and as a **reproducible guide** for anyone interested in building a similar firewall.

> **Public Portfolio Note:** IP addresses, hostnames, usernames, WAN addresses, MAC addresses, and other infrastructure identifiers shown in this repository are sanitized examples and do not represent the live homelab configuration.

---

## Project Overview

The goal of this project was to replace the routing functionality of a basic consumer network with a dedicated and expandable firewall platform.

Instead of purchasing a purpose-built firewall appliance, I repurposed an existing **Lenovo ThinkCentre Tiny** and upgraded it for use as a dedicated network appliance.

The ThinkCentre was prepared with:

* 16 GB RAM
* Kingston SSD
* Built-in Ethernet interface
* Additional Intel I226-V 2.5 GbE Ethernet interface
* pfSense firewall operating system

The completed system provides the routing and firewall foundation for the rest of the homelab.

---

## Final Result

```text
                     Internet
                        │
                        ▼
                Upstream Network
                        │
                       WAN
                        │
              ┌─────────────────┐
              │                 │
              │     pfSense     │
              │   ThinkCentre   │
              │                 │
              └────────┬────────┘
                       │
                      LAN
                  10.10.10.1
                       │
                       ▼
               Internal Network
```

For public documentation, the management network is represented as:

```text
Network: 10.10.10.0/24
Gateway: 10.10.10.1
```

These addresses are sanitized examples.

---

# Why pfSense?

I wanted the homelab to use a dedicated firewall/router rather than rely entirely on a consumer wireless router.

pfSense provides a platform for learning and implementing technologies such as:

* Stateful firewalling
* Routing
* Network Address Translation
* DHCP
* DNS integration
* VLANs
* Inter-VLAN routing
* VPN services
* Traffic monitoring
* Network segmentation
* Firewall policy management

This repository focuses on the **base firewall deployment**.

More advanced network segmentation will be documented as the homelab continues to expand.

---

# Why the Lenovo ThinkCentre?

A small-form-factor business PC offered several advantages for a homelab firewall:

* x86-64 architecture
* Low power consumption
* Replaceable memory
* Replaceable storage
* Expansion capability
* USB boot support
* Compact physical footprint
* More resources than a typical consumer router
* Ability to run pfSense as a dedicated appliance

The ThinkCentre also integrates into the custom 10-inch rack built for the physical infrastructure project.

➡️ **[Homelab Physical Infrastructure](https://github.com/Acoz98/homelab-physical-infrastructure)**

---

# Hardware Configuration

The ThinkCentre was upgraded before pfSense was installed.

| Component                | Configuration               |
| ------------------------ | --------------------------- |
| Platform                 | Lenovo ThinkCentre Tiny     |
| Memory                   | 16 GB RAM                   |
| Storage                  | Kingston SSD                |
| Built-in NIC             | 1 × Ethernet                |
| Additional NIC           | Intel I226-V 2.5 GbE        |
| Additional NIC Interface | M.2 A+E Key                 |
| Operating System         | pfSense                     |
| Role                     | Dedicated Firewall / Router |

The system originally contained **8 GB of RAM**, which was upgraded to **16 GB**.

The storage device was also replaced with a **Kingston SSD** before the machine was converted into a dedicated firewall.

---

## Dual-NIC Design

A firewall needs to communicate with both the upstream and internal networks.

The ThinkCentre originally contained one Ethernet interface, so a second network interface was added.

```text
             Lenovo ThinkCentre
                     │
          ┌──────────┴──────────┐
          │                     │
          ▼                     ▼
   Intel I226-V           Built-in NIC
      2.5 GbE
          │                     │
         WAN                   LAN
          │                     │
          ▼                     ▼
    Upstream Network      Internal Network
```

For this deployment:

| Interface            | Role |
| -------------------- | ---- |
| Intel I226-V 2.5 GbE | WAN  |
| Built-in Ethernet    | LAN  |

The exact physical interface assignment may differ when reproducing this project depending on the hardware being used.

---

# Build Process

The project was divided into three stages.

## Part 1 — Hardware Preparation

The first stage prepared the Lenovo ThinkCentre for its new role as a firewall appliance.

Work completed:

* Hardware inspection
* Memory upgrade from 8 GB to 16 GB
* Kingston SSD installation
* Intel I226-V NIC installation
* Second RJ45 interface installation
* BIOS/UEFI verification
* Hardware validation

📘 **[Part 1 — Hardware Preparation](docs/01-hardware-preparation.md)**

---

## Part 2 — pfSense Installation

After the hardware was prepared, the previous operating system was removed and pfSense was installed.

The process included:

* Downloading the official pfSense installer
* Creating a bootable USB flash drive
* Booting the ThinkCentre from USB
* Running the pfSense installer
* Selecting the Kingston SSD
* Removing the previous operating system
* Installing pfSense
* Removing the installation USB
* Booting pfSense from the internal SSD
* Verifying both Ethernet interfaces

📘 **[Part 2 — pfSense Installation](docs/02-pfsense-installation.md)**

---

## Part 3 — Initial Configuration

After pfSense was installed, the firewall was configured for basic network operation.

The initial configuration included:

* Identifying physical interfaces
* Assigning WAN and LAN
* Connecting a management computer
* Accessing the pfSense webConfigurator
* Completing the Setup Wizard
* Configuring the management network
* Changing the administrator password
* Verifying interface status
* Testing gateway connectivity
* Testing Internet connectivity

📘 **[Part 3 — pfSense Initial Configuration](docs/03-pfsense-initial-configuration.md)**

---

# Deployment Workflow

The complete deployment documented in this repository follows this sequence:

```text
Existing Lenovo ThinkCentre
        │
        ▼
Hardware Inspection
        │
        ├── 8 GB → 16 GB RAM
        │
        ├── Kingston SSD
        │
        └── Intel I226-V NIC
        │
        ▼
Hardware Validation
        │
        ▼
Create pfSense USB Installer
        │
        ▼
Boot Lenovo From USB
        │
        ▼
Install pfSense to SSD
        │
        ▼
Boot From Internal Storage
        │
        ▼
Identify Network Interfaces
        │
        ▼
Assign WAN / LAN
        │
        ▼
Connect Management Client
        │
        ▼
Access webConfigurator
        │
        ▼
Complete Setup Wizard
        │
        ▼
Configure Management Network
        │
        ▼
Validate Internet Connectivity
        │
        ▼
Operational pfSense Firewall
```

---

# Build It Yourself

The exact Lenovo ThinkCentre used in this project is not required.

A similar firewall can be built using other compatible x86-64 hardware.

At minimum, you should consider:

* Compatible x86-64 computer
* Two supported Ethernet interfaces
* Internal storage
* Sufficient memory
* USB boot support
* pfSense-compatible network adapters

A general deployment process is:

```text
Choose Hardware
      ↓
Verify pfSense Compatibility
      ↓
Install Two Ethernet Interfaces
      ↓
Prepare Internal Storage
      ↓
Create Bootable USB
      ↓
Install pfSense
      ↓
Assign WAN and LAN
      ↓
Connect Management Computer
      ↓
Access Web Interface
      ↓
Complete Initial Configuration
      ↓
Validate Connectivity
```

Always verify that the network adapter chipset is supported by the version of pfSense/FreeBSD being installed.

---

# Initial Network Design

At the completion of this project, the basic network consists of:

```text
Internet
   │
   ▼
Upstream Network
   │
   ▼
pfSense WAN
   │
┌───────────────────┐
│                   │
│      pfSense      │
│ Lenovo ThinkCentre│
│                   │
└─────────┬─────────┘
          │
         LAN
     10.10.10.1
          │
          ▼
   Trusted Network
```

This configuration intentionally begins with a simple WAN/LAN design.

Additional networking components are introduced only after the base firewall is confirmed to be working.

---

# Validation

Before considering the initial firewall deployment complete, I verified:

### Hardware

* [x] Lenovo ThinkCentre boots normally
* [x] 16 GB RAM detected
* [x] Kingston SSD detected
* [x] Built-in NIC detected
* [x] Intel I226-V NIC detected

### pfSense

* [x] pfSense installed successfully
* [x] System boots from the Kingston SSD
* [x] WAN interface assigned
* [x] LAN interface assigned
* [x] webConfigurator accessible
* [x] Administrator password changed
* [x] Management network configured

### Networking

* [x] Management client receives addressing
* [x] Client can reach the pfSense gateway
* [x] WAN receives upstream connectivity
* [x] Outbound routing works
* [x] NAT works
* [x] Internet connectivity verified

---

# Security Considerations

Several security practices were followed while building and documenting the firewall.

* Default administrative credentials were changed.
* Management access is performed from the internal network.
* The pfSense webConfigurator uses HTTPS.
* WAN and LAN are separated.
* Unsolicited inbound WAN traffic is blocked by the default firewall configuration.
* Credentials are not stored in this repository.
* Public screenshots should have identifying network information sanitized.
* Real WAN addresses are never intentionally published.
* MAC addresses and hardware identifiers should be removed from public evidence when appropriate.

The pfSense management interface should not be directly exposed to the Internet.

---

# Related Projects

### Physical Infrastructure

The custom 10-inch 5U rack, equipment mounts, structured cabling, and hardware installation are documented separately:

**[Homelab Physical Infrastructure](https://github.com/Acoz98/homelab-physical-infrastructure)**

---

# Disclaimer

This project is a personal homelab and learning environment.

Hardware compatibility, interface names, network addressing, installer behavior, and pfSense configuration may differ between environments.

Always consult the current pfSense documentation before reproducing the installation.

Third-party hardware should be verified for FreeBSD/pfSense compatibility before purchase or deployment.
