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

### VLAN Configuration
pfSense manages VLAN segmentation internally using its VLAN interface feature.
The pfSense LAN NIC (vtnet1) acts as a trunk port receiving tagged frames from
the Proxmox bridge. pfSense's internal VLAN interfaces (e.g. VLAN20_SERVERS)
process tagged traffic and enforce firewall rules per segment.

Key lesson: the pfSense LAN interface upstream gateway must be set to **None**
for internal VLAN interfaces. Setting a gateway causes pfSense to treat the
interface as WAN-type, breaking routing for internal subnets.

---

## DNS Filtering (Pi-hole)

### Why Pi-hole
Pi-hole enforces DNS filtering at the network level, providing protection
for all devices including mobile phones, smart TVs, and IoT devices.
It also offers centralized query logging for visibility into DNS activity.

### Tailscale DNS Integration
Pi-hole is configured as the global nameserver in Tailscale's admin panel
with DNS override enabled. This extends ad blocking and DNS filtering to
all devices on the Tailscale network, regardless of physical location.

Pi-hole interface setting changed to **Permit all origins** to accept queries
from the Tailscale IP range (100.64.0.0/10). This is safe because pfSense
blocks all inbound WAN traffic and port 53 is not forwarded externally.

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

### Subnet Routing
Tailscale is installed on the Plex LXC (CT 104) and advertises the
`192.168.20.0/24` subnet. This allows any Tailscale-connected device to
reach all VLAN 20 services without requiring Tailscale to be installed
on every device in the subnet. The route is approved via the Tailscale
admin panel.

---

## Storage (ZFS + Directory)

### Design Decision: Separate Config and Media Storage
Plex config/metadata and media files are stored on separate pools intentionally:

- **ZFS Mirror (media-mirro):** Two identical 1TB HDDs (ST1000DM003) in a mirror
  configuration. Provides redundancy for Plex's database, metadata, and watch
  history — data that is difficult to recreate. ZFS checksumming also protects
  against silent data corruption.

- **Directory (media-storage):** Single 4TB HDD for the media library. Media
  files are replaceable; redundancy here is lower priority. A second 4TB drive
  can be added in future to mirror this pool as well.

- **SSD (local-lvm):** Proxmox OS, VMs, and LXC root disks remain on the SSD
  for performance. Containers boot and run from SSD while accessing bulk storage
  via mount points.

### Key Lesson: RAID is Not a Backup
ZFS mirroring protects against drive failure but not accidental deletion,
ransomware, or file corruption. A separate backup strategy should be
implemented in future iterations.

---

## Plex Media Server (LXC Container)

### Why LXC over VM
An LXC container was chosen over a full VM for Plex because:
- Lower overhead (no separate kernel, less RAM usage)
- Faster startup and simpler management
- Easy storage mount point configuration in Proxmox
- Sufficient isolation for a media service on a trusted VLAN

### Network Configuration
Plex runs on VLAN 20 (Server LAN) at a static IP (192.168.20.54) outside
the DHCP pool range. Static IPs for servers are assigned below .100 to
avoid conflicts with DHCP leases.

### Proxmox Firewall Disabled on LXC NIC
The Proxmox firewall is intentionally disabled on the Plex LXC's network
interface. See the troubleshooting scenario for full details. pfSense's
VLAN20_SERVERS firewall rules provide all necessary access control, making
the Proxmox firewall redundant and in this case harmful to connectivity.

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
- Proxmox firewall bridge (fwbr) silently dropped VLAN-tagged LXC traffic
- pfSense VLAN interface upstream gateway misconfiguration broke routing
- Tailscale daemon failed in LXC due to missing TUN device (required cgroup config)

Each issue reinforced the importance of layered troubleshooting and
understanding how services interact across the network stack.

---

## Summary
This homelab emphasizes defensive security principles, realistic
infrastructure design, and practical troubleshooting. All changes are
tested, documented, and iterated on to reflect real-world conditions.
