# Homelab Network Architecture (Logical Design)

This homelab was designed to support hands-on practice in networking, offensive and defensive security, malware analysis, and troubleshooting, using QEMU/KVM with libvirt and a pfSense virtual firewall.

The architecture follows strict isolation principles to avoid cross-contamination between labs, while still allowing realistic attack and analysis scenarios.

---

## Design Principles

- Ubuntu is used as the physical host and does not participate in attacks.
- pfSense is deployed as a virtual machine and acts as the **single gateway** for all lab networks.
- Each lab is isolated at Layer 2 using dedicated libvirt networks.
- Kali Linux can connect to multiple lab networks but only **one at a time**.
- Malware analysis environments are isolated from offensive tooling.
- No lab depends on the physical network of the host.

---

## High-Level Architecture

- **Host OS:** Ubuntu (ThinkPad T480)
- **Hypervisor:** QEMU/KVM + libvirt (CLI-driven via `virsh`)
- **Firewall/Gateway:** pfSense (VM with multiple NICs)
- **Attacker VM:** Kali Linux
- **Targets:** Windows, Metasploitable, Chronos, DVWA
- **Defensive / Analysis VMs:** REMnux, FLARE VM

All inter-network routing and filtering is enforced by pfSense.

---

## Network Segmentation Overview

Each lab is implemented as an isolated libvirt network with its own subnet and gateway interface on pfSense.

| Network Name     | Purpose                                   | Subnet           |
|------------------|--------------------------------------------|------------------|
| WAN-NAT          | Internet access for pfSense                | 192.168.100.0/24 |
| LAB-WIN          | Corporate Windows environment (AD + users) | 10.10.10.0/24    |
| LAB-METASPTBL    | Metasploitable 2 attack lab                | 10.20.20.0/24    |
| LAB-CHRONOS      | Chronos 1 vulnerable VM lab                | 10.30.30.0/24    |
| LAB-DVWA         | Web application attack lab (DVWA)          | 10.40.40.0/24    |
| LAB-MALWARE      | Malware analysis environment               | 10.50.50.0/24    |

---

## WAN Network

The WAN network is a libvirt NAT network used exclusively by pfSense.

- Provides internet access for selected lab networks
- No other VM connects directly to WAN
- pfSense WAN interface uses DHCP

---

## LAB-WIN: Corporate Windows Environment

This lab simulates a small corporate network with Active Directory and Windows endpoints.

### Subnet
- Network: `10.10.10.0/24`
- Gateway: `10.10.10.1` (pfSense)

### Hosts
- pfSense (LAN-WIN): `10.10.10.1`
- Windows Server (AD DS): `10.10.10.10`
- Windows 10 Client: `10.10.10.101`
- Windows 7 Client: `10.10.10.102`
- Kali Linux (when active): `10.10.10.50`

This design allows realistic testing of Active Directory attacks, lateral movement, and enterprise-style threat scenarios.

---

## LAB-METASPTBL: Metasploitable Lab

Dedicated environment for exploiting intentionally vulnerable services.

### Subnet
- Network: `10.20.20.0/24`
- Gateway: `10.20.20.1`

### Hosts
- pfSense: `10.20.20.1`
- Metasploitable 2: `10.20.20.100`
- Kali Linux: `10.20.20.50`

---

## LAB-CHRONOS: Chronos Vulnerable VM Lab

Isolated environment for exploitation and enumeration practice.

### Subnet
- Network: `10.30.30.0/24`
- Gateway: `10.30.30.1`

### Hosts
- pfSense: `10.30.30.1`
- Chronos 1: `10.30.30.100`
- Kali Linux: `10.30.30.50`

---

## LAB-DVWA: Web Application Security Lab

Environment dedicated to web vulnerability exploitation using DVWA.

### Subnet
- Network: `10.40.40.0/24`
- Gateway: `10.40.40.1`

### Hosts
- pfSense: `10.40.40.1`
- DVWA VM: `10.40.40.100`
- Kali Linux: `10.40.40.50`

---

## LAB-MALWARE: Malware Analysis Environment

This lab is strictly isolated from offensive tooling.

### Subnet
- Network: `10.50.50.0/24`
- Gateway: `10.50.50.1`

### Hosts
- pfSense: `10.50.50.1`
- REMnux: `10.50.50.10`
- FLARE VM: `10.50.50.11`

No direct connectivity from Kali is allowed. Internet access is optional and controlled via strict pfSense rules. Malware samples are transferred manually.

---

## pfSense Interface Mapping

pfSense is deployed with one interface per network.

| Interface | Connected Network |
|----------|-------------------|
| em0      | WAN-NAT           |
| em1      | LAB-WIN           |
| em2      | LAB-METASPTBL     |
| em3      | LAB-CHRONOS       |
| em4      | LAB-DVWA          |
| em5      | LAB-MALWARE       |

Interfaces can be added incrementally as new labs are enabled.

---

## Kali Linux Network Design

Kali Linux is configured with multiple network interfaces, one per attack lab.

| Interface | Connected Network |
|----------|-------------------|
| eth0     | LAB-WIN           |
| eth1     | LAB-METASPTBL     |
| eth2     | LAB-CHRONOS       |
| eth3     | LAB-DVWA          |

Operational rule: **only one interface is active at any given time**, preventing accidental cross-lab interaction.

---

## Security Considerations

- All inter-LAN routing is disabled by default in pfSense.
- Firewall rules are explicitly defined per lab.
- Malware analysis is physically and logically isolated.
- No lab traffic is bridged to the host network.

---

## Future Extensions

- VLAN segmentation inside LAB-WIN
- IDS/IPS deployment on pfSense
- Traffic mirroring to REMnux
- Centralized logging and alerting

This architecture prioritizes safety, realism, and reproducibility, while remaining fully manageable through CLI-based tooling.
