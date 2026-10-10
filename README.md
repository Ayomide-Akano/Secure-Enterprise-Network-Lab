# Secure Enterprise Network Lab — HQ + Branch

Cisco Packet Tracer enterprise network design. This repository defines the architecture before the simulation is built.

**Current state:** The agreed design has been aligned across the documentation on the audit branch. Visual review and device-model/port validation remain. The Packet Tracer simulation has not been built and no connectivity, security, VPN, or failover tests are claimed as passed.

## Source of truth

Start with [Canonical Design Baseline](docs/design-baseline.md). Keep the [IP Addressing Plan](docs/ip-addressing.md), [Network Design and Cable Map](docs/network-design.md), [Security Controls](docs/security-controls.md), diagram, configuration notes, and tests aligned with that baseline.

## Agreed VLAN design

| VLAN | Name | HQ subnet | Purpose |
|---:|---|---|---|
| 10 | STAFF | 10.10.10.0/24 | Everyday work; may reach approved servers/Internet, not FINANCE |
| 20 | FINANCE | 10.10.20.0/24 | Sensitive financial data; only specifically approved traffic |
| 30 | IT-ADMIN | 10.10.30.0/24 | Network administrators; only authorized source for HQ management VLAN |
| 40 | GUEST | 10.10.40.0/24 | Internet only; denied internal access |
| 50 | SERVERS | 10.10.50.0/24 | AAA, syslog, DHCP, DNS and other internal services |
| 99 | MGMT | 10.10.99.0/24 | Switch management and SSH; only IT-ADMIN may enter |
| 60 | DMZ | 172.16.60.0/24 | Public-facing servers on DMZ-SW1; firewall is gateway |
| 999 | BLACKHOLE/native | No subnet | Native VLAN on trunks and shutdown unused-port parking; no IP/SVI |

For HQ internal VLANs 10, 20, 30, 40, 50, and 99, .1 is the HSRP virtual gateway, .2 is CORE-SW1, and .3 is CORE-SW2. The DMZ uses the firewall gateway 172.16.60.1 and is not routed by an HQ core SVI.

## Branch design

- VLAN 10 STAFF: 10.20.10.0/24, BR-RTR1 gateway 10.20.10.1.
- VLAN 99 MGMT: 10.20.99.0/24, BR-RTR1 gateway 10.20.99.1, BR-SW1 management 10.20.99.2.
- VLAN 999 is native/blackhole with no IP subnet.
- HQ WAN: 203.0.113.0/30. Branch WAN: 198.51.100.0/30.
- HQ firewall inside uses a routed /30 to CORE-SW1: 10.10.254.0/30. CORE-SW2's default route depends on CORE-SW1; this limitation is documented.

## Repository map

| Path | Purpose | State |
|---|---|---|
| docs/design-baseline.md | Canonical architecture and assignments | Source of truth |
| docs/ip-addressing.md | Detailed address register | Must match baseline |
| docs/network-design.md | Device roles, cable map and topology | Must match baseline |
| docs/security-controls.md | Policy matrix and hardening intent | Design only |
| docs/repository-audit.md | Audit and remediation record | Audit log |
| diagrams/hq-branch-topology.drawio | Editable network diagram | Must match baseline |
| configs/hq/README.md | Planned HQ configuration inventory | No configs yet |
| configs/branch/README.md | Planned branch configuration inventory | No configs yet |
| packet-tracer/README.md | Build handoff | Real .pkt file not yet created |
| testing/test-results.md | Static, connectivity, security and failover tests | Not run |

## Build sequence

1. Confirm device models and port names.
2. Place devices and cable according to the port map.
3. Configure VLANs, access ports, trunks, native VLAN 999, unused-port shutdown, EtherChannel and STP.
4. Configure HQ SVIs/HSRP and the routed firewall link.
5. Configure firewall interfaces, routes, NAT and DMZ policy.
6. Configure branch VLANs, router subinterfaces, ISP routing and the planned VPN.
7. Apply ACLs, SSH/authentication, and other supported security controls.
8. Run the test matrix and save evidence.

Never commit real passwords, VPN secrets, private keys, or other credentials. Distinguish designed, configured, simulated, and verified; documentation alone does not prove a control works.
