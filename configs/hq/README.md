# HQ Device Configuration Handoff

No device configuration files have been created yet. Follow docs/design-baseline.md and docs/network-design.md.

Planned configurations after Packet Tracer model/interface confirmation:
- HQ-CORE-SW1: VLANs 10,20,30,40,50,99,999; SVIs ending .2; HSRP VIPs ending .1; LACP/STP; routed /30 to HQ-FW1 at 10.10.254.1; ACLs and secure management.
- HQ-CORE-SW2: matching SVIs ending .3; HSRP standby; LACP/STP; default route via CORE-SW1; ACLs and secure management.
- HQ access switches: access assignments per endpoint table; trunks allowing only 10,20,30,40,50,99 with native 999; management SVIs VLAN 99; unused ports shut down and assigned VLAN 999.
- HQ-FW1: outside 203.0.113.2/30, inside routed 10.10.254.2/30, DMZ 172.16.60.1/24; routes, NAT, zone policy and planned IPsec.
- DMZ-SW1: VLAN 60 for DMZ endpoints, VLAN 99 management at 10.10.99.12; firewall-facing access port VLAN 60 and a separate management-only VLAN 99 access uplink. Never bridge VLAN 60 to the core.
