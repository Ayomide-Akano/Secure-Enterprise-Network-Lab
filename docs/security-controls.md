# Security Controls and Policy Matrix

**Status:** Planned controls only. The Packet Tracer simulation has not yet been built or tested. VLANs, subnets, and gateways must match docs/design-baseline.md.

## Security zones

| Zone | VLAN/subnet | Gateway/control point |
|---|---|---|
| HQ STAFF | VLAN 10, 10.10.10.0/24 | HSRP 10.10.10.1 |
| HQ FINANCE | VLAN 20, 10.10.20.0/24 | HSRP 10.10.20.1 |
| HQ IT-ADMIN | VLAN 30, 10.10.30.0/24 | HSRP 10.10.30.1 |
| HQ GUEST | VLAN 40, 10.10.40.0/24 | HSRP 10.10.40.1 |
| HQ SERVERS | VLAN 50, 10.10.50.0/24 | HSRP 10.10.50.1 |
| HQ MGMT | VLAN 99, 10.10.99.0/24 | HSRP 10.10.99.1 |
| HQ DMZ | VLAN 60, 172.16.60.0/24 | HQ-FW1 172.16.60.1 |
| Branch STAFF | VLAN 10, 10.20.10.0/24 | BR-RTR1 10.20.10.1 |
| Branch MGMT | VLAN 99, 10.20.99.0/24 | BR-RTR1 10.20.99.1 |
| Native/blackhole | VLAN 999, no subnet | No SVI/IP; native trunk VLAN and unused-port parking |

## Policy matrix

| ID | Source | Destination | Service | Decision | Enforcement point | Test |
|---|---|---|---|---|---|---|
| SEC-01 | HQ IT-ADMIN VLAN 30 | HQ management VLAN 99 and network-device management | SSH with authentication | Allow | Core ACLs, VTY ACL and device authentication | TEST-SEC-01 |
| SEC-02 | Any HQ source except IT-ADMIN VLAN 30 | HQ management VLAN 99 | Device management traffic | Deny | Core ACLs and VTY ACLs | TEST-SEC-02 |
| SEC-03 | HQ STAFF VLAN 10 | HQ FINANCE VLAN 20 | Any | Deny | Core ACLs | TEST-SEC-03 |
| SEC-04 | HQ FINANCE VLAN 20 | Internal services | Only explicitly approved business services | Allow narrowly; deny other traffic | Core ACLs | TEST-SEC-04 |
| SEC-05 | HQ GUEST VLAN 40 | All internal HQ subnets | Any | Deny | Core ACLs | TEST-SEC-05 |
| SEC-06 | HQ GUEST VLAN 40 | Approved external destinations | DNS/HTTP/HTTPS as required | Allow narrowly | Core ACL/egress policy | TEST-SEC-06 |
| SEC-07 | Approved clients | AAA-SRV1 10.10.50.10 | Required AAA/service ports only | Allow narrowly | Core ACLs | TEST-SEC-07 |
| SEC-08 | Internet/outside | WEB-SRV1 172.16.60.10 | HTTP/HTTPS only if publishing is enabled | Allow published ports only; deny other inbound | HQ-FW1 policy and optional static NAT | TEST-SEC-08 |
| SEC-09 | DMZ 172.16.60.0/24 | HQ internal subnets | Any | Deny by default | HQ-FW1 DMZ-to-inside policy | TEST-SEC-09 |
| SEC-10 | Branch STAFF VLAN 10 | Branch MGMT VLAN 99 | Device management | Deny | BR-RTR1 ACL | TEST-SEC-10 |
| SEC-11 | Approved branch clients | Approved HQ services through IPsec | Explicit application ports | Allow narrowly after VPN setup | BR-RTR1 ACL/VPN and HQ-FW1 policy | TEST-SEC-11 |
| SEC-12 | Untrusted WAN | Device management interfaces | Any | Deny | Firewall/router ACLs | TEST-SEC-12 |

## Layer 2 and device hardening

- Use SSH, not Telnet, for device administration.
- Only HQ IT-ADMIN VLAN 30 may initiate management access to HQ management VLAN 99. Branch management access must be explicitly controlled and must not be implicitly allowed from branch STAFF.
- Set native VLAN 999 on trunks and explicitly restrict each trunk's allowed VLANs. HQ core-to-core and core-to-access trunks carry VLANs 10,20,30,40,50,99; branch router-to-switch trunk carries VLANs 10,99. Do not carry VLAN 60 on HQ core trunks.
- Administratively shut down unused access ports and assign them to VLAN 999. VLAN 999 has no IP subnet or SVI.
- DMZ-SW1's management connection is VLAN 99 only; the firewall-facing DMZ segment is VLAN 60 only. Do not bridge VLAN 60 into the HQ core.
- Enable port security, DHCP snooping, Dynamic ARP Inspection, and secure STP settings only where the selected Packet Tracer devices support them.
- Configure AAA and logging only after the required server services are configured and reachable; retain a safe local recovery method.
- Never commit real credentials, VPN secrets, or private keys.

Inter-VLAN routing alone does not enforce least privilege. Apply ACLs at the appropriate routed interfaces/SVIs and the firewall, and test both allowed and denied flows. MFA, IDS/IPS, NetFlow, and centralized monitoring remain optional/future features unless supported and tested.
