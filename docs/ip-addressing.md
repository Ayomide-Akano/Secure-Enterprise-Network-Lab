# IP Addressing Plan

## Overview

This lab uses a structured, scalable addressing plan for HQ and Branch segments. A key design principle is **standardization**: all VLAN gateways use **.1**, primary core switch uses **.2**, and secondary core switch uses **.3**. This eliminates confusion and aligns with enterprise best practices for rapid troubleshooting and network management.

## Addressing Standard (Enterprise Best Practice)

| Position | Convention | Example |
|----------|-----------|---------|
| Virtual Gateway (HSRP) | .1 | 10.10.10.1 |
| Primary Core Switch (CORE-SW1) | .2 | 10.10.10.2 |
| Secondary Core Switch (CORE-SW2) | .3 | 10.10.10.3 |

**Benefit**: Every engineer knows that .1 is the gateway, .2 is the primary switch, and .3 is the standby. This pattern reduces troubleshooting time and improves operational clarity.

---

## HQ Addressing (10.10.0.0/16)

### HQ VLAN IP Allocation

| VLAN | Name | Subnet | Virtual Gateway | CORE-SW1 | CORE-SW2 | Purpose |
|------|------|--------|-----------------|----------|----------|---------|
| 10 | MGMT | 10.10.10.0/24 | 10.10.10.1 | 10.10.10.2 | 10.10.10.3 | Management and infrastructure |
| 20 | USERS | 10.10.20.0/24 | 10.10.20.1 | 10.10.20.2 | 10.10.20.3 | Office users and workstations |
| 30 | SERVERS | 10.10.30.0/24 | 10.10.30.1 | 10.10.30.2 | 10.10.30.3 | Internal server resources |
| 40 | GUEST | 10.10.40.0/24 | 10.10.40.1 | 10.10.40.2 | 10.10.40.3 | Guest access and isolation |
| 50 | DMZ | 10.10.50.0/24 | 10.10.50.1 | 10.10.50.2 | 10.10.50.3 | Public-facing DMZ services |
| 99 | NATIVE | 10.10.99.0/24 | 10.10.99.1 | 10.10.99.2 | 10.10.99.3 | Native/trunk VLAN for switch management |

### HQ Detailed Interface Assignments

| Device | Interface | VLAN | IP Address | Mask | Purpose |
|--------|-----------|------|-----------|------|---------|
| HQ-CORE-SW1 | SVI VLAN 10 | 10 | 10.10.10.2 | /24 | Management SVI (primary) |
| HQ-CORE-SW1 | SVI VLAN 20 | 20 | 10.10.20.2 | /24 | User VLAN SVI (primary) |
| HQ-CORE-SW1 | SVI VLAN 30 | 30 | 10.10.30.2 | /24 | Server VLAN SVI (primary) |
| HQ-CORE-SW1 | SVI VLAN 40 | 40 | 10.10.40.2 | /24 | Guest VLAN SVI (primary) |
| HQ-CORE-SW1 | SVI VLAN 50 | 50 | 10.10.50.2 | /24 | DMZ VLAN SVI (primary) |
| HQ-CORE-SW1 | SVI VLAN 99 | 99 | 10.10.99.2 | /24 | Native VLAN SVI (primary) |
| HQ-CORE-SW2 | SVI VLAN 10 | 10 | 10.10.10.3 | /24 | Management SVI (secondary) |
| HQ-CORE-SW2 | SVI VLAN 20 | 20 | 10.10.20.3 | /24 | User VLAN SVI (secondary) |
| HQ-CORE-SW2 | SVI VLAN 30 | 30 | 10.10.30.3 | /24 | Server VLAN SVI (secondary) |
| HQ-CORE-SW2 | SVI VLAN 40 | 40 | 10.10.40.3 | /24 | Guest VLAN SVI (secondary) |
| HQ-CORE-SW2 | SVI VLAN 50 | 50 | 10.10.50.3 | /24 | DMZ VLAN SVI (secondary) |
| HQ-CORE-SW2 | SVI VLAN 99 | 99 | 10.10.99.3 | /24 | Native VLAN SVI (secondary) |
| HQ-FW1 | Inside Interface | - | 10.10.1.254 | /24 | Firewall inside interface (core zone) |
| DMZ-SW1 | SVI VLAN 50 | 50 | 10.10.50.10 | /24 | DMZ switch management IP |
| WEB-SRV1 | Ethernet | 50 | 10.10.50.20 | /24 | Web server in DMZ |
| AAA-SRV1 | Ethernet | 30 | 10.10.30.10 | /24 | AAA/RADIUS server in SERVERS VLAN |

### HQ HSRP Configuration

- **VLAN 10-50, 99**: HSRP active on HQ-CORE-SW1 (primary)
- **Standby**: HQ-CORE-SW2 (secondary)
- **Virtual IP (.1)**: Acts as default gateway for all end devices
- **Priority**: Configure HQ-CORE-SW1 with higher HSRP priority to maintain active role under normal conditions

---

## Branch Addressing (10.20.0.0/16)

### Branch VLAN IP Allocation

| VLAN | Name | Subnet | Gateway | BR-SW1 | Purpose |
|------|------|--------|---------|--------|---------|
| 110 | MGMT | 10.20.10.0/24 | 10.20.10.1 | 10.20.10.2 | Branch management |
| 120 | USERS | 10.20.20.0/24 | 10.20.20.1 | 10.20.20.2 | Branch user LAN |
| 130 | SERVICES | 10.20.30.0/24 | 10.20.30.1 | 10.20.30.2 | Branch local services |
| 140 | GUEST | 10.20.40.0/24 | 10.20.40.1 | 10.20.40.2 | Branch guest access |

### Branch Detailed Interface Assignments

| Device | Interface | VLAN | IP Address | Mask | Purpose |
|--------|-----------|------|-----------|------|---------|
| BR-RTR1 | LAN Interface | 120 | 10.20.20.1 | /24 | Branch router LAN gateway (user VLAN) |
| BR-SW1 | SVI VLAN 110 | 110 | 10.20.10.2 | /24 | Branch switch management SVI |
| BR-SW1 | SVI VLAN 120 | 120 | 10.20.20.2 | /24 | Branch user VLAN SVI |
| BR-SW1 | SVI VLAN 130 | 130 | 10.20.30.2 | /24 | Branch services VLAN SVI |
| BR-SW1 | SVI VLAN 140 | 140 | 10.20.40.2 | /24 | Branch guest VLAN SVI |
| BR-PC1 | Ethernet | 120 | 10.20.20.10 | /24 | Branch user workstation |

---

## WAN Addressing (172.16.0.0/12)

### Site-to-Site Links

| Link | From Device | From IP | To Device | To IP | Subnet | Mask | Purpose |
|------|-------------|---------|-----------|-------|--------|------|---------|
| Internet | ISP-RTR | 203.0.113.1 | HQ-FW1 | 203.0.113.2 | 203.0.113.0 | /30 | Internet backbone |
| HQ→BR WAN | ISP-RTR | 172.16.10.1 | BR-RTR1 | 172.16.10.2 | 172.16.10.0 | /30 | Branch site-to-site link |

### WAN Interface Details

| Device | Interface | IP Address | Mask | Remote Device | Purpose |
|--------|-----------|-----------|------|---------------|---------|
| ISP-RTR | Gi0/0 | 203.0.113.1 | /30 | HQ-FW1 | Provider-facing Internet |
| ISP-RTR | Gi0/1 | 172.16.10.1 | /30 | BR-RTR1 | WAN to Branch |
| HQ-FW1 | Outside | 203.0.113.2 | /30 | ISP-RTR | Internet-facing firewall |
| HQ-FW1 | Inside | 10.10.1.254 | /24 | HQ-CORE-SW1 | Core connectivity |
| BR-RTR1 | WAN | 172.16.10.2 | /30 | ISP-RTR | Branch WAN termination |
| BR-RTR1 | LAN | 10.20.20.1 | /24 | BR-SW1 | Branch LAN gateway |

---

## Loopback Addressing (10.x.255.0/24)

| Device | Loopback Interface | IP Address | Purpose |
|--------|-------------------|-----------|---------|
| HQ-CORE-SW1 | Lo0 | 10.10.255.1 | HQ core primary loopback (OSPF Router ID) |
| HQ-CORE-SW2 | Lo0 | 10.10.255.2 | HQ core secondary loopback (OSPF Router ID) |
| BR-RTR1 | Lo0 | 10.20.255.1 | Branch router loopback (OSPF Router ID) |

---

## Design Principles and Standards

### Standardization Rule
- **All VLAN gateways end in .1** (e.g., 10.10.10.1, 10.20.20.1, 10.10.50.1)
- **All primary core switches end in .2** (e.g., 10.10.10.2, 10.20.20.2)
- **All secondary core switches end in .3** (e.g., 10.10.10.3, 10.20.20.3)

**Why?** This pattern is an industry standard that reduces confusion, speeds troubleshooting, and makes network operations predictable across different shifts and teams.

### Summarization Strategy
- **HQ LAN**: 10.10.0.0/16 (includes all HQ VLANs 10-50, 99)
- **Branch LAN**: 10.20.0.0/16 (includes all Branch VLANs 110-140)
- **WAN**: 172.16.0.0/12 (covers all future point-to-point links)

### HSRP Redundancy
- Virtual gateway addresses (.1) are managed by HSRP
- Ensures automatic failover if primary core switch fails
- Both core switches run HSRP, with priority favoring HQ-CORE-SW1

### Reserved Ranges
- **10.10.99.0/24**: Native VLAN management (VLAN 99)
- **10.10.1.0/24**: Firewall-to-core connectivity zone
- **203.0.113.0/30**: Example Internet space (RFC 5737 documentation range)

---

## NAT and Internet Access

- **HQ Firewall**: Uses Port Address Translation (PAT) for outbound Internet traffic
  - All internal HQ traffic destined for the Internet appears as 203.0.113.2
  - Enables one public IP to serve the entire HQ LAN
- **Branch**: Default gateway is BR-RTR1 (10.20.20.1)
  - Branch traffic to HQ uses IPsec VPN tunnel
  - Internet traffic may route through HQ firewall or direct (depending on policy)
- **DMZ Services**: WEB-SRV1 (10.10.50.20) may have inbound static NAT rules for public access

---

## Future Expansion

- **IPv6**: Reserve 2001:db8:1::/48 for HQ, 2001:db8:2::/48 for Branch
- **Secondary WAN Link**: 172.16.20.0/30 for redundant site-to-site connectivity
- **Additional Subnets**: Extend 10.10.0.0/16 and 10.20.0.0/16 as business growth requires
- **Multi-site**: Scale to additional branches using 10.30.0.0/16, 10.40.0.0/16, etc., maintaining the same .1/.2/.3 pattern

---

## Quick Reference: Common Gateways

| Purpose | HQ | Branch |
|---------|----|----|
| Management gateway | 10.10.10.1 | 10.20.10.1 |
| User gateway | 10.10.20.1 | 10.20.20.1 |
| Server gateway | 10.10.30.1 | 10.20.30.1 |
| Guest gateway | 10.10.40.1 | 10.20.40.1 |
| DMZ gateway | 10.10.50.1 | N/A |
