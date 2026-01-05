# Troubleshooting Playbooks

## Issue: VM Not Receiving DHCP Lease

### Symptoms
- VM reports no IP address
- APIPA address assigned

### Investigation
- Verified VLAN tagging on Proxmox bridge
- Confirmed DHCP service running on pfSense
- Captured DHCP discovery packets using Wireshark

### Resolution
- Corrected VLAN tag on VM network interface
- Restarted DHCP service

### Lesson Learned
Consistent VLAN tagging across hypervisor and firewall is critical for DHCP functionality.

---

## Issue: Inter-VLAN Traffic Blocked Unexpectedly

### Symptoms
- Clients unable to reach server resources

### Investigation
- Reviewed firewall rule order
- Checked pfSense logs for blocked packets

### Resolution
- Adjusted rule priority to explicitly allow required traffic

### Lesson Learned
Firewall rule order directly affects traffic flow and must be carefully managed.
