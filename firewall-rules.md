## Firewall Policy Overview

Default Policy:
- Deny all inter-VLAN traffic
- Allow outbound traffic from User VLAN to WAN
- Allow management access only from Management VLAN

### Example Rules
| Source VLAN | Destination | Port | Action | Reason |
|------------|------------|------|--------|--------|
| VLAN 10 | WAN | Any | Allow | Internet access |
| VLAN 10 | VLAN 20 | Any | Deny | Prevent lateral movement |
| Mgmt | pfSense | 443 | Allow | Administration |
