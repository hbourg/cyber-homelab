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
- Purpose: Internal services and server simulation

### VLAN 99 – Management
- Subnet: 192.168.99.0/24
- Gateway: 192.168.99.1
- DHCP: Disabled (static addressing preferred)
- Purpose: Proxmox and infrastructure management

---

## DHCP Configuration
- Centralized DHCP provided by pfSense
- Separate scopes per VLAN
- Lease assignments verified via pfSense dashboard
