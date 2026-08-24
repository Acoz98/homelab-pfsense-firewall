# Part 3 — pfSense Initial Configuration

This guide documents the initial configuration of pfSense after the operating system has been successfully installed on the Lenovo ThinkCentre.

At this stage:

* pfSense is installed on the Kingston SSD
* The USB installer has been removed
* The ThinkCentre boots directly into pfSense
* Both physical Ethernet interfaces are detected
* The system is ready for WAN and LAN configuration

If you have not completed the installation yet, start here:

**[Part 2 — pfSense Installation](02-pfsense-installation.md)**

This stage includes:

1. Identifying the physical network interfaces
2. Assigning WAN and LAN
3. Verifying the default pfSense configuration
4. Connecting a management computer
5. Accessing the pfSense webConfigurator
6. Completing the initial Setup Wizard
7. Changing the management network
8. Securing the administrator account
9. Validating Internet connectivity
10. Validating DNS resolution

> **Public Portfolio Note:** IP addresses, hostnames, usernames, WAN addresses, and other infrastructure identifiers shown throughout this repository are sanitized examples and do not represent the live homelab configuration.

---

# 1. First Boot Console

After pfSense boots from the Kingston SSD, the local console displays the detected network interfaces and interface assignments.

The console is useful for several initial administrative tasks, including:

* Assigning interfaces
* Changing interface IP addresses
* Resetting administrative access
* Testing connectivity
* Rebooting or shutting down the firewall

A typical pfSense console includes options similar to:

```text
1) Assign Interfaces
2) Set interface(s) IP address
3) Reset admin account and password
4) Reset to factory defaults
5) Reboot system
6) Halt system
7) Ping host
8) Shell
```

The exact menu may vary depending on the version of pfSense being used.

---

# 2. Identify the Physical Interfaces

The Lenovo ThinkCentre contains two physical Ethernet interfaces:

```text
Lenovo ThinkCentre
│
├── Built-in Ethernet
│
└── Intel I226-V 2.5 GbE Ethernet
```

These interfaces need to be assigned different network roles.

For this build:

```text
Intel I226-V
     │
     ▼
    WAN

Built-in Ethernet
     │
     ▼
    LAN
```

> Interface names shown by pfSense may vary depending on the hardware and driver. Do not assign interfaces based only on names such as `igc0`, `em0`, or `igb0`. Verify which physical port corresponds to each detected interface.

---

# 3. Identify Interfaces Using Link State

One reliable way to identify a physical interface is to use its Ethernet link state.

Connect an Ethernet cable to one interface and observe which interface pfSense reports as having an active link.

The process can be repeated for the second interface.

```text
Connect Cable
     │
     ▼
Observe Link State
     │
     ▼
Identify Interface
     │
     ▼
Record Physical Port
```

Document which interface corresponds to each physical Ethernet port before proceeding.

This prevents accidentally reversing WAN and LAN.

---

# 4. Assign WAN and LAN

From the pfSense console, use the interface-assignment option if WAN and LAN were not automatically assigned correctly.

The basic topology is:

```text
Internet / Upstream Network
           │
           │
          WAN
           │
           ▼
┌─────────────────────────┐
│                         │
│        pfSense          │
│   Lenovo ThinkCentre    │
│                         │
└────────────┬────────────┘
             │
            LAN
             │
             ▼
      Internal Network
```

For this build:

| Interface            | Purpose |
| -------------------- | ------- |
| Intel I226-V 2.5 GbE | WAN     |
| Built-in Ethernet    | LAN     |

After selecting the interfaces, confirm the assignments.

---

# 5. Do Not Configure VLANs Yet

During interface assignment, pfSense may ask whether VLANs should be configured.

For this stage, keep the firewall configuration simple.

Do not configure VLANs yet.

The objective is first to verify that the basic network path works:

```text
Internet
    │
    ▼
pfSense WAN
    │
    ▼
pfSense LAN
    │
    ▼
Management Computer
```

VLANs will be introduced after the managed switch is connected and the basic firewall configuration is validated.

---

# 6. Default pfSense Configuration

After installation and interface assignment, pfSense provides a functional default configuration.

Typical defaults include:

```text
WAN:
DHCP Client

LAN:
192.168.1.1/24

LAN DHCP:
Enabled

Web Interface:
HTTPS / Port 443

DNS Resolver:
Enabled
```

pfSense also allows LAN clients to initiate outbound traffic and performs IPv4 NAT for LAN traffic leaving the WAN interface.

Incoming connections arriving unsolicited from WAN are blocked by default.

This provides a safe starting point for the initial configuration.

---

# 7. Important — Check for Subnet Conflicts

Before connecting the full network, verify that the upstream WAN network does not use the same subnet as pfSense LAN.

A conflict could look like this:

```text
BAD EXAMPLE

Upstream Router:
192.168.1.1/24

pfSense LAN:
192.168.1.1/24

        ❌
Overlapping Networks
```

WAN and LAN must use different networks.

For example:

```text
GOOD EXAMPLE

WAN / Upstream:
192.0.2.0/24

LAN:
10.10.10.0/24

        ✅
Separate Networks
```

The `192.0.2.0/24` network shown here is reserved for documentation examples and does not represent the live WAN configuration.

---

# 8. Connect a Management Computer

Before introducing the managed switch, connect a trusted computer directly to the pfSense LAN interface.

```text
Internet
   │
   ▼
pfSense WAN
   │
┌─────────────────────┐
│      pfSense        │
│    ThinkCentre      │
└─────────┬───────────┘
          │
         LAN
          │
          ▼
  Management Computer
```

Keeping the topology simple makes troubleshooting much easier.

If something does not work, only three primary components need to be checked:

* pfSense
* Ethernet cable
* Management computer

---

# 9. Configure the Management Computer for DHCP

The management computer should initially be configured to obtain an IP address automatically using DHCP.

pfSense runs a DHCP server on LAN by default.

A client connected to the default LAN should receive an address similar to:

```text
pfSense LAN:
192.168.1.1

Management Computer:
192.168.1.x
```

Do not manually use `192.168.1.1` on the client because that address belongs to the firewall.

---

# 10. Verify the Client IP Address

Verify that the connected management computer received a valid LAN address.

## Windows

```powershell
ipconfig
```

## Linux

```bash
ip addr
```

## macOS

```bash
ifconfig
```

Confirm that the computer has:

* An IPv4 address in the LAN subnet
* The correct subnet mask
* pfSense as the default gateway
* DNS information

---

# 11. Test Connectivity to pfSense

Before opening the web interface, verify basic connectivity.

Using the default LAN configuration:

```bash
ping 192.168.1.1
```

Successful replies confirm that the management computer can communicate with the pfSense LAN interface.

The communication path is:

```text
Management Computer
        │
        ▼
     Ethernet
        │
        ▼
    pfSense LAN
```

If this test fails, do not continue to the web interface yet.

Troubleshoot the physical and IP configuration first.

---

# 12. Access the pfSense Web Interface

Open a web browser on the management computer.

Navigate to:

```text
https://192.168.1.1
```

pfSense runs its web-based management interface over HTTPS by default.

The browser may display a certificate warning because pfSense initially uses a locally generated certificate.

Verify that you are connecting to the expected LAN address before continuing.

The connection path is:

```text
Management Computer
        │
        │ HTTPS
        ▼
https://192.168.1.1
        │
        ▼
pfSense webConfigurator
```

---

# 13. Initial Login

The factory default pfSense credentials are:

```text
Username:
admin

Password:
pfsense
```

These credentials should only be used for the initial configuration.

> **Security Warning:** Do not leave the firewall using the default administrator password, even in a homelab.

The administrator password will be changed during the Setup Wizard.

---

# 14. Start the Setup Wizard

After logging into the pfSense webConfigurator for the first time, the Setup Wizard is presented.

The wizard provides a structured way to configure the basic firewall settings.

The wizard covers:

```text
General Information
        ↓
DNS
        ↓
Time Zone
        ↓
WAN
        ↓
LAN
        ↓
Administrator Password
        ↓
Apply Configuration
```

Click **Next** to begin the configuration process.

---

# 15. Configure the Firewall Hostname

Choose a hostname that clearly identifies the firewall.

For public documentation, an example hostname is:

```text
pfsense-fw01
```

A descriptive hostname makes the device easier to identify later when the environment contains multiple servers and network devices.

Avoid generic names such as:

```text
server
router
computer
```

when documenting larger environments.

---

# 16. Configure the Domain

The Setup Wizard also allows a domain to be configured.

For public documentation, use a non-sensitive example such as:

```text
home.arpa
```

or another documentation-safe internal naming convention.

---

# 17. Configure DNS

pfSense can operate as a DNS resolver for clients on the internal network.

The DNS Resolver is enabled by default.

At this stage, keep the DNS configuration simple.

More advanced DNS services will be introduced later when the Raspberry Pi and Pi-hole infrastructure are documented.

The objective in this stage is only to verify that DNS resolution works through the basic firewall configuration.

---

# 18. Configure the Time Zone

Select the correct time zone for the firewall.

Accurate time is important for:

* Firewall logs
* Security events
* DHCP leases
* Certificates
* VPN authentication
* Troubleshooting
* Monitoring

Incorrect timestamps can make troubleshooting significantly more difficult.

---

# 19. Configure WAN

The WAN interface connects toward the upstream network.

For this homelab deployment, WAN initially received an address using DHCP.

```text
Upstream Network
       │
       │ DHCP
       ▼
  pfSense WAN
```

For public diagrams, this repository uses sanitized example addressing:

```text
Example WAN Address:
192.0.2.10

Example Upstream Gateway:
192.0.2.1
```

These addresses do not represent the live homelab environment.

---

# 20. Private Network Options on WAN

pfSense includes WAN options related to blocking private and reserved networks.

Whether these settings should be enabled depends on the upstream environment.

For example, if pfSense WAN is temporarily connected behind another home router, the WAN interface may receive an RFC1918 private address such as:

```text
192.168.x.x
10.x.x.x
172.16.x.x - 172.31.x.x
```

In this type of lab configuration, settings that block private networks on WAN may need to be evaluated carefully.

Do not copy settings without understanding what type of network exists upstream.

---

# 21. Configure the LAN Management Network

The default pfSense LAN is:

```text
192.168.1.1/24
```

For the public version of this project, the management network is represented as:

```text
Management Network:
10.10.10.0/24

pfSense Gateway:
10.10.10.1
```

During the Setup Wizard, change the LAN address to:

```text
10.10.10.1
```

with a `/24` subnet.

This produces:

```text
Network:
10.10.10.0/24

Usable Host Range:
10.10.10.1 - 10.10.10.254

pfSense Gateway:
10.10.10.1
```

The addresses shown here are sanitized documentation examples.

---

# 22. Why Use a Dedicated Management Network?

The management network provides a trusted location for administering infrastructure devices.

Examples of devices that may eventually use this network include:

* pfSense
* Managed switch
* Administrative workstation
* Other infrastructure-management interfaces

The public network design for this homelab begins with:

```text
VLAN 10
Management

10.10.10.0/24
```

VLAN configuration itself will be introduced later.

At this stage, `10.10.10.0/24` is simply being used as the trusted LAN network.

---

# 23. Change the Administrator Password

The Setup Wizard requires the administrator password to be changed from the factory default.

Choose a strong and unique password.

Do not:

* Reuse a personal password
* Commit the password inside configuration files

---

# 24. Apply the Setup Wizard Configuration

After reviewing the settings, apply the configuration.

pfSense will reload the services affected by the changes.

If the LAN address was changed from:

```text
192.168.1.1
```

to:

```text
10.10.10.1
```

the browser connection will be interrupted.

This is expected.

The firewall is no longer using the old LAN address.

---

# 25. Renew the Management Computer Address

After changing the pfSense LAN network, the management computer must receive an address in the new subnet.

The desired result is:

```text
pfSense:
10.10.10.1

Management Client:
10.10.10.x
```

If the computer does not automatically receive a new address, renew the DHCP lease.

## Windows

```powershell
ipconfig /release
ipconfig /renew
```

On macOS or Linux, disconnecting and reconnecting the Ethernet interface may also trigger a new DHCP request.

---

# 26. Reconnect to the pfSense Web Interface

After the management computer receives an address in the new network, open:

```text
https://10.10.10.1
```

Log in using:

```text
Username:
admin
```

and the **new administrator password** created during the Setup Wizard.

The pfSense dashboard should now load.

---

# 27. Verify Interface Status

From the pfSense dashboard, verify that both WAN and LAN are operational.

Check that:

### WAN

* Interface is up
* WAN received an IPv4 address
* Gateway is available

### LAN

* Interface is up
* LAN address is `10.10.10.1/24`
* Management client is connected

The basic topology should now look like:

```text
                    Internet
                       │
                       ▼
                Upstream Network
                       │
                      WAN
                       │
             ┌──────────────────┐
             │                  │
             │     pfSense      │
             │   ThinkCentre    │
             │                  │
             └────────┬─────────┘
                      │
                     LAN
                 10.10.10.1
                      │
                      ▼
             Management Client
                10.10.10.x
```

---

# 28. Test LAN Connectivity

From the management computer:

```bash
ping 10.10.10.1
```

Successful replies confirm that the client can communicate with the firewall.

---

# 29. Test Internet Connectivity

Next, test an external IP address.

Example:

```bash
ping 1.1.1.1
```

If successful, the traffic path is:

```text
Management Computer
        │
        ▼
pfSense LAN
        │
        ▼
Firewall / NAT
        │
        ▼
pfSense WAN
        │
        ▼
Upstream Network
        │
        ▼
Internet
```

This confirms that basic routing and outbound NAT are functioning.

---

# Part 3 Complete

At this point, the ThinkCentre is operating as a functional pfSense firewall/router.

The progression so far is:

```text
PART 1 — HARDWARE

Lenovo ThinkCentre
        │
        ├── RAM Upgrade
        ├── Kingston SSD
        └── Intel I226-V NIC
        │
        ▼

PART 2 — INSTALLATION

Bootable USB
        │
        ▼
pfSense Installed
        │
        ▼
Boot From SSD
        │
        ▼

PART 3 — INITIAL CONFIGURATION

WAN / LAN Assigned
        │
        ▼
Management Client Connected
        │
        ▼
webConfigurator
        │
        ▼
Setup Wizard
        │
        ▼
10.10.10.0/24 Management Network
        │
        ▼
Internet Validation
        │
        ▼
Basic Firewall Operational
