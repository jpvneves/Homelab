# Homelab

## Project Overview

**Goal:** Build a homelab environment for testing and studying cybersecurity.
**Skill Level:** Experienced with virtual machines and Ubuntu.

---

## System Specifications

| Component | Specification                    |
| --------- | -------------------------------- |
| Laptop    | Samsung Galaxy Book3 Pro 360 16” |
| CPU       | Intel Core i7-1360P, 12 cores    |
| GPU       | Intel Iris Xe Graphics           |
| RAM       | 16 GB                            |
| SSD       | 512 GB                           |

---

## Initial Plan

I initially planned to create a full homelab with multiple Linux and Windows virtual machines. However, I encountered hardware limitations, particularly insufficient RAM and cooling capacity, which required me to revise the plan.

### Hypervisor Configuration

* **VirtualBox + Extension Pack**
* **VM Template Configuration:**

  * Chipset: Q35 (unavailable, switched to PIIX3)
  * Firmware: UEFI (caused crashes, requires further testing)
  * TPM 2.0: Windows only (deferred)
  * Secure Boot: Off
  * Graphics: VMSVGA
  * Video RAM: 128 MB (later reduced)
  * Disk: VDI, dynamically allocated

### Virtual Machines

* Arch Linux (KDE Plasma too resource-intensive)
* Kali Linux
* Ubuntu Server
* Windows 11 (deferred)
* Windows Server (deferred)

### Networking

* NAT Network
* Host-Only Network
* Internal Network

---

## Revised Plan

After reassessing hardware constraints, the homelab was simplified:

### Hypervisor Configuration

* **VirtualBox + Extension Pack**
* **VM Template Configuration:**

  * Chipset: PIIX3
  * Secure Boot: Off
  * Graphics: VMSVGA
  * Video RAM: 64 MB
  * Disk: VDI, dynamically allocated

### Virtual Machines

| VM             | CPU | RAM | Disk  | Notes                             |
| -------------- | --- | --- | ----- | --------------------------------- |
| Ubuntu Desktop | 2 | 4 GB | 25 GB | Primary Linux desktop environment |
| Kali Linux     | 2 | 3 GB | 25 GB | Security testing and pentesting   |
| Ubuntu Server  | 2 | 2 GB | 20 GB | Headless server environment       |

### Networking

* NAT (default)

### Maintenance

* Created snapshots of each VM immediately after clean installation
* Snapshot name: **“Clean Install”**

---

## Updating

```
sudo apt update
sudo apt upgrade
```

After a restart, the Ubuntu Server VM began blackscreening. After several resets it temporarily booted, but the issue returned in later reboots. Assigning one additional CPU core (now running with 2 cores) resolved the issue for now.

### Maintenance

* Created snapshots of each VM immediately after updating
* Snapshot name: **“Updated”**

---

## Setting Up

To maintain consistency between the desktop environments, I adjusted several system settings, including:

* Display resolution
* Dark mode
* Keyboard and input settings
* General UI preferences
* Keyboard layout

### Maintenance

* Created snapshots of each VM immediately after finishing setting up.
* Snapshot name: **“Finished”**

---

## Networking

To expand the homelab’s networking capabilities, I configured three separate VirtualBox networks, each serving a different purpose:

### Network Definitions

#### **Adapter 1 — NAT Network (VM ↔ Internet)**

* **Name:** NATNET
* **Adapter Type:** Intel PRO/1000 MT Desktop
* **Promiscuous Mode:** Deny
* **Cable Connected:** Yes
* **DHCP:** Yes

#### **Adapter 2 — Host-Only Network (VM ↔ Host Machine)**

* **Name:** VirtualBox Host-Only Ethernet Adapter
* **Adapter Type:** Intel PRO/1000 MT Desktop
* **Promiscuous Mode:** Allow VMs
* **Cable Connected:** Yes
* **DHCP:** Yes

#### **Adapter 3 — Internal Network (VM ↔ VM)**

* **Name:** LABNET
* **Adapter Type:** Intel PRO/1000 MT Desktop
* **Promiscuous Mode:** Allow All
* **Cable Connected:** Yes
* **DHCP:** No (static addressing required)

### Network Profiles

After researching networking security, I opted not to keep the Internal Network connected to the NAT network simultaneously. This prevents possible malware from escaping the lab environment.

#### **Connections (Management Mode)**

Used during normal operation and updates.

| VM             | NAT Network | Host-Only | Internal Network |
| -------------- | ----------- | --------- | ---------------- |
| Ubuntu Desktop | Yes         | Yes       | No               |
| Kali Linux     | Yes         | Yes       | Yes              |
| Ubuntu Server  | Yes         | Yes       | No               |

#### **Connections (Attacking Mode)**

Used during tests involving exploitation or malware.

| VM             | NAT Network | Host-Only | Internal Network |
| -------------- | ----------- | --------- | ---------------- |
| Ubuntu Desktop | No          | Yes       | Yes              |
| Kali Linux     | Yes         | Yes       | Yes              |
| Ubuntu Server  | No          | Yes       | Yes              |

### Internal Network Configuration

Each VM was configured with a **static IP** on the Internal Network (LABNET):

#### **Ubuntu Desktop**

```
sudo nano /etc/netplan/01-netcfg.yaml
```

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s9:
      dhcp4: false
      addresses:
        - 192.168.10.30/24
```

```
sudo netplan apply
```

#### **Kali Linux**

```
sudo nmcli con show
sudo nmcli con mod "Ethernet connection 3" ipv4.addresses 192.168.10.20/24
sudo nmcli con mod "Ethernet connection 3" ipv4.method manual
sudo nmcli con up "Ethernet connection 3"
```

#### **Ubuntu Server**

```
sudo nano /etc/netplan/01-netcfg.yaml
```

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
      dhcp4: true
    enp0s8:
      dhcp4: false
      addresses:
        - 192.168.56.110/24
      optional: true
    enp0s9:
      dhcp4: false
      addresses:
        - 192.168.10.10/24
```

```
sudo netplan apply
```

### Resulting Interface Assignments

**Ubuntu Desktop**

* NAT: `enp0s3` → 10.0.2.8/24
* Host-Only: `enp0s8` → 192.168.56.104/24
* Internal: `enp0s9` → 192.168.10.30/24

**Kali Linux**

* NAT: `eth0` → 10.0.2.15/24
* Host-Only: `eth1` → 192.168.56.105/24
* Internal: `eth2` → 192.168.10.20/24

**Ubuntu Server**

* NAT: `enp0s3` → 10.0.2.7/24
* Host-Only: `enp0s8` → 192.168.56.110/24
* Internal: `enp0s9` → 192.168.10.10/24

Ping tests across the Internal Network were successful. For the Host-Only network, the host firewall required an exception to allow VM → host communication.

### Maintenance

* Created snapshots of each VM immediately after finishing the networking.
* Snapshot name: **“Connected”**

---

## Lessons

* Hardware limitations may require simplifying an initial homelab design.
* Arch Linux may be challenging due to rolling releases and resource-limited setup.
* Snapshots in every stage are essential for maintaining clean baselines for testing.
* Internal Networks should be isolated from the NAT Network during offensive testing to ensure safety.
* Assigning static IPs and understanding network interfaces is crucial.

---

## Next Steps

* Add Windows VMs once sufficient resources are available or move to a more capable host.

---

*Project Status:* **Phase 1 - Complete**
