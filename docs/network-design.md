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

## Proposed Segments

### HQ
- Management VLAN
- User VLAN
- Server VLAN
- Guest VLAN
- DMZ / external services

### Branch
- Branch user VLAN
- Branch management VLAN
- Local server or guest resources

## Connectivity

- HQ Internet edge and firewall
- Branch router/firewall connecting to HQ over WAN
- Internal routing with OSPF or static routes depending on scale
- Optional VPN tunnels for secure inter-site connectivity

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

## Security Controls

- ACLs on interfaces
- Zone-based firewall policies
- VLAN segmentation
- SSH and secure management access
- Logging to central monitoring

## Future Considerations

- SD-WAN or dual WAN connectivity
- 802.1X for endpoint access
- IDS/IPS deployment
- Multi-factor authentication for admins
