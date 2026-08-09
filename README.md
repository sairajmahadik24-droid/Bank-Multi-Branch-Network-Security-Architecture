# 🏦 Multi-Branch Bank Network Infrastructure & Security Design

An enterprise-grade Cisco Packet Tracer network simulation modeling a secure **Multi-Branch Financial Architecture**. The design enforces strict Layer 2/3 security, dynamic inter-branch routing, server zone access control, and edge address translation aligned with financial security standards (e.g., PCI-DSS principles).

---

## 📌 Executive Summary

Modern banking networks require strict logical separation between teller/customer-facing branches, administrative branches, and central data center servers. This project implements a high-availability, zero-trust network topology connecting:
* **Branch Tellers & Customer Service (VLANs 10 & 40):** Isolated segments with restricted access.
* **Regional Audit & Management (VLAN 20):** Privileged segment authorized to reach headquarters servers.
* **Central Data Center / HQ (VLAN 30):** Houses core banking databases and centralized DHCP infrastructure.
* **Enterprise Gateway:** Edge router configured with PAT (NAT Overload) for secure WAN/ISP egress.

---

## 🛠️ Technical Architecture & Key Features

### 1. Multi-Branch Segmentation & Subnetting
* **Logical VLAN Division:**
  * **VLAN 10 (Branch A Tellers):** `10.0.0.0/24`
  * **VLAN 20 (Regional Management):** `20.0.0.0/24`
  * **VLAN 30 (Central HQ / Data Center):** `30.0.0.0/24`
  * **VLAN 40 (Branch B Tellers):** `40.0.0.0/24`
* **OSPF Area 0 Dynamic Routing:** Enables automated, full-mesh routing convergence across inter-branch router links.
* **Centralized DHCP Relay:** Configured `ip helper-address` on branch interfaces to forward DHCP traffic securely to `Server0` at HQ.

### 2. Financial Security & Threat Mitigation
* **Layer 2 Infrastructure Hardening:**
  * **Switch Port Security:** Configured `switchport port-security maximum 1` and `violation shutdown` on access ports to prevent rogue hardware insertion in physical branch locations.
  * **DHCP Snooping:** Activated on switches to drop unauthorized or rogue DHCP server responses.
* **Access Control List (ACL 101) - Data Center Isolation:**
  * Applied outbound on Data Center router interface (`Gig0/1`).
  * **Permits** VLAN 20 (Regional Management) to access core database servers (`30.0.0.0/24`).
  * **Permits** DHCP relay traffic (UDP Port 67/68) across subnets.
  * **Denies** general branch tellers (VLAN 10 & 40) from directly accessing core Data Center hosts.

### 3. Remote Management Hardening
* Disabled unencrypted management interfaces (Telnet/HTTP).
* Enforced **SSH v2** across all routers and switches using **RSA 1024-bit key pairs** and encrypted local credentials (`service password-encryption` and `enable secret`).

### 4. Edge Translation & Internet Gateway
* **PAT (NAT Overload):** Configured on perimeter gateway (`MRC`) mapping internal branch ranges (`10.0.0.0/8`, `20.0.0.0/8`, `30.0.0.0/8`, `40.0.0.0/8`) to a single public IP (`12.0.1.1`) for outbound WAN traffic.

