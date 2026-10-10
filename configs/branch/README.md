# Branch Device Configurations

No device configurations have been added yet. Use docs/design-baseline.md as the source of truth and docs/network-design.md for the port map.

Planned configuration files, to be added after Packet Tracer device models and interface names are confirmed:
- branch-router.txt — WAN interface, VLAN 110/120/130/140 subinterfaces, routing, ACLs, planned IPsec VPN, secure management.
- branch-switch.txt — VLANs, router-on-a-stick trunk, management SVI VLAN 110, unused-port hardening.
- branch-vlan-plan.txt — only if needed as a concise device-side checklist.

BR-RTR1 subinterfaces provide the branch VLAN gateways. BR-SW1 must not be assigned gateway SVIs for VLANs 120, 130, or 140.
