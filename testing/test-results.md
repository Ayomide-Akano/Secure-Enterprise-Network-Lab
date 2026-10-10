# Test Plan and Results

**Current phase:** Pre-build design consistency. The Packet Tracer simulation has not been built. No connectivity, security, VPN, or failover tests are claimed as passed.

## Recording rules

For every test, record date/time, Packet Tracer version/file, devices/interfaces, command or action, expected result, observed result, status, and evidence path. Leave tests Not run until executed. If a feature is unsupported, mark Blocked/Unsupported and explain the limitation.

## Static design checks

| ID | Check | Expected result | Status |
|---|---|---|---|
| DES-01 | VLAN IDs/names across baseline, IP plan, network design, and diagram | HQ VLANs 10 STAFF, 20 FINANCE, 30 IT-ADMIN, 40 GUEST, 50 SERVERS, 99 MGMT, 60 DMZ, 999 BLACKHOLE/native; branch VLANs 10 STAFF, 99 MGMT, 999 BLACKHOLE/native | Pending |
| DES-02 | HQ internal subnets/gateways | VLANs 10/20/30/40/50/99 use 10.10.<VLAN>.0/24 and HSRP .1; core SVIs .2/.3 | Pending |
| DES-03 | DMZ separation | VLAN 60 uses 172.16.60.0/24; firewall gateway .1; no core SVI and no VLAN 60 on core trunks | Pending |
| DES-04 | Branch subnets | VLAN 10 uses 10.20.10.0/24; VLAN 99 uses 10.20.99.0/24; BR-RTR1 provides .1 gateways | Pending |
| DES-05 | WAN and firewall routed link | HQ WAN 203.0.113.0/30; branch WAN 198.51.100.0/30; inside routed link 10.10.254.0/30; no overlap | Pending |
| DES-06 | Cable endpoints/port labels | Every link matches the port map and selected device models | Pending |
| DES-07 | Trunk/native policy | HQ trunks allow 10,20,30,40,50,99; branch trunk allows 10,99; native VLAN 999; VLAN 60 excluded from core trunks | Pending |
| DES-08 | Management policy and IP assignments | HQ switches use VLAN 99; only IT-ADMIN is authorized to enter HQ MGMT; branch switch uses 10.20.99.2 | Pending |
| DES-09 | Device inventory and endpoint VLANs | Every diagram device appears in inventory; each endpoint has a VLAN and address | Pending |

## Connectivity/routing tests after build

| ID | Action | Expected result | Status | Observed/evidence |
|---|---|---|---|---|
| NET-01 | HQ STAFF PC pings 10.10.10.1 | HSRP gateway responds | Not run | |
| NET-02 | HQ FINANCE PC pings 10.10.20.1 | HSRP gateway responds | Not run | |
| NET-03 | HQ IT-ADMIN PC pings 10.10.30.1 | HSRP gateway responds | Not run | |
| NET-04 | HQ core reaches HQ-FW1 inside 10.10.254.2 | Routed /30 link responds | Not run | |
| NET-05 | AAA-SRV1 pings 10.10.50.1 | Server gateway responds | Not run | |
| NET-06 | WEB-SRV1 pings 172.16.60.1 | Firewall DMZ gateway responds | Not run | |
| NET-07 | BR-PC1 pings 10.20.10.1 | Branch staff gateway responds | Not run | |
| NET-08 | BR-SW1 management reachability | 10.20.99.2 is reachable from approved management source only | Not run | |
| NET-09 | Verify HQ and branch WAN links | Separate /30s are correctly addressed | Not run | |
| NET-10 | Verify return routes before/after IPsec setup | Routing and encryption match intended policy | Not run | |

## Security tests after policy configuration

| ID | Action | Expected result | Status | Observed/evidence |
|---|---|---|---|---|
| TEST-SEC-01 | SSH to network devices from HQ IT-ADMIN VLAN 30 | Allowed with authentication | Not run | |
| TEST-SEC-02 | Attempt access to HQ management VLAN 99 from STAFF, FINANCE, GUEST, or SERVERS | Denied | Not run | |
| TEST-SEC-03 | Attempt HQ STAFF VLAN 10 to FINANCE VLAN 20 | Denied | Not run | |
| TEST-SEC-04 | Test approved FINANCE services | Approved services work; unapproved traffic is denied | Not run | |
| TEST-SEC-05 | Attempt HQ guest access to internal subnets | Denied | Not run | |
| TEST-SEC-06 | Test approved guest Internet services | Approved services work; other traffic is denied | Not run | |
| TEST-SEC-07 | Access AAA-SRV1 on required service ports | Approved services work; unrelated ports are blocked | Not run | |
| TEST-SEC-08 | Attempt outside access to WEB-SRV1 | Only explicitly published HTTP/HTTPS ports are allowed | Not run | |
| TEST-SEC-09 | Attempt DMZ-to-HQ internal access | Denied by default | Not run | |
| TEST-SEC-10 | Attempt branch STAFF access to branch MGMT | Denied | Not run | |
| TEST-SEC-11 | Test approved and unapproved branch-to-HQ flows | Only approved flows through configured VPN succeed | Not run | |
| TEST-SEC-12 | Attempt device management from untrusted WAN | Denied | Not run | |

## Redundancy and Layer 2 tests after build

| ID | Action | Expected result | Status | Observed/evidence |
|---|---|---|---|---|
| HA-01 | Inspect HQ core LACP EtherChannel | Both links bundled in one channel | Not run | |
| HA-02 | Inspect STP topology | No Layer-2 loop; root/blocked ports match intended design | Not run | |
| HA-03 | Disable active HSRP core/SVI in controlled test | HSRP role changes and local gateway connectivity recovers | Not run | |
| HA-04 | Disconnect one access uplink | Connectivity remains through surviving path where topology permits | Not run | |
| HA-05 | Disconnect HQ-CORE-SW1 or its firewall link | External routing is impaired as documented; this is a known design dependency | Not run | |

## Evidence template

- Test ID:
- Date/time:
- Packet Tracer version and file:
- Devices/interfaces:
- Action/command:
- Expected result:
- Observed result:
- Status: Pass / Fail / Blocked / Not run
- Evidence path:
- Notes/remediation:

## Current implementation status

- Canonical VLAN/IP baseline: updated to the user's agreed design.
- Packet Tracer topology: not built.
- Device configurations: not created/verified.
- Connectivity, security, VPN, and failover tests: not run.
