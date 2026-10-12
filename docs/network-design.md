# Network Design

## Objective

Design a secure enterprise network connecting a Headquarters (HQ) and a Branch office while enforcing segmentation, redundancy, and access control.

## High-Level Topology

- HQ: core services, management, user access, security services
- Branch: remote site connectivity, local users, optional branch services
- WAN: routed connectivity between HQ and Branch
- Security boundary: firewall, ACLs, VPN, identity-based access

## Design Principles

1. Separate user, management, server, and guest traffic.
2. Use route summarization and structured addressing.
3. Apply least-privilege firewall policies.
4. Ensure redundancy where possible.
5. Centralize logging and monitoring.

## Device Inventory

| Device Name | Role | Location | Notes |
|-------------|------|----------|-------|
| HQ-CORE-SW1 | Core switch | HQ | Primary aggregation and distribution layer |
| HQ-CORE-SW2 | Core switch | HQ | Redundant core switch for resilience |
| HQ-ACC-SW1 | Access switch | HQ | User access layer |
| HQ-ACC-SW2 | Access switch | HQ | Secondary access layer |
| HQ-FW1 | Firewall | HQ | Internet edge and policy enforcement |
| DMZ-SW1 | DMZ switch | HQ | Public-facing services |
| ISP-RTR | Internet edge router | ISP | WAN/Internet provider connection |
| BR-RTR1 | Branch router | Branch | Site-to-site WAN termination |
| BR-SW1 | Branch access switch | Branch | Branch endpoint aggregation |
| WEB-SRV1 | Web server | DMZ | Public-facing server |
| AAA-SRV1 | AAA server | HQ | Authentication and accounting services |
| BR-PC1 | Branch endpoint | Branch | End-user workstation |
| USER-PCs | End-user hosts | HQ / Branch | Standard office endpoints |

## VLAN Mapping

The VLAN mapping has been standardized. Gateway ownership indicates which device (or HSRP cluster) will own the default gateway for the VLAN.

| Site | VLAN | Name | Subnet | Gateway | Gateway owner |
|------|------:|------|--------|---------:|-------------------------|
| HQ | 10 | STAFF | 10.10.10.0/24 | 10.10.10.1 | HSRP on HQ cores |
| HQ | 20 | FINANCE | 10.10.20.0/24 | 10.10.20.1 | HSRP on HQ cores |
| HQ | 30 | IT-ADMIN | 10.10.30.0/24 | 10.10.30.1 | HSRP on HQ cores |
| HQ | 40 | GUEST | 10.10.40.0/24 | 10.10.40.1 | HSRP on HQ cores |
| HQ | 50 | SERVERS | 10.10.50.0/24 | 10.10.50.1 | HSRP on HQ cores |
| HQ | 99 | MGMT | 10.10.99.0/24 | 10.10.99.1 | HSRP on HQ cores |
| HQ | 60 | DMZ | 172.16.60.0/24 | 172.16.60.1 | HQ-FW1 |
| HQ | 999 | BLACKHOLE/native | No IP subnet | None | No SVI |
| Branch | 10 | STAFF | 10.20.10.0/24 | 10.20.10.1 | BR-RTR1 |
| Branch | 99 | MGMT | 10.20.99.0/24 | 10.20.99.1 | BR-RTR1 |
| Branch | 999 | BLACKHOLE/native | No IP subnet | None | No SVI |

> Note: VLAN 60 (DMZ) uses a different addressing range (172.16.60.0/24) and is owned by the firewall (HQ-FW1). VLANs marked BLACKHOLE/native do not have SVIs and are used as native/native-trunk placeholders.

## IP Allocation per Interface

(Only key interface assignments called out here; see "HQ IP assignments" below for the canonical HQ SVI addresses.)

| Device | Interface | IP Address | Mask | Description |
|--------|-----------|-----------|------|-------------|
| ISP-RTR | Gi0/0 | 203.0.113.1 | /30 | Provider-facing internet side |
| ISP-RTR | Gi0/1 | 172.16.10.1 | /30 | WAN side to branch router |
| HQ-FW1 | Outside | 203.0.113.2 | /30 | Internet-facing firewall interface |
| HQ-FW1 | DMZ | 172.16.60.1 | /24 | DMZ firewall interface / gateway |
| ISP-RTR | Branch-facing | 198.51.100.1 | /30 | (example branch WAN) |
| BR-RTR1 | WAN | 198.51.100.2 | /30 | Branch WAN link |

## HQ IP assignments (canonical)

These are the authoritative SVI and routed-link addresses for the HQ site.

| Device | Interface/VLAN | Address | Purpose |
|--------|----------------|--------:|---------|
| HQ-CORE-SW1 | SVI VLAN 10 | 10.10.10.2/24 | STAFF SVI (primary switch) |
| HQ-CORE-SW2 | SVI VLAN 10 | 10.10.10.3/24 | STAFF SVI (secondary switch) |
| HQ-CORE-SW1 | SVI VLAN 20 | 10.10.20.2/24 | FINANCE SVI (primary) |
| HQ-CORE-SW2 | SVI VLAN 20 | 10.10.20.3/24 | FINANCE SVI (secondary) |
| HQ-CORE-SW1 | SVI VLAN 30 | 10.10.30.2/24 | IT-ADMIN SVI (primary) |
| HQ-CORE-SW2 | SVI VLAN 30 | 10.10.30.3/24 | IT-ADMIN SVI (secondary) |
| HQ-CORE-SW1 | SVI VLAN 40 | 10.10.40.2/24 | GUEST SVI (primary) |
| HQ-CORE-SW2 | SVI VLAN 40 | 10.10.40.3/24 | GUEST SVI (secondary) |
| HQ-CORE-SW1 | SVI VLAN 50 | 10.10.50.2/24 | SERVERS SVI (primary) |
| HQ-CORE-SW2 | SVI VLAN 50 | 10.10.50.3/24 | SERVERS SVI (secondary) |
| HQ-CORE-SW1 | SVI VLAN 99 | 10.10.99.2/24 | MGMT SVI (primary) |
| HQ-CORE-SW2 | SVI VLAN 99 | 10.10.99.3/24 | MGMT SVI (secondary) |
| HQ-ACC-SW1 | SVI VLAN 99 | 10.10.99.10/24 | Switch management SVI |
| HQ-ACC-SW2 | SVI VLAN 99 | 10.10.99.11/24 | Switch management SVI |
| DMZ-SW1 | SVI VLAN 99 | 10.10.99.12/24 | DMZ switch management only (DMZ access remains VLAN 60) |
| AAA-SRV1 | NIC VLAN 50 | 10.10.50.10/24 | Internal services (servers VLAN) |
| HQ-FW1 | Outside | 203.0.113.2/30 | ISP peer 203.0.113.1 |
| ISP-RTR | HQ-facing interface | 203.0.113.1/30 | HQ WAN peer |
| HQ-FW1 | Inside routed link | 10.10.254.2/30 | Core peer 10.10.254.1 |
| HQ-CORE-SW1 | Routed link to firewall | 10.10.254.1/30 | Firewall next hop 10.10.254.2 |
| HQ-FW1 | DMZ interface | 172.16.60.1/24 | DMZ gateway |
| WEB-SRV1 | NIC VLAN 60 | 172.16.60.10/24 | DMZ web server; gateway 172.16.60.1 |

> HSRP virtual gateways for VLANs 10/20/30/40/50/99 will be configured with .1 as the virtual IP (10.10.x.1) with the HQ cores (.2 and .3) as active/standby. The DMZ gateway is owned by HQ-FW1 (172.16.60.1) and therefore has no HSRP.

## IP Allocation per Branch

| Device | Interface/VLAN | Address | Purpose |
|--------|----------------|--------:|---------|
| BR-RTR1 | VLAN 10 gateway | 10.20.10.1/24 | Branch STAFF gateway |
| BR-RTR1 | VLAN 99 gateway | 10.20.99.1/24 | Branch MGMT gateway |
| BR-SW1 | SVI VLAN 99 | 10.20.99.2/24 | Branch switch management SVI |
| BR-PC1 | NIC VLAN 10 | 10.20.10.10/24 | Branch staff workstation |

## Physical Cable Map

| # | From | To | Cable | Purpose |
|---|------|----|-------|---------|
| 1 | HQ-CORE-SW1 (2 ports) | HQ-CORE-SW2 (2 ports) | Copper straight (two links) | Core interconnect, later an EtherChannel |
| 2 | HQ-CORE-SW1 | HQ-ACC-SW1 | Copper straight | Uplink |
| 3 | HQ-CORE-SW1 | HQ-ACC-SW2 | Copper straight | Uplink |
| 4 | HQ-CORE-SW2 | HQ-ACC-SW1 | Copper straight | Redundant uplink |
| 5 | HQ-CORE-SW2 | HQ-ACC-SW2 | Copper straight | Redundant uplink |
| 6 | HQ-CORE-SW1 | HQ-FW1 (inside) | Copper straight | Core to firewall |
| 7 | HQ-FW1 (outside) | ISP-RTR | Copper straight | Internet link |
| 8 | HQ-FW1 (DMZ) | DMZ-SW1 | Copper straight | DMZ zone |
| 9 | ISP-RTR | BR-RTR1 | Copper straight | Branch WAN |
| 10 | BR-RTR1 | BR-SW1 | Copper straight | Branch LAN |
| 11 | Endpoints | Their switches | Copper straight | PCs on access, WEB-SRV1 on DMZ-SW1, AAA-SRV1 on HQ-CORE-SW2, BR-PC1 on BR-SW1 |

## Port Record

### HQ core and access layer

| # | From | Port | To | Port |
|---:|---|---:|---|---:|
| 1 | HQ-CORE-SW1 | Gi1/0/23 | HQ-CORE-SW2 | Gi1/0/23 |
| 2 | HQ-CORE-SW1 | Gi1/0/24 | HQ-CORE-SW2 | Gi1/0/24 |
| 3 | HQ-CORE-SW1 | Gi1/0/1 | HQ-ACC-SW1 | Gi1/0/1 |
| 4 | HQ-CORE-SW1 | Gi1/0/2 | HQ-ACC-SW2 | Gi1/0/1 |
| 5 | HQ-CORE-SW2 | Gi1/0/1 | HQ-ACC-SW1 | Gi1/0/2 |
| 6 | HQ-CORE-SW2 | Gi1/0/2 | HQ-ACC-SW2 | Gi1/0/2 |

Why two cables between the cores, and four down to the access layer? The two core cables will become one EtherChannel. The four uplinks give every access switch a path to both cores, so no single device failure cuts anyone off.

### Edge and DMZ

| # | From | Port | To | Port |
|---:|---|---:|---|---:|
| 7 | HQ-CORE-SW1 | Gi1/0/10 | HQ-FW1 | Gi1/2 (inside) |
| 8 | HQ-FW1 | Gi1/1 (outside) | ISP-RTR | Gi0/0 |
| 9 | HQ-FW1 | Gi1/3 (DMZ) | DMZ-SW1 | Fa0/1 |
| 10 | WEB-SRV1 | Fa0 | DMZ-SW1 | Fa0/2 |

Why three separate firewall ports? Each port is a different security zone (inside, outside, DMZ), and the firewall’s rules apply between them.

### Branch

{