# Packet Tracer Build Handoff

The actual Cisco Packet Tracer simulation has not been built yet.

Before adding a .pkt file:
1. Read docs/design-baseline.md; it is the source of truth.
2. Confirm device models and actual interface names.
3. Follow docs/network-design.md for cabling and port assignments.
4. Follow docs/ip-addressing.md for every IP, subnet, and gateway.
5. Keep the agreed VLAN roles: HQ 10 STAFF, 20 FINANCE, 30 IT-ADMIN, 40 GUEST, 50 SERVERS, 99 MGMT, 60 DMZ; VLAN 999 BLACKHOLE/native. Branch VLANs 10 STAFF, 99 MGMT, 999 BLACKHOLE/native.
6. Save the genuine Packet Tracer project here as enterprise-network.pkt.
7. Reopen the saved project to confirm it loads, then capture evidence and update testing/test-results.md.

Do not create a text file with a .pkt extension. Only a genuine Packet Tracer project belongs here.
