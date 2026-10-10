# Secure Enterprise Network Lab — HQ + Branch

A Cisco Packet Tracer enterprise-network design project. The goal is a consistent, documented architecture before the lab is built.

**Current state:** The documentation baseline is being reconciled on the audit branch. The Packet Tracer simulation has not yet been built. No connectivity, security, VPN, or failover tests are claimed as passed.

## Source of truth

Start with [Canonical Design Baseline](docs/design-baseline.md). Keep the detailed [IP Addressing Plan](docs/ip-addressing.md), [Network Design and Cable Map](docs/network-design.md), [Security Policy Matrix](docs/security-controls.md), editable diagram, device configurations, and tests aligned with that baseline.

## Architecture at a glance

- HQ Layer-3 core pair with HSRP for VLANs 10, 20, 30, 40, and 901.
- Two HQ access switches dual-homed to both cores; the two core-to-core links are one LACP EtherChannel.
- HQ firewall with outside, inside/transit, and DMZ interfaces.
- DMZ VLAN 50 is behind the firewall; no HQ core SVI exists for VLAN 50.
- Branch router-on-a-stick provides gateways for VLANs 110, 120, 130, and 140.
- Planned site-to-site IPsec VPN between HQ-FW1 and BR-RTR1.
- VLAN 999 is native/unused; VLAN 998 is the unused-port parking VLAN. Neither has an IP address.

The single physical firewall-to-CORE-SW1 link is a known single point of failure. The project must not claim full end-to-end redundancy unless this is redesigned and tested.

## Repository map

| Path | Purpose | State |
|---|---|---|
| docs/design-baseline.md | Canonical architecture/addressing decisions | Working baseline |
| docs/ip-addressing.md | Detailed IP/interface register | Must match baseline |
| docs/network-design.md | Device roles, VLANs, cabling, topology | Must match baseline |
| docs/security-controls.md | Planned zone policy and control mapping | Design only |
| docs/repository-audit.md | Audit findings and remediation record | Audit log |
| diagrams/hq-branch-topology.drawio | Editable design diagram | Updated to canonical baseline |
| diagrams/hq-branch-topology.drawio.png | Exported preview | Removed while stale; regenerate after visual review |
| configs/hq/README.md | Planned HQ configuration inventory | No device configs yet |
| configs/branch/README.md | Planned branch configuration inventory | No device configs yet |
| packet-tracer/README.md | Packet Tracer build handoff | Real .pkt file not yet created |
| testing/test-results.md | Static, connectivity, security, and failover test matrix | Tests not run |

## Implementation sequence

1. Confirm the selected Packet Tracer device models and actual interface names.
2. Place devices and cable exactly as documented; update the port map everywhere if model ports differ.
3. Configure VLANs, trunks, native/parking VLANs, EtherChannel, and STP.
4. Configure HQ SVIs/HSRP and transit VLAN 901.
5. Configure firewall interfaces, routes, NAT, and DMZ policy.
6. Configure branch subinterfaces and ISP routing.
7. Configure VPN, ACLs, SSH, and AAA where supported.
8. Run every applicable test and attach evidence.
9. Update status only when evidence supports it.

## Safety and accuracy

- 203.0.113.0/24 is documentation-only address space, not a real public allocation.
- Never commit real passwords, VPN keys, private keys, or other secrets.
- Confirm feature support for the selected Packet Tracer devices.
- Distinguish designed, configured, simulated, and verified. A document or diagram does not prove a control works.
