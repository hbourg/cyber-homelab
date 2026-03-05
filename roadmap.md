# Future Enhancements Roadmap

Planned improvements to expand security monitoring and detection capabilities.

---

## Completed
- ✅ pfSense firewall and routing
- ✅ Pi-hole DNS filtering
- ✅ Tailscale zero-trust remote access
- ✅ VLAN segmentation (User LAN / Server LAN / Management)
- ✅ ZFS mirror storage redundancy
- ✅ Plex Media Server (LXC, VLAN 20, remote access via Tailscale)
- ✅ Tailscale subnet routing for full VLAN 20 remote access
- ✅ Pi-hole DNS filtering extended to Tailscale network

---

## In Progress / Planned

### Security Monitoring
- IDS/IPS deployment (Suricata on pfSense)
- Centralized log aggregation
- SIEM deployment (Wazuh)
- SIEM-style alerting and correlation

### Attack / Detect Practice
- Vulnerable VM lab (Metasploitable, DVWA) on isolated VLAN
- Kali Linux attacker VM on separate attacker VLAN
- Honeypot deployment (OpenCanary)

### Infrastructure
- Automated pfSense configuration backups to GitHub
- Additional VLAN segments (IoT, Guest)
- Second 4TB drive to mirror media storage pool
- Expand documented scenarios in `/scenarios`

---

## Goal
Continue evolving the lab to mirror enterprise SOC environments, with
end-to-end visibility from network traffic through to SIEM alerting.
