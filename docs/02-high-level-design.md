# High Level Design Document

## Contents
<p align="left">
  
</p>

<p align="left">
  
</p>

## Overview

This High-Level Design (HLD) document defines the overall enterprise network architecture for Verra Technology Inc. The purpose of this document is to describe the proposed network design at an architectural level, including site layout, routing strategy, security zones, high availability, centralized services, monitoring, and automation.

This document does not include device-level commands or detailed interface configuration. Those details will be provided in the Low-Level Design (LLD) and implementation documents.


## Design Objectives

| **ID** | **Design Objective** |
|:-------|:---------------------|
| **HLD-OB-001** | Design a scalable enterprise network architecture for a growing organization. |
| **HLD-OB-002** | Provide logical separation between departments using VLANs. |
| **HLD-OB-003** | Support dynamic routing between internal network segments. |
| **HLD-OB-004** | Provide redundant default gateway availability using HSRP. |
| **HLD-OB-005** | Secure traffic between departments using access control policies (ACLs). |
| **HLD-OB-006** | Provide centralized network services such as DHCP, DNS, Syslog, NTP, and SNMP. |
| **HLD-OB-007** | Enable centralized network monitoring and operational visibility. |
| **HLD-OB-008** | Support configuration backup and basic network automation using Python. |


