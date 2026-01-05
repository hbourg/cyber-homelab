# Firewall Rules Documentation

## Overview
pfSense serves as the centralized enforcement point for all network traffic. The firewall policy follows a **default-deny** approach, allowing only explicitly permitted traffic and preventing unauthorized lateral movement between network segments.

---

## Default Policy
- Deny all inter-VLAN traffic
- Allow outbound traffic from User VLAN to WAN
- Restrict management access to the Management VLAN only
- Log blocked traffic for visibility and troubleshooting

---

## Firewall Rules Summary

| Source VLAN | Destination | Port(s) | Action | Reason |
|------------|------------|--------|--------|--------|
| VLAN 10 (User) | WAN | Any | Allow | Internet access |
| VLAN 10 (User) | VLAN 20 (Server) | Any | Deny | Prevent lateral movement |
| VLAN 20 (Server) | WAN | Any | Allow | System updates |
| Management | pfSense | 443 | Allow | Administrative access |
| Any | Any | Any | Deny | Default deny rule |

---

## Logging
- Blocked inter-VLAN traffic is logged
- Firewall logs reviewed during troubleshooting
- Logs used to validate rule effectiveness
