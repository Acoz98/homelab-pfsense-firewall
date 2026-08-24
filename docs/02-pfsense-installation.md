# Part 2 — pfSense Installation

This guide documents the software installation process used to convert the prepared **Lenovo ThinkCentre Tiny** into a dedicated pfSense firewall/router.

This guide begins after the hardware preparation is complete.

If you have not prepared the hardware yet, start here:

**[Part 1 — Hardware Preparation](01-hardware-preparation.md)**

The installation process includes:

1. Downloading the official pfSense installer
2. Preparing a bootable USB flash drive
3. Booting the Lenovo ThinkCentre from USB
4. Connecting the installer to the network
5. Installing pfSense onto the Kingston SSD 

> **Public Portfolio Note:** IP addresses, hostnames, usernames, WAN addresses, and other infrastructure identifiers shown in this repository are sanitized examples and do not represent the live homelab configuration.

---

# 1. Prerequisites

Before beginning the software installation, verify that the hardware preparation is complete.

For this build, the Lenovo ThinkCentre was prepared with:

* 16 GB RAM
* Kingston SSD
* Built-in Ethernet interface
* Intel I226-V 2.5 GbE Ethernet interface
* Working USB ports
* Keyboard
* Monitor
* Internet connection

You will also need:

* USB flash drive
* Separate computer for downloading the installer
* Ethernet cables
* Access to the Lenovo BIOS/UEFI or boot menu

At this point, the system should look conceptually like:

```text
Lenovo ThinkCentre
        │
        ├── 16 GB RAM
        ├── Kingston SSD
        ├── Built-in Ethernet
        └── Intel I226-V Ethernet
        │
        ▼
Hardware Ready
        │
        ▼
pfSense Installation
```

---

# 2. Download the pfSense Installer

Download the pfSense installation media from the official Netgate source.

Official documentation:

[Download pfSense Installation Media](https://docs.netgate.com/pfsense/en/latest/install/download-installer-image.html)

For most third-party 64-bit Intel or AMD computers being installed from a USB flash drive, select:

```text
AMD64 Memstick USB
```

This is the appropriate installer type for a system such as the Lenovo ThinkCentre used in this project.

> Always download firewall installation media from the official source. Avoid third-party downloads or unofficial images.

---

# 3. Current Netgate Installer Behavior

The current Netgate Installer is an **online installer**.

The USB image contains the installation environment, but the pfSense software packages are downloaded from Netgate during installation.

This means the ThinkCentre requires working network connectivity while the installation is being performed.

Conceptually:

```text
Bootable USB
     │
     ▼
Netgate Installer
     │
     ▼
Internet Connection
     │
     ▼
Download pfSense Packages
     │
     ▼
Install to Kingston SSD
```

Because installer behavior can change over time, consult the current official Netgate documentation before beginning a new installation.

---

# 4. Create the Bootable USB Installer

The pfSense image must be written to the USB flash drive.

This does **not** mean copying the downloaded file onto the USB as a normal file.

The installer image must be written to the USB using disk-imaging software.

Common options include:

* balenaEtcher
* Rufus
* Another raw disk-image writing utility

The basic process is:

```text
Download pfSense Image
        │
        ▼
Insert USB Flash Drive
        │
        ▼
Open Imaging Utility
        │
        ▼
Select pfSense Image
        │
        ▼
Select USB Drive
        │
        ▼
Write Image
        │
        ▼
Bootable pfSense USB
```

> **Warning:** Writing the pfSense image to the USB drive will erase the existing contents of that USB drive.

Make sure the correct drive is selected before starting the imaging process.

---

# 5. Insert the USB Into the ThinkCentre

Once the bootable USB has been created:

1. Shut down the Lenovo ThinkCentre.
2. Insert the pfSense USB installer.
3. Connect the monitor.
4. Connect the keyboard.
5. Connect the appropriate Ethernet cable.
6. Power on the ThinkCentre.

The next objective is to boot from the USB instead of the internal Kingston SSD.

---

# 6. Open the Boot Menu

During startup, enter the Lenovo boot menu or BIOS/UEFI configuration.

The exact key can vary depending on the ThinkCentre model.

From the boot-device list, select the USB flash drive containing the pfSense installer.

The boot sequence becomes:

```text
Power On
   │
   ▼
Lenovo BIOS / UEFI
   │
   ▼
Boot Menu
   │
   ▼
USB Flash Drive
   │
   ▼
Netgate Installer
```

If the USB installer does not appear:

* Verify that the USB was imaged correctly
* Try another USB port
* Check the BIOS/UEFI boot order
* Verify USB boot is enabled
* Recreate the installer if necessary

---

# 7. Start the pfSense Installer

After successfully booting from the USB drive, the Netgate Installer should launch.

Follow the installation prompts displayed on the local monitor.

The installer will detect the available network interfaces.

On a third-party system such as the ThinkCentre, the interfaces must be identified correctly.

The system should detect both:

```text
Built-in Ethernet Interface

Intel I226-V 2.5 GbE Interface
```

Do not continue until the required Ethernet interfaces are detected.

---

# 8. Configure the Installer Network Connection

The current Netgate Installer requires Internet access to retrieve the pfSense installation packages.

The installer will ask which network interface should be used as the WAN connection.

The **WAN interface** should be connected toward an upstream network with Internet access.

Conceptually:

```text
Internet
   │
   ▼
Upstream Router / Gateway
   │
   ▼
ThinkCentre WAN Interface
   │
   ▼
Netgate Installer
```

For most basic environments, WAN can obtain an address automatically using DHCP.

---

# 9. Configure the LAN Interface

The installer may also allow the LAN interface to be configured.

A fresh pfSense configuration commonly uses:

```text
LAN Address:
192.168.1.1/24

DHCP Range:
192.168.1.100
through
192.168.1.150
```

These are pfSense defaults and are shown here so someone following the guide understands what they may see during installation.

The management network used later in this public repository is represented using sanitized addressing:

```text
Management Network:
10.10.10.0/24

pfSense Gateway:
10.10.10.1
```

The `10.10.10.0/24` network is a documentation example and does not represent the live homelab configuration.

---

# 10. Avoid WAN and LAN Subnet Conflicts

The WAN and LAN networks must not overlap.

For example, this would create a conflict:

```text
BAD EXAMPLE

Upstream Network:
192.168.1.0/24

pfSense LAN:
192.168.1.0/24

        ❌
Same Network
```

Instead, WAN and LAN should use different networks.

Example using sanitized/documentation addressing:

```text
GOOD EXAMPLE

WAN / Upstream:
192.0.2.0/24

pfSense LAN:
10.10.10.0/24

        ✅
Separate Networks
```

The `192.0.2.0/24` range shown throughout this repository is used for documentation examples and does not represent the live WAN configuration.

---

# 11. Install pfSense to the Kingston SSD

Once the installer has working network connectivity, proceed with the pfSense installation.

Select the **Kingston SSD** as the target installation drive.

> **Warning:** Installing pfSense onto the SSD will erase the operating system, partitions, and data currently stored on the selected disk.

For this project, the ThinkCentre was being converted into a dedicated firewall appliance, so the previous operating system was intentionally removed.

The installation process becomes:

```text
USB Installer
      │
      ▼
Netgate Installer
      │
      ▼
Download pfSense Packages
      │
      ▼
Kingston SSD
      │
      ▼
pfSense Installed
```

Follow the installation prompts until the process completes.

---

# 12. Remove the USB Installer

After pfSense finishes installing:

1. Allow the installer to complete.
2. Reboot the ThinkCentre when instructed.
3. Remove the USB flash drive.

Removing the USB ensures that the computer boots from the Kingston SSD instead of launching the installer again.

The boot process should now be:

```text
Power On
   │
   ▼
Kingston SSD
   │
   ▼
pfSense
```

---
# Part 2 Complete

At this point:

- pfSense is installed on the Kingston SSD
- The USB installer has been removed
- The ThinkCentre boots directly into pfSense
- Both physical Ethernet interfaces are detected
- The system is ready for WAN and LAN configuration

Continue to:

**[Part 3 — pfSense Initial Configuration](03-pfsense-initial-configuration.md)**
