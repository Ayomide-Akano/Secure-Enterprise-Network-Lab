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
