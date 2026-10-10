# Security Controls

## Security Objectives

Protect the enterprise network from unauthorized access, lateral movement, and external exploitation while maintaining connectivity for legitimate users and services.

## Recommended Controls

### Perimeter Security
- Next-generation firewall at HQ
- Branch firewall or edge security appliance
- Port filtering and stateful inspection

### Internal Segmentation
- VLAN separation between user, server, management, and guest traffic
- ACL enforcement between trust zones
- Restrictive default-deny policies

### Remote Access and VPN
- IPsec VPN for HQ-to-Branch connectivity
- Secure remote administration via SSH and VPN
- MFA for admin access where supported

### Logging and Monitoring
- Syslog collection
- NetFlow or equivalent telemetry
- Alerting for failed login attempts and segmentation violations

### Device Hardening
- Disable unused services
- Change default credentials
- Enforce strong password policy
- Apply security updates and patching cycles

## Baseline Policy Examples

- Allow DNS, HTTP, HTTPS from user VLANs to the Internet
- Restrict server VLAN access to approved application ports only
- Prevent guest network from reaching internal management networks
- Allow administrative access only from management subnet
