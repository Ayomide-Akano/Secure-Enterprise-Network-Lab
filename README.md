# Secure Enterprise Network Lab — HQ + Branch

A Cisco Packet Tracer-oriented enterprise network design project focused on segmented HQ and branch connectivity, routing, perimeter security, and repeatable validation.

> **Current state:** Design documentation exists, but the repository audit has identified conflicting addressing decisions, missing device configurations, and a placeholder rather than a valid Packet Tracer project. The network must not be described as implemented or validated yet.

## Project objectives

- Design a segmented HQ and branch network.
- Document VLANs, subnets, gateways, routed links, device roles, and physical connectivity.
- Define least-privilege security boundaries between users, management, servers, guests, and DMZ services.
- Build matching device configurations and a real Packet Tracer topology.
- Validate reachability, prohibited traffic, redundancy, and security controls with recorded evidence.

## Repository layout

| Path | Purpose | Current status |
|---|---|---|
| `docs/network-design.md` | Architecture, inventory, VLANs, physical/logical topology | Draft; addressing conflicts need resolution |
| `docs/ip-addressing.md` | IP plan and interface assignments | Draft; conflicts need resolution |
| `docs/security-controls.md` | Security objectives and recommended controls | Guidance only; not implementation evidence |
| `docs/repository-audit.md` | Findings and remediation order from the repository review | Initial audit available |
| `diagrams/hq-branch-topology.drawio` | Editable Draw.io topology | Needs alignment with the canonical plan |
| `diagrams/hq-branch-topology.drawio.png` | Exported topology preview | Needs regeneration after diagram correction |
| `configs/hq/` | HQ device configurations | Not yet populated with configurations |
| `configs/branch/` | Branch device configurations | Not yet populated with configurations |
| `packet-tracer/` | Cisco Packet Tracer project | A real `.pkt` file is still required |
| `testing/test-results.md` | Test plan and results | Planned checks only; no completed evidence recorded |

## Intended architecture

The documented target includes a headquarters site and a branch site, HQ core and access switching, a perimeter firewall, an ISP edge, a DMZ web service, an AAA service, and a branch router/access switch. The design proposes VLAN segmentation, controlled inter-VLAN access, secure device management, centralized logging, and a site-to-site VPN.

These are **design intentions**, not proof that the controls are configured or operational.

## Important implementation status

| Stage | Status |
|---|---|
| Initial repository and documentation review | In progress |
| Canonical addressing/VLAN plan | Blocked by conflicting assignments |
| Reconciled topology diagram | Pending |
| HQ and branch device configurations | Pending |
| Valid Packet Tracer simulation file | Pending |
| Connectivity and routing tests | Pending |
| Security negative tests and segmentation validation | Pending |
| Redundancy/failover tests | Pending |
| Evidence-backed final documentation | Pending |

## Audit and next steps

Read [the initial repository audit](docs/repository-audit.md). The next priority is to approve one consistent IP/VLAN/interface plan before producing device configurations. This avoids embedding contradictory gateway and subnet assignments in the lab.

Testing must record the action or command, expected result, observed result, pass/fail outcome, and evidence. A planned control must not be marked as passed until it has been tested.

## Lab assumptions and safety

- Documentation-only address ranges (for example, `203.0.113.0/24`) are examples and are not real public allocations for production use.
- Use unique credentials in the lab; never commit real passwords, shared secrets, VPN keys, or other sensitive credentials.
- Confirm feature support for the exact Packet Tracer device models before treating a configuration example as executable.
- This is a learning lab, not a production-ready reference architecture.

## Contribution workflow

1. Reconcile the addressing and VLAN plan.
2. Update the design document and editable diagram from the same baseline.
3. Add device-specific configuration examples.
4. Build and open the actual Packet Tracer project.
5. Run tests and record evidence.
6. Update status claims only after verification.
