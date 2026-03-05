# Network Configuration

## VLAN Overview

### VLAN 10 – User LAN
- Subnet: 192.168.10.0/24
- Gateway: 192.168.10.1
- DHCP Range: 192.168.10.100 – 192.168.10.200
- Purpose: Client endpoints and user testing

### VLAN 20 – Server LAN
- Subnet: 192.168.20.0/24
- Gateway: 192.168.20.1
- DHCP Range: 192.168.20.100 – 192.168.20.200
- Static Assignments: 192.168.20.2 – 192.168.20.99 (reserved for servers)
- Purpose: Internal services and server infrastructure

#### VLAN 20 Static Assignments
| Host | IP | Description |
|------|----|-------------|
| Plex LXC | 192.168.20.54 | Plex Media Server (LXC Container 104) |

### VLAN 99 – Management
- Subnet: 192.168.99.0/24
- Gateway: 192.168.99.1
- DHCP: Disabled (static addressing preferred)
- Purpose: Proxmox and infrastructure management

---

## DHCP Configuration
- Centralized DHCP provided by pfSense
- Separate scopes per VLAN
- Static IPs assigned outside DHCP pool to avoid conflicts
- Lease assignments verified via pfSense dashboard

---

## Proxmox Bridge Configuration

| Bridge | Purpose | VLAN Aware |
|--------|---------|------------|
| vmbr0 | WAN (Cox gateway uplink) | No |
| vmbr1 | pfSense LAN (internal trunk) | Yes |

pfSense VM interfaces:
- net0 → vmbr0 (WAN)
- net1 → vmbr1 (LAN, untagged trunk — pfSense handles VLAN tagging internally)

LXC containers use VLAN tags on vmbr1. pfSense's internal VLAN interfaces process tagged frames from containers.

---

## Tailscale Remote Access

- Tailscale installed on Plex LXC (CT 104)
- Subnet route `192.168.20.0/24` advertised via Tailscale
- Route approved in Tailscale admin panel
- Allows full remote access to VLAN 20 devices without exposing any WAN ports
- Pi-hole set as global nameserver in Tailscale admin with DNS override enabled
- Pi-hole configured to accept queries from Tailscale IP range (100.64.0.0/10)

---

## Storage Configuration

| Pool Name | Type | Devices | Mount | Usage |
|-----------|------|---------|-------|-------|
| media-mirro | ZFS Mirror | /dev/sda, /dev/sdb (1TB each) | Proxmox storage | Plex config, metadata |
| media-storage | Directory (ext4) | /dev/sdc (4TB) | /mnt/pve/media-storage | Media library |
| local-lvm | LVM | /dev/sdd (525GB SSD) | — | Proxmox OS, VMs, containers |
