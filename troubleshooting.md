## Issue: VM Not Receiving DHCP Lease

### Symptoms
- No IP address assigned
- VM shows APIPA address

### Investigation
- Verified VLAN tagging on Proxmox bridge
- Checked pfSense DHCP service
- Captured traffic with Wireshark

### Resolution
- Corrected VLAN tag on VM NIC
