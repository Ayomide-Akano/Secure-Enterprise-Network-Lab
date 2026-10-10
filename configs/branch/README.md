# Branch Device Configuration Handoff

No device configuration files have been created yet. Follow docs/design-baseline.md and docs/network-design.md.

Planned configurations after Packet Tracer model/interface confirmation:
- BR-RTR1: WAN 198.51.100.2/30; router-on-a-stick subinterfaces VLAN 10 (10.20.10.1/24) and VLAN 99 (10.20.99.1/24); routing, ACLs, secure management, and planned IPsec.
- BR-SW1: VLANs 10, 99, 999; router-facing trunk allows VLANs 10, 99, and 999 with native VLAN 999; management SVI 10.20.99.2/24; unused ports shut down and assigned VLAN 999.
- BR-PC1: VLAN 10 STAFF, 10.20.10.10/24, gateway 10.20.10.1.

Do not use VLANs 110–140 in this design. The agreed branch VLANs are 10 STAFF, 99 MGMT, and 999 BLACKHOLE/native.
