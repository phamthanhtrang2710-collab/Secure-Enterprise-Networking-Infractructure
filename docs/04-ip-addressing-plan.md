# IP Addressing Plan

## <a id="contents"></a>Contents

<p align="left">

<a href="#overview"><img src="https://img.shields.io/badge/OVERVIEW-0B8FD3?style=for-the-badge"></a>
<a href="#addressing-design-principles"><img src="https://img.shields.io/badge/PRINCIPLES-27AE60?style=for-the-badge"></a>
<a href="#enterprise-ipv4-address-allocation"><img src="https://img.shields.io/badge/IPv4%20BLOCK-8E44AD?style=for-the-badge"></a>
<a href="#reserved-addressing-strategy"><img src="https://img.shields.io/badge/RESERVED%20IP-E67E22?style=for-the-badge"></a>
<a href="#ip-allocation-policy"><img src="https://img.shields.io/badge/IP%20POLICY-3498DB?style=for-the-badge"></a>
<a href="#vlan-naming-convention"><img src="https://img.shields.io/badge/VLAN%20NAMING-9B59B6?style=for-the-badge"></a>
<a href="#vlan-addressing-plan"><img src="https://img.shields.io/badge/VLAN%20PLAN-16A085?style=for-the-badge"></a>
<a href="#core-router-address-allocation"><img src="https://img.shields.io/badge/CORE%20ROUTERS-D35400?style=for-the-badge"></a>
<a href="#wan-point-to-point-addressing-plan"><img src="https://img.shields.io/badge/WAN%20LINKS-C0392B?style=for-the-badge"></a>

</p>

<p align="left">

<a href="#dhcp-scope-design"><img src="https://img.shields.io/badge/DHCP%20SCOPE-2ECC71?style=for-the-badge"></a>
<a href="#management-ip-address-plan"><img src="https://img.shields.io/badge/MANAGEMENT%20IP-34495E?style=for-the-badge"></a>
<a href="#design-notes"><img src="https://img.shields.io/badge/DESIGN%20NOTES-7F8C8D?style=for-the-badge"></a>
</p>

## Overview

This document defines the IPv4 addressing plan for the Secure Enterprise Network Infrastructure project. The addressing scheme is designed to support VLAN segmentation, routing, gateway redundancy, server infrastructure, monitoring, and future expansion.

The design uses private IPv4 addressing based on the `10.10.0.0/16` address block.

## Addressing Design Principles

| Design Principle | Implementation |
|:-----------------|:---------------|
| **Scalability** | Each business function is assigned a dedicated **/24 subnet**, allowing future growth without redesigning the addressing scheme. |
| **Simplicity** | VLAN IDs are aligned with the third octet whenever possible to simplify administration and troubleshooting. |
| **Consistency** | Gateway addresses, DHCP scopes, and reserved IP ranges follow a standardized allocation model across all VLANs. |
| **Security** | User, server, and management networks are logically separated to reduce lateral movement and improve access control. |
| **Maintainability** | All address allocations follow a documented convention to simplify operations, auditing, and future expansion. |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Enterprise IPv4 Address Allocation

| Network | Prefix | Purpose |
|:---------|:------:|:--------|
| **10.10.0.0** | **/16** | Enterprise internal addressing space |
| **203.0.113.0** | **/30** | Simulated ISP WAN connection |
| **198.51.100.0** | **/30** | External testing network |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Reserved Addressing Strategy

| IP Range | Purpose |
|:----------|:--------|
| **.1** | HSRP Virtual Gateway |
| **.2** | Core Router 1 |
| **.3** | Core Router 2 |
| **.4 – .20** | Infrastructure Devices |
| **.21 – .49** | Static Endpoints |
| **.50 – .200** | DHCP Client Pool |
| **.201 – .254** | Future Expansion |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## IP Allocation Policy

| Device Type | Address Assignment |
|:------------|:------------------|
| User Workstations | DHCP |
| Laptops | DHCP |
| Printers | Static |
| IP Phones | DHCP |
| Servers | Static |
| Network Devices | Static |
| Firewalls | Static |
| Wireless Access Points | Static |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## VLAN Naming Convention

| VLAN ID | Description |
|:-------:|:------------|
|10|Human Resources|
|20|Information Technology|
|30|Finance Department|
|40|Sales Department|
|50|Enterprise Servers|
|99|Network Management|
|999|Native / Black Hole VLAN|

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## VLAN Addressing Plan

| VLAN | Name | Subnet | Mask | HSRP Gateway | DHCP Scope | Notes |
|:---:|:------|:-------------|:-------------|:-------------|:---------------------------|:---------------------------|
|10|HR|10.10.10.0/24|255.255.255.0|10.10.10.1|10.10.10.50 – 10.10.10.200|User VLAN|
|20|IT|10.10.20.0/24|255.255.255.0|10.10.20.1|10.10.20.50 – 10.10.20.200|IT Staff|
|30|Finance|10.10.30.0/24|255.255.255.0|10.10.30.1|10.10.30.50 – 10.10.30.200|Restricted ACL|
|40|Sales|10.10.40.0/24|255.255.255.0|10.10.40.1|10.10.40.50 – 10.10.40.200|Sales Users|
|50|Servers|10.10.50.0/24|255.255.255.0|10.10.50.1|Static Only|Infrastructure Servers|
|99|Management|10.10.99.0/24|255.255.255.0|10.10.99.1|Static Only|Network Management|
|999|Native / Black Hole|N/A|N/A|N/A|None|Unused Native VLAN|

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Core Router Address Allocation

| VLAN | HSRP VIP | Core R1 | Core R2 |
|:---:|:-----------|:-----------|:-----------|
|10|10.10.10.1|10.10.10.2|10.10.10.3|
|20|10.10.20.1|10.10.20.2|10.10.20.3|
|30|10.10.30.1|10.10.30.2|10.10.30.3|
|40|10.10.40.1|10.10.40.2|10.10.40.3|
|50|10.10.50.1|10.10.50.2|10.10.50.3|
|99|10.10.99.1|10.10.99.2|10.10.99.3|

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## WAN Point-to-Point Addressing Plan

| Link | Network | Prefix | Local Device | Local IP | Remote Device | Remote IP | Purpose |
|:------|:-----------|:------:|:-------------|:-------------|:--------------|:-------------|:----------------|
| EDGE-R1 ↔ ISP-R1 | `203.0.113.0` | /30 | EDGE-R1 | `203.0.113.1` | ISP-R1 | `203.0.113.2` | Internet WAN |
| CORE-R1 ↔ EDGE-R1 | `10.10.250.0` | /30 | CORE-R1 | `10.10.250.1` | EDGE-R1 | `10.10.250.2` | Primary WAN |
| CORE-R2 ↔ EDGE-R1 | `10.10.250.4` | /30 | CORE-R2 | `10.10.250.5` | EDGE-R1 | `10.10.250.6` | Secondary WAN |
| CORE-R1 ↔ CORE-R2 | `10.10.250.8` | /30 | CORE-R1 | `10.10.250.9` | CORE-R2 | `10.10.250.10` | OSPF Transit Link |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## DHCP Scope Design

| VLAN | Start Address | End Address | Excluded Addresses |
|:---:|:---------------|:-------------|:-------------------|
|10|10.10.10.50|10.10.10.200|10.10.10.1 – 10.10.10.49|
|20|10.10.20.50|10.10.20.200|10.10.20.1 – 10.10.20.49|
|30|10.10.30.50|10.10.30.200|10.10.30.1 – 10.10.30.49|
|40|10.10.40.50|10.10.40.200|10.10.40.1 – 10.10.40.49|
|50|Static Only|Static Only|Entire subnet reserved|
|99|Static Only|Static Only|Entire subnet reserved|

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Management IP Address Plan

> All network infrastructure devices are managed through **Management VLAN 99 (10.10.99.0/24)**.

| Hostname | Device Role | Management Interface | Management IP | Function |
|:----------|:------------|:--------------------:|:--------------|:---------|
| **CORE-R1** | Core Router | Vlan99 | `10.10.99.2` | Primary Core Router |
| **CORE-R2** | Core Router | Vlan99 | `10.10.99.3` | Secondary Core Router |
| **DIST-SW1** | Distribution Switch | Vlan99 | `10.10.99.10` | Distribution Layer |
| **HR-SW01** | HR Access Switch | Vlan99 | `10.10.99.11` | HR Department |
| **IT-SW01** | IT Access Switch | Vlan99 | `10.10.99.12` | IT Department |
| **FIN-SW01** | Finance Access Switch | Vlan99 | `10.10.99.13` | Finance Department |
| **SALES-SW01** | Sales Access Switch | Vlan99 | `10.10.99.14` | Sales Department |
| **SRV-SW01** | Server Access Switch | Vlan99 | `10.10.99.15` | Server Network |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Design Notes

- The enterprise uses a hierarchical IPv4 addressing model based on the **10.10.0.0/16** private address space.

- Functional departments are assigned dedicated **/24** subnets to simplify routing, troubleshooting, and future scalability.

- A consistent addressing convention is applied throughout the network:
  - `.1` → HSRP Virtual Gateway
  - `.2` → Core Router 1
  - `.3` → Core Router 2

- Infrastructure devices use static IP addressing, while end-user devices receive addresses dynamically through DHCP.

- All network infrastructure devices are managed through **Management VLAN 99**, which is isolated from user traffic.

- Point-to-point routed links use **/30** subnets to optimize address utilization.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Glossary

| Acronym | Definition |
|---------|------------|
| DHCP | Dynamic Host Configuration Protocol |
| DNS | Domain Name System |
| HSRP | Hot Standby Router Protocol |
| IP | Internet Protocol |
| ISP | Internet Service Provider |
| VLAN | Virtual Local Area Network |
| WAN | Wide Area Network |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## References

- RFC 1918 - Address Allocation for Private Internets
- RFC 5737 - IPv4 Address Blocks Reserved for Documentation
- Cisco Enterprise Campus Design Guide
- Cisco Validated Design (CVD)

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>
