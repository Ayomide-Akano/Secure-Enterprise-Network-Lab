# IP Addressing Plan

## IPv4 Addressing Overview

This lab uses a structured, scalable addressing plan for HQ and Branch segments.

## Proposed Addressing

### HQ
- HQ LAN Core: 10.10.0.0/16
- Management: 10.10.10.0/24
- Users: 10.10.20.0/24
- Servers: 10.10.30.0/24
- Guest: 10.10.40.0/24
- DMZ: 10.10.50.0/24
- Loopbacks: 10.10.255.0/24

### Branch
- Branch LAN Core: 10.20.0.0/16
- Management: 10.20.10.0/24
- Users: 10.20.20.0/24
- Guest: 10.20.40.0/24
- Local services: 10.20.30.0/24
- Loopbacks: 10.20.255.0/24

### WAN
- HQ WAN: 172.16.10.0/30 or /31 subnets
- Branch WAN: 172.16.20.0/30 or /31 subnets
- Point-to-point links: 172.16.100.0/30, 172.16.101.0/30 (example)

## NAT and Internet Access

- HQ firewall uses PAT for outbound Internet traffic
- Branch may use NAT or routed access depending on design

## Design Notes

- Keep major subnets aligned to site-specific ranges
- Use summarization to simplify routing
- Reserve a small management range for devices and future growth
