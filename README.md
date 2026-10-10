# Secure-Enterprise-Network-Lab

Secure Enterprise Network (HQ + Branch) - A networking lab and documentation repository for a secure enterprise architecture spanning a headquarters site and a branch office.

## Overview

This repository is designed to model and document a realistic enterprise network environment with:

- Headquarters (HQ) core services and security controls
- Branch office connectivity and local user access
- Segmented VLAN design and secure inter-site communication
- IP addressing and routing strategy
- Firewall, access control, and security hardening guidance
- Packet Tracer lab structure for practical validation

## Project Goals

- Design a secure and scalable enterprise network
- Separate user, management, server, and guest traffic
- Enforce least-privilege policies and segmentation
- Document HQ and branch site connectivity
- Provide a foundation for Packet Tracer and networking lab exercises

## Repository Structure

- `docs/` – design documentation, addressing plan, and security controls
- `diagrams/` – architecture diagrams and exported visuals
- `configs/` – device configuration examples for HQ and branch locations
- `packet-tracer/` – Packet Tracer project assets
- `testing/` – validation and test results documentation

## High-Level Architecture

- HQ: central internet edge, firewall, routing, switching, and core services
- Branch: remote site with local users, segmentation, and secure WAN connectivity
- WAN: site-to-site connectivity between HQ and branch
- Security boundary: firewall policies, ACLs, segmentation, logging, and hardening

## Documentation

The `docs/` folder includes:

- `network-design.md` – enterprise network architecture and connectivity model
- `ip-addressing.md` – IP addressing plan for HQ, branch, and WAN
- `security-controls.md` – firewall, segmentation, access control, and device hardening guidance

## Configurations

The `configs/` folder is intended to hold example device configurations for:

- HQ core switch
- HQ router
- HQ firewall
- Branch router
- Branch switch
- Branch firewall

## Packet Tracer

The `packet-tracer/` directory is reserved for the Cisco Packet Tracer lab representation of the secure enterprise topology.

## Testing

The `testing/` directory contains validation notes, test assumptions, and results documentation.

## Typical Use Cases

- Network design coursework
- Enterprise security lab exercises
- HQ + branch connectivity design practice
- Firewall and segmentation validation
- Packet Tracer simulation scenarios

## Notes

This repository is intended as a practical lab foundation and can be expanded with device configs, diagrams, topologies, and validation reports.

## Status

In progress – baseline architecture and documentation are in place.
