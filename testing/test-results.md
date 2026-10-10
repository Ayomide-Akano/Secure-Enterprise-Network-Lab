# Test Plan and Results

**Current phase:** Pre-build design consistency. The Packet Tracer topology has not yet been built. No connectivity, security, or failover tests are claimed as passed.

## Recording rules

For each test, record date/time, Packet Tracer version/file, devices/interfaces, action or command, expected result, observed result, status, and evidence path. Leave status Not run until executed. If a feature is unsupported, mark it Blocked/Unsupported and describe the limitation.

## Static design checks

| ID | Check | Expected result | Status |
|---|---|---|---|
| DES-01 | VLAN IDs/names across baseline, IP plan, design, diagram | Exact match | Pending |
| DES-02 | Subnets, masks, gateways, interface IPs | No overlap; gateway ownership matches routing model | Pending |
| DES-03 | Cable endpoints and port names | Each link matches the port map and selected device models | Pending |
| DES-04 | Trunk/native VLAN plan | Native VLAN 999; only required VLANs carried | Pending |
| DES-05 | Device inventory/endpoints | Every diagram device appears in inventory and every endpoint has a VLAN/zone | Pending |
| DES-06 | DMZ architecture | VLAN 50 is behind HQ-FW1; no core SVI for VLAN 50 | Pending |
| DES-07 | Branch routing model | BR-RTR1 subinterfaces are gateways; BR-SW1 only has VLAN 110 management SVI | Pending |

## Connectivity/routing tests after build

| ID | Action | Expected result | Status | Observed/evidence |
|---|---|---|---|---|
| NET-01 | HQ endpoint pings its default gateway | Gateway responds | Not run | |
| NET-02 | Test approved HQ user-to-server flows | Only approved services succeed | Not run | |
| NET-03 | Ping HQ-FW1 inside 10.10.1.254 from core | Reachable over VLAN 901 | Not run | |
| NET-04 | Ping WEB-SRV1 gateway 10.10.50.1 | Reachable | Not run | |
| NET-05 | BR-PC1 pings 10.20.20.1 | Branch gateway responds | Not run | |
| NET-06 | Verify both ISP WAN /30 links | Separate, non-overlapping subnets | Not run | |
| NET-07 | Test routes before/after IPsec setup | Route and encryption behavior matches the intended design | Not run | |
| NET-08 | Verify return routes | Replies follow an expected path | Not run | |

## Security tests after policy configuration

| ID | Action | Expected result | Status | Observed/evidence |
|---|---|---|---|---|
| TEST-SEC-01 | SSH to devices from HQ management VLAN 10 | Allowed with authentication | Not run | |
| TEST-SEC-02 | Attempt management access from HQ users VLAN 20 | Denied | Not run | |
| TEST-SEC-03 | Attempt HQ internal access from guest VLAN 40 | Denied | Not run | |
| TEST-SEC-04 | Test approved guest external services | Approved services work; others denied | Not run | |
| TEST-SEC-05 | Attempt outside access to WEB-SRV1 | Only explicitly published HTTP/HTTPS allowed | Not run | |
| TEST-SEC-06 | Attempt DMZ-to-HQ internal access | Denied by default | Not run | |
| TEST-SEC-07 | Access AAA-SRV1 on required ports | Approved service works; unrelated ports blocked | Not run | |
| TEST-SEC-08 | Attempt branch guest VLAN 140 to branch internal networks | Denied | Not run | |
| TEST-SEC-09 | Test approved/unapproved branch-to-HQ flows | Approved flows through VPN work; unapproved flows fail | Not run | |
| TEST-SEC-10 | Attempt device management from untrusted WAN | Denied | Not run | |

## Redundancy and Layer 2 tests after build

| ID | Action | Expected result | Status | Observed/evidence |
|---|---|---|---|---|
| HA-01 | Inspect HQ core LACP EtherChannel | Both links bundled in one channel | Not run | |
| HA-02 | Inspect STP topology | No L2 loop; expected root/blocked ports | Not run | |
| HA-03 | Disable active HSRP core/SVI in controlled test | HSRP role changes and gateway connectivity recovers | Not run | |
| HA-04 | Disconnect one access uplink | Connectivity remains through surviving path where topology permits | Not run | |
| HA-05 | Disconnect firewall-to-CORE-SW1 link | Firewall reachability is lost; known single point of failure is documented | Not run | |

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

- Design baseline: being reconciled.
- Packet Tracer topology: not built.
- Device configurations: not created/verified.
- Connectivity/security/failover tests: not run.
