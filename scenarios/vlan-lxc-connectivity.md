# Scenario: LXC Container VLAN Connectivity Failure

## Overview
During deployment of a Plex Media Server LXC container on VLAN 20, the container
had no network connectivity despite correct IP assignment and pfSense VLAN
configuration. This scenario documents the full diagnostic process and root cause.

---

## Environment
- Proxmox VE host with vmbr1 as VLAN-aware bridge (pfSense LAN trunk)
- pfSense VM managing VLAN routing with VLAN20_SERVERS interface (192.168.20.1/24)
- Plex LXC (CT 104): IP 192.168.20.54/24, bridge=vmbr1, VLAN tag=20, firewall=1

---

## Symptoms
- Container had correct IP address assigned (`ip addr show` confirmed 192.168.20.54/24)
- Default route was correctly set (`default via 192.168.20.1`)
- `ping 192.168.20.1` returned "Destination host unreachable"
- `ping 8.8.8.8` failed
- pfSense Diagnostics → Ping showed 100% packet loss to container
- Container IP did not appear in pfSense ARP table
- `apt update` failed with DNS resolution errors

---

## Diagnostic Steps

### Step 1 – Verify Container IP and Routing
```bash
ip addr show
ip route show
```
Result: IP and gateway correctly configured. Issue is not inside the container.

### Step 2 – Verify pfSense VLAN Interface
Checked Interfaces → VLAN20_SERVERS in pfSense.
Result: Interface enabled, IP 192.168.20.1/24 set correctly.

### Step 3 – Verify Firewall Rules
Checked Firewall → Rules → VLAN20_SERVERS.
Result: Allow rule present for VLAN20 subnets to any destination.

### Step 4 – Check pfSense ARP Table
Checked Diagnostics → ARP Table.
Result: 192.168.20.54 not present. pfSense had never seen the container's MAC address,
meaning traffic was not reaching pfSense at all.

### Step 5 – tcpdump on vmbr1
```bash
tcpdump -i vmbr1 -n arp
```
Result: No ARP requests from 192.168.20.54 visible on vmbr1.

### Step 6 – tcpdump on veth104i0
```bash
tcpdump -i veth104i0 -n arp
```
Result: ARP requests leaving the container ("who has 192.168.20.1 tell 192.168.20.54")
confirmed, but traffic never reached vmbr1.

---

## Root Cause

The Proxmox firewall (`firewall=1` in LXC config) inserts a non-VLAN-aware bridge
called `fwbr104i0` into the traffic path between the container and vmbr1:

```
Container → fwbr104i0 (VLAN 1 only) → vmbr1 → pfSense
```

`fwbr104i0` is a standard Linux bridge without VLAN filtering support. It only
knows about VLAN 1 and silently drops all VLAN 20 tagged frames. The `bridge vlan add`
command cannot be run against it — it returns "Operation not supported."

This means ARP requests left the container correctly but were dropped before reaching
vmbr1, so pfSense never learned the container's MAC address.

---

## Resolution

Disabled the Proxmox firewall on the LXC NIC by setting `firewall=0` in the container
network config. This removes `fwbr104i0` from the traffic path entirely:

```
Container → vmbr1 → pfSense VLAN 20
```

Steps:
1. Stop CT 104
2. In Proxmox GUI: CT 104 → Network → eth0 → Edit → uncheck Firewall
3. Start CT 104
4. Verify: `ping 192.168.20.1` from container → 0% packet loss

---

## Security Consideration

Disabling the Proxmox firewall on the NIC does not reduce security in this architecture.
pfSense's VLAN20_SERVERS firewall rules enforce all access control for VLAN 20 traffic.
pfSense provides stateful inspection, rule logging, and per-rule control — capabilities
that exceed what the Proxmox iptables firewall offers. Using pfSense as the sole
enforcement point is consistent with a single-boundary security model.

---

## Additional Issues Encountered

### pfSense Upstream Gateway Misconfiguration
During troubleshooting, a gateway (VLAN20_GW) was incorrectly added to the
VLAN20_SERVERS interface. pfSense treats interfaces with an upstream gateway as
WAN-type, which breaks routing for LAN-side interfaces. The upstream gateway for
internal VLAN interfaces must always be set to **None**.

### Tailscale TUN Device Missing in LXC
After resolving connectivity, Tailscale installation failed because LXC containers
do not have access to `/dev/net/tun` by default. Fixed by adding to `/etc/pve/lxc/104.conf`:

```
lxc.cgroup2.devices.allow = c 10:200 rwm
lxc.mount.entry = /dev/net/tun dev/net/tun none bind,create=file
```

---

## Lessons Learned
- The Proxmox firewall and VLAN-tagged LXC NICs are incompatible — always disable
  the Proxmox firewall on LXC NICs when using VLAN tags
- tcpdump at each hop in the traffic path is essential for isolating where frames
  are being dropped
- pfSense LAN-side VLAN interfaces should never have an upstream gateway set
- ARP table absence is a reliable indicator that traffic is not reaching the router
  at all, distinguishing L2 from L3/L4 problems
