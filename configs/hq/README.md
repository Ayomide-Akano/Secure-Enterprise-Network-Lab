# HQ Device Configurations

No device configurations have been added yet. Use docs/design-baseline.md as the source of truth and docs/network-design.md for the port map.

Planned configuration files, to be added after Packet Tracer device models and interface names are confirmed:
- hq-core-sw1.txt — VLANs, SVIs, HSRP, LACP/STP, ACLs, routes, secure management.
- hq-core-sw2.txt — matching VLANs/SVIs, HSRP standby, LACP/STP, ACLs, routes.
- hq-access-switches.txt — access VLANs, trunks, native VLAN 999, parking VLAN 998, edge security.
- hq-firewall.txt — outside/inside/DMZ interfaces, routes, NAT and zone policy.
- hq-dmz-switch.txt — VLAN 50 and management SVI.
- hq-vlan-plan.txt — only if needed as a concise device-side checklist.

These files are intentionally not represented as implemented yet.
