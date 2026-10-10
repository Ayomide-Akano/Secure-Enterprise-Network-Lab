# Repository Audit and Design Correction Log

**Repository:** Ayomide-Akano/Secure-Enterprise-Network-Lab  
**Working branch:** audit/architecture-and-repository-baseline  
**Scope:** Design baseline, IP register, network/cabling plan, security policy, tests, diagrams, configuration handoff files, and Packet Tracer placeholder.

## User-approved design correction

The previous baseline incorrectly assigned VLAN 50 to the DMZ, treated VLAN 99 as native, added VLAN 901 as firewall transit, and invented branch VLANs 110–140. These decisions did not match the user's agreed project text and are superseded.

The corrected design is:

- HQ VLAN 10 STAFF — 10.10.10.0/24.
- HQ VLAN 20 FINANCE — 10.10.20.0/24.
- HQ VLAN 30 IT-ADMIN — 10.10.30.0/24.
- HQ VLAN 40 GUEST — 10.10.40.0/24.
- HQ VLAN 50 SERVERS — 10.10.50.0/24.
- HQ VLAN 99 MGMT — 10.10.99.0/24.
- VLAN 999 BLACKHOLE/native — no IP subnet or SVI; native on trunks and shutdown unused-port parking.
- VLAN 60 DMZ — 172.16.60.0/24, present on DMZ-SW1 only, with HQ-FW1 gateway 172.16.60.1.
- Branch VLAN 10 STAFF — 10.20.10.0/24.
- Branch VLAN 99 MGMT — 10.20.99.0/24.
- HQ WAN — 203.0.113.0/30.
- Branch WAN — 198.51.100.0/30.
- HQ firewall inside link — routed point-to-point 10.10.254.0/30, not an extra VLAN.

For HQ internal VLANs 10,20,30,40,50,99, the HSRP VIP is .1, CORE-SW1 is .2, CORE-SW2 is .3. STAFF cannot reach FINANCE; FINANCE is restricted; IT-ADMIN is the only authorized source for HQ management; GUEST is Internet-only; SERVERS are limited to required service ports; DMZ-to-internal access is denied by default.

## Remediation performed

- Replaced docs/design-baseline.md with the user's agreed VLAN roles, subnets, HSRP numbering pattern, DMZ placement, branch addressing, WAN links, security intent, and routing limitation.
- Rewrote docs/ip-addressing.md to remove VLAN 901 and branch VLANs 110–140 and use the agreed address ranges.
- Rewrote docs/network-design.md with corrected device roles, endpoint VLANs, trunk policy, routed firewall link, DMZ isolation, branch VLANs, and cable map.
- Rewrote docs/security-controls.md to reflect the agreed STAFF/FINANCE/IT-ADMIN/GUEST/SERVERS/MGMT/DMZ security boundaries.
- Rewrote testing/test-results.md with corrected static and functional test expectations; tests remain unrun until the Packet Tracer simulation exists.
- Updated README.md to summarize the agreed design and direct readers to the canonical baseline.

## Explicit limitations / pending work

- Confirm Packet Tracer device models and actual interface names before cabling.
- Update the Draw.io source to match this corrected design and visually inspect it.
- Add a real Packet Tracer .pkt file after the user builds the lab.
- Add actual device configurations only after model/feature support is confirmed.
- Run tests and attach evidence; do not mark any test passed before execution.
- CORE-SW2 uses CORE-SW1 as its default next hop, and the firewall's routed inside link terminates at CORE-SW1. External routing is therefore not fully redundant if CORE-SW1 or its firewall link fails.

## Verification status

The documentation baseline and detailed design files are being corrected. Packet Tracer has not been built, so no simulation behavior is verified. No VPN, ACL, NAT, HSRP, EtherChannel, STP, AAA, or failover behavior is claimed as tested.
