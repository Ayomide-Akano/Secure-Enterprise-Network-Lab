# Security Controls and Policy Matrix

**Status:** Planned security baseline only. No control is claimed as configured or tested. Addressing must match docs/design-baseline.md.

## Zones

| Zone | Subnet/VLAN | Gateway/control point |
|---|---|---|
| HQ management | VLAN 10, 10.10.10.0/24 | Core HSRP 10.10.10.1 |
| HQ users | VLAN 20, 10.10.20.0/24 | Core HSRP 10.10.20.1 |
| HQ servers | VLAN 30, 10.10.30.0/24 | Core HSRP 10.10.30.1 |
| HQ guest | VLAN 40, 10.10.40.0/24 | Core HSRP 10.10.40.1 and ACL |
| HQ DMZ | VLAN 50, 10.10.50.0/24 | HQ-FW1 10.10.50.1 |
| HQ firewall transit | VLAN 901, 10.10.1.0/24 | Core HSRP 10.10.1.1; firewall 10.10.1.254 |
| Branch management | VLAN 110, 10.20.10.0/24 | BR-RTR1 10.20.10.1 |
| Branch users | VLAN 120, 10.20.20.0/24 | BR-RTR1 10.20.20.1 |
| Branch services | VLAN 130, 10.20.30.0/24 | BR-RTR1 10.20.30.1 |
| Branch guest | VLAN 140, 10.20.40.0/24 | BR-RTR1 10.20.40.1 |

VLAN 999 is native/unused and VLAN 998 is for unused access ports at both HQ and the branch. Neither has an IP or SVI. PC-FIN1 is in HQ users VLAN 20; no separate Finance VLAN exists.

## Policy matrix

| ID | Source | Destination | Service | Decision | Enforcement point | Test |
|---|---|---|---|---|---|---|
| SEC-01 | HQ management VLAN 10 | Network devices | SSH with authentication | Allow | VTY ACL and device AAA/local authentication | TEST-SEC-01 |
| SEC-02 | HQ users VLAN 20 | HQ management VLAN 10 | Any | Deny by default | Core ACLs | TEST-SEC-02 |
| SEC-03 | HQ guest VLAN 40 | HQ internal subnets | Any | Deny | Core ACLs | TEST-SEC-03 |
| SEC-04 | HQ guest VLAN 40 | Approved external destinations | DNS/HTTP/HTTPS only as required | Allow narrowly | Core ACL/egress policy | TEST-SEC-04 |
| SEC-05 | Internet/outside | WEB-SRV1 10.10.50.20 | HTTP/HTTPS only if publishing enabled | Allow published ports only; deny other inbound | HQ-FW1 policy and optional static NAT | TEST-SEC-05 |
| SEC-06 | DMZ 10.10.50.0/24 | HQ internal subnets | Any | Deny by default | HQ-FW1 DMZ-to-inside policy | TEST-SEC-06 |
| SEC-07 | Approved clients | AAA-SRV1 10.10.30.10 | Required AAA/service ports only | Allow narrowly | Core ACLs | TEST-SEC-07 |
| SEC-08 | Branch guest VLAN 140 | Branch internal subnets | Any | Deny | BR-RTR1 ACL | TEST-SEC-08 |
| SEC-09 | Branch VLANs | HQ subnets | Explicit approved applications through VPN | Allow narrowly after VPN is configured | BR-RTR1 ACL/VPN and HQ-FW1 policy | TEST-SEC-09 |
| SEC-10 | Untrusted WAN | Device management interfaces | Any | Deny | Firewall/router ACLs | TEST-SEC-10 |

## Hardening baseline

- Use SSH, not Telnet, for device administration.
- Restrict administration to the HQ management subnet and explicitly approved branch management sources.
- Shut down unused access ports and assign them to VLAN 998.
- Configure trunks explicitly with only required allowed VLANs: core-to-core 10,20,30,40,901; HQ core-to-access 10,20,30,40; branch router-to-switch 110,120,130,140. Use native VLAN 999, park unused access ports in VLAN 998, and do not place user endpoints in VLAN 1. Never carry DMZ VLAN 50 on HQ core trunks.
- Enable port security, DHCP snooping, Dynamic ARP Inspection, and secure STP settings only where supported by selected Packet Tracer models.
- Configure logging and AAA only when the destination/service is configured and reachable; retain a safe local recovery method.
- Never commit real credentials, VPN secrets, or private keys.
- MFA, IDS/IPS, NetFlow, and centralized monitoring remain future/optional items unless supported and tested.

Inter-VLAN routing alone does not enforce least privilege. Apply ACLs to the correct routed interfaces/SVIs and test permitted and prohibited traffic in both directions. The test cases live in testing/test-results.md.
