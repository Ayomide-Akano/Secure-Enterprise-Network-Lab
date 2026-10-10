# IP Addressing Plan

## IPv4 Addressing Overview

This lab uses a structured, scalable addressing plan for HQ and Branch segments. A key design principle is standardization: all gateways use .1, core switches use .2 and .3. This eliminates confusion and aligns with enterprise best practices.

## HQ Addressing (10.10.0.0/16)

### VLAN Interface Assignments

| VLAN | Name | Subnet | Virtual Gateway (HSRP) | HQ-CORE-SW1 | HQ-CORE-SW2 | Purpose |
|------|------|--------|------------------------|-------------|-------------|---------|
| 10 | MGMT | 10.10.10.0/24 | 10.10.10.1 | 10.10.10.2 | 10.10.10.3 | Management and infrastructure |
| 20 | USERS | 10.10.20.0/24 | 10.10.20.1 | 10.10.20.2 | 10.10.20.3 | Office users and workstations |
| 30 | SERVERS | 10.10.30.0/24 | 10.10.30.1 | 10.10.30.2 | 10.10.30.3 | Internal server resources |
| 40 | GUEST | 10.10.40.0/24 | 10.10.40.1 | 10.10.40.2 | 10.10.40.3 | Guest access and isolation |
| 50 | DMZ | 10.10.50.0/24 | 10.10.50.1 | 10.10.50.2 | 10.10.50.3 | Public-facing DMZ services |
| 99 | NATIVE | 10.10.99.0/24 | 10.10.99.1 | 10.10.99.2 | 10.10.99.3 | Native/trunk VLAN for switch management |

### IP Allocation Details

| Device | Interface | VLAN | IP Address | Mask | Purpose |
|--------|-----------|------|-----------|------|---------|
| HQ-CORE-SW1 | SVI VLAN 10 | 10 | 10.10.10.2 | /24 | Management SVI on primary core |
| HQ-CORE-SW1 | SVI VLAN 20 | 20 | 10.10.20.2 | /24 | User VLAN SVI on primary core |
| HQ-CORE-SW1 | SVI VLAN 30 | 30 | 10.10.30.2 | /24 | Server VLAN SVI on primary core |
| HQ-CORE-SW1 | SVI VLAN 40 | 40 | 10.10.40.2 | /24 | Guest VLAN SVI on primary core |
| HQ-CORE-SW1 | SVI VLAN 50 | 50 | 10.10.50.2 | /24 | DMZ VLAN SVI on primary core |
| HQ-CORE-SW1 | SVI VLAN 99 | 99 | 10.10.99.2 | /24 | Native VLAN SVI on primary core |
| HQ-CORE-SW2 | SVI VLAN 10 | 10 | 10.10.10.3 | /24 | Management SVI on secondary core |
| HQ-CORE-SW2 | SVI VLAN 20 | 20 | 10.10.20.3 | /24 | User VLAN SVI on secondary core |
| HQ-CORE-SW2 | SVI VLAN 30 | 30 | 10.10.30.3 | /24 | Server VLAN SVI on secondary core |
| HQ-CORE-SW2 | SVI VLAN 40 | 40 | 10.10.40.3 | /24 | Guest VLAN SVI on secondary core |
| HQ-CORE-SW2 | SVI VLAN 50 | 50 | 10.10.50.3 | /24 | DMZ VLAN SVI on secondary core |
| HQ-CORE-SW2 | SVI VLAN 99 | 99 | 10.10.99.3 | /24 | Native VLAN SVI on secondary core |
| HQ-FW1 | Inside | - | 10.10.1.254 | /24 | Firewall inside interface |
| DMZ-SW1 | SVI VLAN 50 | 50 | 10.10.50.10 | /24 | DMZ switch management IP (secondary subnet) |
| WEB-SRV1 | Eth0 | 50 | 10.10.50.20 | /24 | DMZ web server |
| AAA-SRV1 | Eth0 | 30 | 10.10.30.10 | /24 | AAA/RADIUS server (servers VLAN) |

### HSRP Configuration Notes

- HSRP virtual gateway .1 provides redundancy for both core switches
- HQ-CORE-SW1 serves as primary HSRP speaker (holds active role)
- HQ-CORE-SW2 serves as secondary HSRP speaker (standby role)
- HSRP priority should favor HQ-CORE-SW1 to load balance active traffic

## Branch Addressing (10.20.0.0/16)

### VLAN Interface Assignments

| VLAN | Name | Subnet | Gateway | BR-SW1 | Purpose |
|------|------|--------|---------|--------|---------|
| 110 | MGMT | 10.20.10.0/24 | 10.20.10.1 | 10.20.10.2 | Branch management |
| 120 | USERS | 10.20.20.0/24 | 10.20.20.1 | 10.20.20.2 | Branch user LAN |
| 130 | SERVICES | 10.20.30.0/24 | 10.20.30.1 | 10.20.30.2 | Branch local services |
| 140 | GUEST | 10.20.40.0/24 | 10.20.40.1 | 10.20.40.2 | Branch guest access |

### IP Allocation Details

| Device | Interface | VLAN | IP Address | Mask | Purpose |
|--------|-----------|------|-----------|------|---------|
| BR-RTR1 | LAN | 120 | 10.20.20.1 | /24 | Branch router LAN gateway |
| BR-SW1 | SVI VLAN 110 | 110 | 10.20.10.2 | /24 | Branch management SVI |
| BR-SW1 | SVI VLAN 120 | 120 | 10.20.20.2 | /24 | Branch user SVI |
| BR-SW1 | SVI VLAN 130 | 130 | 10.20.30.2 | /24 | Branch services SVI |
| BR-SW1 | SVI VLAN 140 | 140 | 10.20.40.2 | /24 | Branch guest SVI |
| BR-PC1 | Eth0 | 120 | 10.20.20.10 | /24 | Branch user workstation |

## WAN Addressing

### Site-to-Site Links

| Link | ISP-RTR | BR-RTR1 | Subnet | Mask | Purpose |
|------|---------|---------|--------|------|---------|
| HQ to ISP | 203.0.113.1 | - | 203.0.113.0 | /30 | Internet backbone |
| ISP to BR | 172.16.10.1 | 172.16.10.2 | 172.16.10.0 | /30 | Branch WAN link |
| HQ-FW1 Outside | - | - | 203.0.113.2 | /30 | Firewall Internet interface |

## Loopback Addressing

| Device | Loopback Address | Purpose |
|--------|-----------------|---------|
| HQ-CORE-SW1 | 10.10.255.1 | HQ core primary loopback (OSPF RID) |
| HQ-CORE-SW2 | 10.10.255.2 | HQ core secondary loopback (OSPF RID) |
| BR-RTR1 | 10.20.255.1 | Branch router loopback (OSPF RID) |

## Design Notes and Standards

- **Gateway Rule**: All VLAN gateways use .1 (e.g., 10.10.10.1, 10.20.20.1)
- **Core Switch Rule**: Primary and secondary core switches use .2 and .3 respectively
- **Enterprise Standard**: This standardization eliminates confusion and enables quick troubleshooting
- **HSRP Redundancy**: Virtual gateway .1 is managed by HSRP between core switches
- **Summarization**: HQ uses 10.10.0.0/16, Branch uses 10.20.0.0/16 for clean route aggregation
- **WAN Links**: Point-to-point /30 subnets to minimize wasted IPs on serial links
- **Loopback IPs**: Reserved for OSPF Router IDs and future routing protocol use

## NAT and Internet Access

- HQ firewall uses PAT for outbound Internet traffic (behind 203.0.113.2)
- Branch may use NAT or routed access depending on firewall policy
- DMZ services (WEB-SRV1) may have inbound static NAT rules

## Future Expansion

- IPv6 planning for all segments
- Additional subnets under 10.10.0.0/16 and 10.20.0.0/16 as needed
- Site-to-site redundancy (secondary WAN link) using 172.16.20.0/30
