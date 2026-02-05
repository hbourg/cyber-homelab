# Implementation Notes & Design Decisions

This document captures key design choices, tradeoffs, and lessons learned
while building and operating this homelab. The goal is to document *why*
specific technologies and configurations were chosen rather than simply
how to install them.

---

## Firewall & Routing (pfSense)

### Why pfSense
pfSense was selected over consumer-grade routers to allow granular control
over firewall rules, NAT behavior, DHCP, and VPN integration. This enabled
hands-on experience with stateful firewall policy design and troubleshooting.

### Virtualizing the Firewall
Running pfSense as a virtual machine inside Proxmox provided:
- Snapshot and rollback capability
- Safer experimentation with firewall rules
- Better visibility into interface assignments
- Flexibility for future network expansion

---

## DNS Filtering (Pi-hole)

### Why Pi-hole
Pi-hole enforces DNS filtering at the network level, providing protection
for all devices including mobile phones, smart TVs, and IoT devices.
It also offers centralized query logging for visibility into DNS activity.

### Key Limitations
Some services (e.g., YouTube) serve ads from the same domains as content,
making them unsuitable for DNS-based blocking. This limitation is
documented and understood.

---

## Remote Access (Tailscale)

### Initial Approach: WireGuard
Manual WireGuard configuration was initially attempted to gain experience
with modern VPN technology. However, NAT traversal complexity and mobile
network inconsistencies made reliability difficult without additional
infrastructure.

### Final Solution: Tailscale
Tailscale was adopted to provide:
- Zero-trust, identity-based access
- WireGuard encryption without port forwarding
- Reliable connectivity from restrictive networks
- Reduced operational overhead

---

## Wireless Networking

### Access Point Mode
The Netgear RAX9 operates strictly in access point mode. Routing, NAT, and
DHCP are handled exclusively by pfSense to maintain a single authoritative
network boundary.

---

## Troubleshooting Highlights
- Bridge misconfiguration caused DHCP failures (APIPA addresses)
- Incorrect authentication realms caused Proxmox login failures
- DNS failures surfaced as general connectivity issues
- Access point wiring errors prevented Wi-Fi broadcasting

Each issue reinforced the importance of layered troubleshooting and
understanding how services interact across the network stack.

---

## Summary
This homelab emphasizes defensive security principles, realistic
infrastructure design, and practical troubleshooting. All changes are
tested, documented, and iterated on to reflect real-world conditions.
