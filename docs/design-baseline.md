# Canonical Design Baseline

**Status:** Pre-build design. The Packet Tracer simulation has not been built or tested.

This is the single source of truth. If another file conflicts with this baseline, update that file. Do not invent additional VLANs or change subnet assignments without first recording an approved change here.

## Architecture

- HQ uses two Layer-3 core switches and HSRP for internal VLANs 10, 20, 30, 40, 50, and 99. The virtual gateway in each HQ internal subnet is .1; CORE-SW1 uses .2; CORE-SW2 uses .3.
- HQ VLAN roles: 10 STAFF, 20 FINANCE, 30 IT-ADMIN, 40 GUEST, 50 SERVERS, 99 MGMT, 999 BLACKHOLE/native.
- The DMZ is VLAN 60 using 172.16.60.0/24. It exists on DMZ-SW1 only; HQ-FW1 is its gateway. VLAN 60 is not carried on HQ core trunks.
- VLAN 999 has no IP subnet or SVI. It is the native VLAN on trunks and the parking VLAN for unused access ports, which must also be administratively shut down.
- HQ-FW1 outside connects to ISP-RTR on 203.0.113.0/30. BR-RTR1 connects to ISP-RTR on 198.51.100.0/30.
- Branch VLAN 10 is staff on 10.20.10.0/24. Branch VLAN 99 is management on 10.20.99.0/24. VLAN 999 is native/blackhole with no IP.
- The firewall-to-core connection is a routed point-to-point link, not an extra VLAN: CORE-SW1 10.10.254.1/30 and HQ-FW1 inside 10.10.254.2/30. CORE-SW1 routes to the firewall; CORE-SW2 uses CORE-SW1 as its default next hop. This is a known dependency and means external routing is not fully redundant if CORE-SW1 fails.
- The planned site-to-site IPsec VPN peers are HQ-FW1 outside and BR-RTR1 WAN. VPN operation remains unverified until the simulation is built and tested.

## Canonical VLAN and subnet map

| Site | VLAN | Name | Subnet | Gateway | Gateway owner |
|---|---:|---|---|---|---|
| HQ | 10 | STAFF | 10.10.10.0/24 | 10.10.10.1 | HSRP on HQ cores |
| HQ | 20 | FINANCE | 10.10.20.0/24 | 10.10.20.1 | HSRP on HQ cores |
| HQ | 30 | IT-ADMIN | 10.10.30.0/24 | 10.10.30.1 | HSRP on HQ cores |
| HQ | 40 | GUEST | 10.10.40.0/24 | 10.10.40.1 | HSRP on HQ cores |
| HQ | 50 | SERVERS | 10.10.50.0/24 | 10.10.50.1 | HSRP on HQ cores |
| HQ | 99 | MGMT | 10.10.99.0/24 | 10.10.99.1 | HSRP on HQ cores |
| HQ | 60 | DMZ | 172.16.60.0/24 | 172.16.60.1 | HQ-FW1 |
| HQ | 999 | BLACKHOLE/native | No IP subnet | None | No SVI |
| Branch | 10 | STAFF | 10.20.10.0/24 | 10.20.10.1 | BR-RTR1 |
| Branch | 99 | MGMT | 10.20.99.0/24 | 10.20.99.1 | BR-RTR1 |
| Branch | 999 | BLACKHOLE/native | No IP subnet | None | No SVI |

## HQ IP assignments

| Device | Interface/VLAN | Address | Purpose |
|---|---|---|---|
| HQ-CORE-SW1 | SVI VLAN 10 | 10.10.10.2/24 | STAFF SVI |
| HQ-CORE-SW2 | SVI VLAN 10 | 10.10.10.3/24 | STAFF SVI |
| HQ-CORE-SW1 | SVI VLAN 20 | 10.10.20.2/24 | FINANCE SVI |
| HQ-CORE-SW2 | SVI VLAN 20 | 10.10.20.3/24 | FINANCE SVI |
| HQ-CORE-SW1 | SVI VLAN 30 | 10.10.30.2/24 | IT-ADMIN SVI |
| HQ-CORE-SW2 | SVI VLAN 30 | 10.10.30.3/24 | IT-ADMIN SVI |
| HQ-CORE-SW1 | SVI VLAN 40 | 10.10.40.2/24 | GUEST SVI |
| HQ-CORE-SW2 | SVI VLAN 40 | 10.10.40.3/24 | GUEST SVI |
| HQ-CORE-SW1 | SVI VLAN 50 | 10.10.50.2/24 | SERVERS SVI |
| HQ-CORE-SW2 | SVI VLAN 50 | 10.10.50.3/24 | SERVERS SVI |
| HQ-CORE-SW1 | SVI VLAN 99 | 10.10.99.2/24 | MGMT SVI |
| HQ-CORE-SW2 | SVI VLAN 99 | 10.10.99.3/24 | MGMT SVI |
| HQ-ACC-SW1 | SVI VLAN 99 | 10.10.99.10/24 | Switch management |
| HQ-ACC-SW2 | SVI VLAN 99 | 10.10.99.11/24 | Switch management |
| DMZ-SW1 | SVI VLAN 99 | 10.10.99.12/24 | Management only; DMZ port group remains VLAN 60 |
| AAA-SRV1 | NIC, VLAN 50 | 10.10.50.10/24 | Internal services |
| HQ-FW1 | Outside | 203.0.113.2/30 | ISP peer 203.0.113.1 |
| ISP-RTR | HQ-facing interface | 203.0.113.1/30 | HQ WAN peer |
| HQ-FW1 | Inside routed link | 10.10.254.2/30 | Core peer 10.10.254.1 |
| HQ-CORE-SW1 | Routed link to firewall | 10.10.254.1/30 | Firewall next hop 10.10.254.2 |
| HQ-FW1 | DMZ interface | 172.16.60.1/24 | DMZ gateway |
| WEB-SRV1 | NIC, VLAN 60 | 172.16.60.10/24 | DMZ web server; gateway 172.16.60.1 |

The HQ internal subnet numbering rule is consistent across VLANs 10, 20, 30, 40, 50, and 99: .1 is the HSRP virtual gateway, .2 is CORE-SW1, and .3 is CORE-SW2. Use .10–.49 for planned static endpoints where possible, then allocate DHCP pools outside the static range. The DMZ is a separate subnet and uses the firewall gateway, not the HQ HSRP pattern.

## Branch and WAN IP assignments

| Device | Interface/VLAN | Address | Purpose |
|---|---|---|---|
| BR-RTR1 | WAN | 198.51.100.2/30 | ISP peer 198.51.100.1 |
| ISP-RTR | Branch-facing interface | 198.51.100.1/30 | Branch WAN peer |
| BR-RTR1 | Subinterface VLAN 10 | 10.20.10.1/24 | Branch staff gateway |
| BR-RTR1 | Subinterface VLAN 99 | 10.20.99.1/24 | Branch management gateway |
| BR-SW1 | SVI VLAN 99 | 10.20.99.2/24 | Switch management |
| BR-PC1 | NIC, VLAN 10 | 10.20.10.10/24 | Staff workstation; gateway 10.20.10.1 |

WAN links are separate: HQ 203.0.113.0/30 uses .1 and .2; branch 198.51.100.0/30 uses .1 and .2. These are documentation/test address ranges, not real public allocations.

## Trunk and cabling rules

- HQ core-to-core LACP EtherChannel: allow VLANs 10, 20, 30, 40, 50, 99, and 999; native VLAN 999.
- HQ core-to-access trunks: allow VLANs 10, 20, 30, 40, 50, 99, and 999; native VLAN 999.
- The HQ firewall inside interface uses a routed /30 link to CORE-SW1, not an access port in a fabricated transit VLAN.
- DMZ-SW1 connects to HQ-FW1 on an access port in VLAN 60. A separate management-only access link/port connects DMZ-SW1 to HQ management VLAN 99. VLAN 60 must not traverse that management uplink.
- Branch router-to-switch trunk: allow VLANs 10, 99, and 999; native VLAN 999.
- VLAN 999 is not assigned an IP address or SVI. Shut down unused ports and park them in VLAN 999.

Port numbers are planned labels and must be checked against the chosen Packet Tracer models before cabling. If a model uses different interfaces, update the port map and diagram together.

## Security intent

- STAFF VLAN 10 may reach approved internal server services and the Internet, but not FINANCE VLAN 20.
- FINANCE VLAN 20 is highly restricted; allow only explicitly approved traffic.
- IT-ADMIN VLAN 30 may access management VLAN 99. Other source VLANs must not access VLAN 99.
- GUEST VLAN 40 is Internet-only and denied access to all internal subnets.
- SERVERS VLAN 50 exposes only required service ports.
- DMZ VLAN 60 is behind HQ-FW1. DMZ-to-internal access is denied by default; inbound publishing is limited to approved web ports.
- Branch STAFF and MGMT are separate; branch-to-HQ access is only for explicitly approved applications and should use IPsec once configured.
- Use SSH, authentication, least-privilege ACLs, secure trunks, port security, DHCP snooping/DAI and secure STP only where supported by the selected Packet Tracer devices.

## Known limitations and change control

The routed firewall link is connected to CORE-SW1 only; CORE-SW2 depends on CORE-SW1 for its default route. HQ-FW1 is also a single firewall. Do not claim full end-to-end redundancy. Before changing any VLAN, subnet, gateway, interface, cable, or policy, update this baseline first, then the IP plan, network design, diagram, security matrix, configuration notes, and test plan. No configuration or test is considered complete until built and evidenced in Packet Tracer.
