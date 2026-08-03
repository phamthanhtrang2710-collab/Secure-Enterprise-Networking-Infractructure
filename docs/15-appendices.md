# Appendices

## <a id="contents"></a>Contents

<p align="left">

<a href="#overview"><img src="https://img.shields.io/badge/OVERVIEW-0B8FD3?style=for-the-badge"></a>
<a href="#appendix-a-device-inventory"><img src="https://img.shields.io/badge/DEVICE%20INVENTORY-27AE60?style=for-the-badge"></a>
<a href="#appendix-b-vlan-summary"><img src="https://img.shields.io/badge/VLAN%20SUMMARY-8E44AD?style=for-the-badge"></a>
<a href="#appendix-c-ip-addressing-summary"><img src="https://img.shields.io/badge/IP%20ADDRESSING-16A085?style=for-the-badge"></a>
<a href="#appendix-d-interface-mapping"><img src="https://img.shields.io/badge/INTERFACES-2980B9?style=for-the-badge"></a>
<a href="#appendix-e-routing-summary"><img src="https://img.shields.io/badge/ROUTING-E67E22?style=for-the-badge"></a>
<a href="#appendix-f-hsrp-summary"><img src="https://img.shields.io/badge/HSRP-D35400?style=for-the-badge"></a>
<a href="#appendix-g-security-policy-summary"><img src="https://img.shields.io/badge/SECURITY-C0392B?style=for-the-badge"></a>
<a href="#appendix-h-infrastructure-services"><img src="https://img.shields.io/badge/SERVICES-1ABC9C?style=for-the-badge"></a>
<a href="#appendix-i-management-addressing"><img src="https://img.shields.io/badge/MANAGEMENT-3498DB?style=for-the-badge"></a>
<a href="#appendix-j-monitoring-summary"><img src="https://img.shields.io/badge/MONITORING-34495E?style=for-the-badge"></a>
<a href="#appendix-k-verification-commands"><img src="https://img.shields.io/badge/COMMANDS-7F8C8D?style=for-the-badge"></a>
<a href="#appendix-l-test-and-verification-index"><img src="https://img.shields.io/badge/TEST%20INDEX-2ECC71?style=for-the-badge"></a>
<a href="#appendix-m-backup-and-recovery-summary"><img src="https://img.shields.io/badge/BACKUP-9B59B6?style=for-the-badge"></a>
<a href="#appendix-n-document-register"><img src="https://img.shields.io/badge/DOCUMENT%20REGISTER-95A5A6?style=for-the-badge"></a>

</p>

## Overview

This document consolidates supporting technical information for the **Secure Enterprise Network Infrastructure** project.

The appendices provide quick-reference tables for device inventory, VLAN assignments, IP addressing, interface mappings, routing protocols, gateway redundancy, security controls, infrastructure services, management addressing, monitoring, verification commands, recovery procedures, and related project documentation.

The information in this document should remain synchronized with the approved High-Level Design, Low-Level Design, implementation guides, device configurations, Test Plan, Troubleshooting Guide, Monitoring Guide, Backup and Recovery Guide, and Operations Runbook.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Appendix A — Device Inventory

| Hostname | Platform | Network Layer | Primary Role | Management IP |
|:---------|:---------|:--------------|:-------------|:--------------|
| **ISP-R1** | Cisco Router | ISP | Simulated Internet Service Provider | N/A |
| **EDGE-R1** | Cisco ISR 4331 | Internet Edge | BGP, NAT, perimeter ACLs, default routing | `10.10.99.4` |
| **CORE-R1** | Cisco ISR 4331 | Core | Inter-VLAN routing, HSRP Active, OSPF | `10.10.99.2` |
| **CORE-R2** | Cisco ISR 4331 | Core | Inter-VLAN routing, HSRP Standby, OSPF | `10.10.99.3` |
| **DIST-SW1** | Cisco Catalyst 3560 | Distribution | Layer 2 trunk aggregation | `10.10.99.10` |
| **HR-SW01** | Cisco Catalyst 2960 | Access | HR endpoint connectivity | `10.10.99.21` |
| **IT-SW01** | Cisco Catalyst 2960 | Access | IT endpoint connectivity | `10.10.99.22` |
| **FIN-SW01** | Cisco Catalyst 2960 | Access | Finance endpoint connectivity | `10.10.99.23` |
| **SALES-SW01** | Cisco Catalyst 2960 | Access | Sales endpoint connectivity | `10.10.99.24` |
| **SRV-SW01** | Cisco Catalyst 2960 | Access | Server infrastructure connectivity | `10.10.99.25` |
| **WIN-SRV01** | Windows Server 2022 | Server | AD DS, DHCP, DNS, NTP | `10.10.50.10` |
| **LNX-SRV01** | Ubuntu Server 24.04 LTS | Server | Syslog, SNMP, web services, monitoring | `10.10.50.20` |

### Device Role Classification

| Device Category | Devices |
|:----------------|:--------|
| Internet Edge | ISP-R1, EDGE-R1 |
| Core Routing | CORE-R1, CORE-R2 |
| Distribution | DIST-SW1 |
| Access Switching | HR-SW01, IT-SW01, FIN-SW01, SALES-SW01, SRV-SW01 |
| Infrastructure Servers | WIN-SRV01, LNX-SRV01 |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Appendix B — VLAN Summary

| VLAN ID | VLAN Name | Subnet | HSRP Gateway | Purpose |
|:-------:|:----------|:-------|:-------------|:--------|
| **10** | HR | `10.10.10.0/24` | `10.10.10.1` | Human Resources users |
| **20** | IT | `10.10.20.0/24` | `10.10.20.1` | IT administrators and support staff |
| **30** | Finance | `10.10.30.0/24` | `10.10.30.1` | Finance and payroll systems |
| **40** | Sales | `10.10.40.0/24` | `10.10.40.1` | Sales and business operations |
| **50** | Servers | `10.10.50.0/24` | `10.10.50.1` | Centralized infrastructure services |
| **99** | Management | `10.10.99.0/24` | `10.10.99.1` | Network device administration |
| **999** | Native-BlackHole | N/A | N/A | Unused native VLAN |

### VLAN-to-Switch Mapping

| Switch | Primary VLAN | Additional VLAN | Function |
|:-------|:------------:|:---------------:|:---------|
| HR-SW01 | 10 | 99 | HR user and management connectivity |
| IT-SW01 | 20 | 99 | IT user and management connectivity |
| FIN-SW01 | 30 | 99 | Finance user and management connectivity |
| SALES-SW01 | 40 | 99 | Sales user and management connectivity |
| SRV-SW01 | 50 | 99 | Server and management connectivity |

### VLAN Security Classification

| VLAN | Security Classification | Primary Security Control |
|:----:|:------------------------|:-------------------------|
| 10 | Medium | ACL-protected user VLAN |
| 20 | High | Authorized administrative access |
| 30 | Critical | Strict inter-VLAN ACL restrictions |
| 40 | Medium | Approved business services only |
| 50 | Critical | Restricted server access |
| 99 | Critical | SSH, SNMP, and management ACLs |
| 999 | High | No user ports or Layer 3 gateway |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Appendix C — IP Addressing Summary

### Enterprise Address Blocks

| Network | Prefix | Purpose |
|:--------|:------:|:--------|
| `10.10.0.0` | `/16` | Enterprise private IPv4 address space |
| `10.10.250.0` | `/30` | EDGE-R1 to CORE-R1 routed link |
| `10.10.250.4` | `/30` | EDGE-R1 to CORE-R2 routed link |
| `203.0.113.0` | `/30` | EDGE-R1 to ISP-R1 connection |
| `198.51.100.0` | `/30` | External testing network |

### Standard Address Allocation

| Address Range | Assignment |
|:--------------|:-----------|
| `.1` | HSRP virtual gateway |
| `.2` | CORE-R1 |
| `.3` | CORE-R2 |
| `.4 – .20` | Infrastructure devices |
| `.21 – .49` | Static endpoints |
| `.50 – .200` | DHCP clients |
| `.201 – .254` | Future expansion |

### Core Gateway Addressing

| VLAN | HSRP VIP | CORE-R1 | CORE-R2 |
|:----:|:---------|:--------|:--------|
| 10 | `10.10.10.1` | `10.10.10.2` | `10.10.10.3` |
| 20 | `10.10.20.1` | `10.10.20.2` | `10.10.20.3` |
| 30 | `10.10.30.1` | `10.10.30.2` | `10.10.30.3` |
| 40 | `10.10.40.1` | `10.10.40.2` | `10.10.40.3` |
| 50 | `10.10.50.1` | `10.10.50.2` | `10.10.50.3` |
| 99 | `10.10.99.1` | `10.10.99.2` | `10.10.99.3` |

### Routed Link Addressing

| Link | Local Device | Local IP | Remote Device | Remote IP |
|:-----|:-------------|:---------|:--------------|:----------|
| Enterprise to ISP | EDGE-R1 | `203.0.113.1/30` | ISP-R1 | `203.0.113.2/30` |
| Primary Core Link | EDGE-R1 | `10.10.250.1/30` | CORE-R1 | `10.10.250.2/30` |
| Secondary Core Link | EDGE-R1 | `10.10.250.5/30` | CORE-R2 | `10.10.250.6/30` |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Appendix D — Interface Mapping

### Routed Interfaces

| Device | Interface | Connected Device | Remote Interface | Address |
|:-------|:----------|:-----------------|:-----------------|:--------|
| EDGE-R1 | G0/0 | ISP-R1 | G0/0 | `203.0.113.1/30` |
| EDGE-R1 | G0/1 | CORE-R1 | G0/1 | `10.10.250.1/30` |
| EDGE-R1 | G0/2 | CORE-R2 | G0/1 | `10.10.250.5/30` |
| CORE-R1 | G0/1 | EDGE-R1 | G0/1 | `10.10.250.2/30` |
| CORE-R2 | G0/1 | EDGE-R1 | G0/2 | `10.10.250.6/30` |

### Core-to-Distribution Trunks

| Device | Interface | Connected Device | Remote Interface | Allowed VLANs |
|:-------|:----------|:-----------------|:-----------------|:--------------|
| CORE-R1 | G0/0 | DIST-SW1 | G1/0/1 | 10,20,30,40,50,99 |
| CORE-R2 | G0/0 | DIST-SW1 | G1/0/2 | 10,20,30,40,50,99 |

### Distribution-to-Access Trunks

| DIST-SW1 Interface | Connected Device | Remote Interface | Allowed VLANs |
|:-------------------|:-----------------|:-----------------|:--------------|
| G1/0/3 | HR-SW01 | G0/1 | 10,99 |
| G1/0/4 | IT-SW01 | G0/1 | 20,99 |
| G1/0/5 | FIN-SW01 | G0/1 | 30,99 |
| G1/0/6 | SALES-SW01 | G0/1 | 40,99 |
| G1/0/7 | SRV-SW01 | G0/1 | 50,99 |

### Server Access Ports

| Switch | Interface | Connected Device | VLAN |
|:-------|:----------|:-----------------|:----:|
| SRV-SW01 | G0/2 | WIN-SRV01 | 50 |
| SRV-SW01 | G0/3 | LNX-SRV01 | 50 |

### Trunk Standards

| Parameter | Value |
|:----------|:------|
| Encapsulation | IEEE 802.1Q |
| Native VLAN | 999 |
| DTP | Disabled |
| Allowed VLANs | Explicitly configured |
| End-User Devices on Trunks | Not permitted |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Appendix E — Routing Summary

### Routing Protocols

| Protocol | Scope | Devices | Purpose |
|:---------|:------|:--------|:--------|
| OSPFv2 | Internal | EDGE-R1, CORE-R1, CORE-R2 | Internal dynamic routing |
| eBGP | External | EDGE-R1, ISP-R1 | ISP route exchange |
| Static Route | Edge | EDGE-R1, ISP-R1 | Default and return routing |
| HSRP | VLAN Gateways | CORE-R1, CORE-R2 | First-hop redundancy |

### OSPF Parameters

| Parameter | Value |
|:----------|:------|
| Process ID | 1 |
| Area | 0 |
| EDGE-R1 Router ID | `1.1.1.1` |
| CORE-R1 Router ID | `2.2.2.2` |
| CORE-R2 Router ID | `3.3.3.3` |
| Passive Interfaces | VLAN gateway interfaces |
| Authentication | Future enhancement |
| ECMP | Enabled |

### OSPF Networks

| Network | Participating Devices |
|:--------|:----------------------|
| `10.10.10.0/24` | CORE-R1, CORE-R2 |
| `10.10.20.0/24` | CORE-R1, CORE-R2 |
| `10.10.30.0/24` | CORE-R1, CORE-R2 |
| `10.10.40.0/24` | CORE-R1, CORE-R2 |
| `10.10.50.0/24` | CORE-R1, CORE-R2 |
| `10.10.99.0/24` | CORE-R1, CORE-R2 |
| `10.10.250.0/30` | EDGE-R1, CORE-R1 |
| `10.10.250.4/30` | EDGE-R1, CORE-R2 |

### BGP Parameters

| Parameter | Value |
|:----------|:------|
| Enterprise AS | 65001 |
| ISP AS | 65000 |
| Enterprise BGP Device | EDGE-R1 |
| ISP Peer | ISP-R1 |
| Advertised Enterprise Prefix | `10.10.0.0/16` |
| Default Internet Route | Statically configured toward ISP |
| BGP Authentication | Future enhancement |

### Static Routes

| Device | Destination | Next Hop |
|:-------|:------------|:---------|
| EDGE-R1 | `0.0.0.0/0` | `203.0.113.2` |
| ISP-R1 | `10.10.0.0/16` | `203.0.113.1` |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Appendix F — HSRP Summary

### HSRP Parameters

| Parameter | CORE-R1 | CORE-R2 |
|:----------|:--------|:--------|
| Role | Preferred Active | Standby |
| Base Priority | 110 | 100 |
| Preemption | Enabled | Enabled |
| Interface Tracking | Enabled | Enabled |
| Version | HSRPv2 | HSRPv2 |

### HSRP Group Mapping

| VLAN | Group | Virtual IP | Preferred Active |
|:----:|:-----:|:-----------|:-----------------|
| 10 | 10 | `10.10.10.1` | CORE-R1 |
| 20 | 20 | `10.10.20.1` | CORE-R1 |
| 30 | 30 | `10.10.30.1` | CORE-R1 |
| 40 | 40 | `10.10.40.1` | CORE-R1 |
| 50 | 50 | `10.10.50.1` | CORE-R1 |
| 99 | 99 | `10.10.99.1` | CORE-R1 |

### Expected Failover Behavior

| Failure Event | Expected Result |
|:--------------|:----------------|
| CORE-R1 failure | CORE-R2 becomes Active |
| CORE-R1 uplink failure | Tracking reduces priority and initiates failover |
| CORE-R1 recovery | Preemption restores CORE-R1 as Active |
| CORE-R2 failure | CORE-R1 remains Active |
| OSPF path failure | Routing reconverges over the remaining path |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Appendix G — Security Policy Summary

### Inter-VLAN Access Policy

| Source | Destination | Services | Action |
|:-------|:------------|:---------|:------:|
| IT VLAN | Enterprise VLANs | Administrative traffic | Allow |
| Management VLAN | Network Devices | SSH, SNMP | Allow |
| HR VLAN | Finance VLAN | Any | Deny |
| HR VLAN | Server VLAN | DNS, DHCP, HTTPS | Allow |
| Finance VLAN | Server VLAN | DNS, DHCP, HTTPS, approved database services | Allow |
| Sales VLAN | Server VLAN | DNS, HTTPS | Allow |
| User VLANs | Management VLAN | Any | Deny |
| User VLANs | Internet | HTTP, HTTPS, DNS, ICMP | Allow |
| Internet | Internal VLANs | Unsolicited traffic | Deny |
| Any | Any | Unapproved traffic | Deny |

### Perimeter Security

| Security Function | Implementation |
|:------------------|:---------------|
| Firewall Services | Simulated using perimeter ACLs on EDGE-R1 |
| Address Translation | PAT on EDGE-R1 |
| Inbound Policy | Deny unsolicited traffic |
| Outbound Policy | Permit approved services |
| Management from Internet | Denied |
| Security Logging | Forwarded to LNX-SRV01 |

### Layer 2 Security Controls

| Control | Status |
|:--------|:------:|
| Port Security | Enabled |
| PortFast | Enabled on access ports |
| BPDU Guard | Enabled on access ports |
| Rapid PVST+ | Enabled |
| Native VLAN 999 | Enabled |
| DTP | Disabled |
| Storm Control | Enabled |
| Unused Ports | Administratively shutdown |

### Management Security

| Control | Implementation |
|:--------|:---------------|
| Administrative Protocol | SSH Version 2 |
| Telnet | Disabled |
| Authentication | Local AAA |
| Monitoring | SNMPv3 preferred |
| Management Network | VLAN 99 |
| Internet Administrative Access | Denied |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Appendix H — Infrastructure Services

| Service | Platform | Address | Function |
|:--------|:---------|:--------|:---------|
| Active Directory | WIN-SRV01 | `10.10.50.10` | Identity and domain services |
| DHCP | WIN-SRV01 | `10.10.50.10` | Dynamic IPv4 assignment |
| DNS | WIN-SRV01 | `10.10.50.10` | Internal and external name resolution |
| NTP | WIN-SRV01 | `10.10.50.10` | Time synchronization |
| Syslog | LNX-SRV01 | `10.10.50.20` | Centralized device logging |
| SNMP Monitoring | LNX-SRV01 | `10.10.50.20` | Infrastructure monitoring |
| Web Services | LNX-SRV01 | `10.10.50.20` | Internal web services |
| LibreNMS / Zabbix | LNX-SRV01 | `10.10.50.20` | Monitoring dashboards and alerting |

### DHCP Scope Summary

| VLAN | Scope Start | Scope End | Default Gateway | DNS Server |
|:----:|:------------|:----------|:----------------|:-----------|
| 10 | `10.10.10.50` | `10.10.10.200` | `10.10.10.1` | `10.10.50.10` |
| 20 | `10.10.20.50` | `10.10.20.200` | `10.10.20.1` | `10.10.50.10` |
| 30 | `10.10.30.50` | `10.10.30.200` | `10.10.30.1` | `10.10.50.10` |
| 40 | `10.10.40.50` | `10.10.40.200` | `10.10.40.1` | `10.10.50.10` |
| 50 | Static Only | Static Only | `10.10.50.1` | `10.10.50.10` |
| 99 | Static Only | Static Only | `10.10.99.1` | `10.10.50.10` |

### DNS Records

| Hostname | Record Type | Address |
|:---------|:-----------:|:--------|
| `win-srv01.verra.local` | A | `10.10.50.10` |
| `lnx-srv01.verra.local` | A | `10.10.50.20` |
| `edge-r1.verra.local` | A | `10.10.99.4` |
| `core-r1.verra.local` | A | `10.10.99.2` |
| `core-r2.verra.local` | A | `10.10.99.3` |
| `dist-sw1.verra.local` | A | `10.10.99.10` |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Appendix I — Management Addressing

| Hostname | Management IP | Access Method |
|:---------|:--------------|:--------------|
| EDGE-R1 | `10.10.99.4` | SSH |
| CORE-R1 | `10.10.99.2` | SSH |
| CORE-R2 | `10.10.99.3` | SSH |
| DIST-SW1 | `10.10.99.10` | SSH |
| HR-SW01 | `10.10.99.21` | SSH |
| IT-SW01 | `10.10.99.22` | SSH |
| FIN-SW01 | `10.10.99.23` | SSH |
| SALES-SW01 | `10.10.99.24` | SSH |
| SRV-SW01 | `10.10.99.25` | SSH |
| WIN-SRV01 | `10.10.50.10` | RDP / PowerShell |
| LNX-SRV01 | `10.10.50.20` | SSH |

### Management Access Policy

| Source | Destination | Protocol | Action |
|:-------|:------------|:---------|:------:|
| Management VLAN | Network Infrastructure | SSH, SNMP | Allow |
| IT VLAN | Network Infrastructure | SSH | Allow |
| User VLANs | Network Infrastructure | Any | Deny |
| Internet | Network Infrastructure | Any | Deny |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Appendix J — Monitoring Summary

### Monitoring Components

| Component | Method | Monitored Items |
|:----------|:-------|:----------------|
| Routers | SNMP, ICMP | Availability, CPU, memory, routing, interfaces |
| Switches | SNMP | Port state, VLAN status, errors, utilization |
| Servers | SNMP / Agent | CPU, memory, storage, service availability |
| Syslog | UDP 514 | Events, configuration changes, security violations |
| DHCP | Service check | Scope and lease availability |
| DNS | Service check | Internal and external resolution |
| NTP | Service check | Synchronization status |
| Internet | ICMP / Service test | External connectivity |

### Monitoring Thresholds

| Metric | Threshold | Required Action |
|:-------|:---------:|:----------------|
| Device Availability | Below 100% | Immediate investigation |
| CPU Utilization | Above 80% | Generate warning |
| Memory Utilization | Above 85% | Generate warning |
| Interface Utilization | Above 80% | Investigate congestion |
| Packet Loss | Above 1% | Investigate network path |
| Interface Errors | Increasing | Investigate physical or duplex issue |
| Critical Syslog Event | Any | Immediate review |
| Backup Failure | Any | Immediate investigation |

### Monitoring Protocols

| Protocol | Purpose |
|:---------|:--------|
| SNMPv3 | Secure device polling |
| SNMPv2c | Lab fallback where required |
| Syslog | Centralized event collection |
| ICMP | Device and path availability |
| SSH | Secure management and validation |
| Agent Monitoring | Server operating system visibility |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Appendix K — Verification Commands

### General Device Verification

```text
show running-config
show startup-config
show version
show inventory
show ip interface brief
show interfaces
show logging
