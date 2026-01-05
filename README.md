# Cybersecurity & Networking Homelab

> **TL;DR:** Virtualized enterprise-style network using Proxmox and pfSense with VLAN segmentation, default-deny firewall policy, and documented troubleshooting scenarios.


## Overview
This project is a personal cybersecurity homelab designed to simulate a small-scale enterprise network environment. The lab focuses on **network segmentation, firewall configuration, virtualization, and traffic analysis**, using industry-standard tools to develop hands-on defensive security and system administration skills.

The environment is built and maintained as a continuous learning platform to practice **real-world troubleshooting, network security controls, and documentation**.

---

## Objectives
- Design a segmented virtual network resembling an enterprise layout
- Implement firewall rules and traffic control using pfSense
- Gain hands-on experience with virtualization and network bridging
- Practice troubleshooting connectivity, routing, and DNS issues
- Analyze network traffic and logs for security visibility
- Document architecture and configurations clearly and professionally

---

## Technologies Used
- **Proxmox VE** – Virtualization platform for hosting isolated virtual machines
- **pfSense** – Firewall, router, NAT, and DHCP services
- **Linux & Windows VMs** – Endpoint and server simulation
- **VLANs** – Network segmentation and isolation
- **Managed switch & access point** – Physical networking integration

---

## Network Architecture
- Dedicated **WAN and LAN interfaces** routed through pfSense
- **VLAN-segmented networks** to isolate services and clients
- Virtual NICs bridged through Proxmox
- DHCP centrally managed via pfSense
- Firewall rules enforcing least-privilege traffic flow

### High-Level Architecture
![High-Level Architecture](diagrams/high-level-architecture.png)


### Logical Network Segmentation
![Logical Network Segmentation](diagrams/logical-network-segmentation.png)


### Virtualization & Bridging (Proxmox)
![Virtualization & Bridging](diagrams/proxmox-bridging.png)



---

## Key Features & Skills Demonstrated
- Firewall rule creation and validation
- NAT configuration and interface assignment
- VLAN tagging and isolation
- DHCP configuration and IP management
- Virtual machine deployment and networking
- Troubleshooting Layer 2–4 connectivity issues
- Traffic inspection and packet analysis
- Operating in restricted / limited-connectivity environments
- Technical documentation and change tracking

---

## Troubleshooting Scenarios Practiced
- VM unable to obtain DHCP lease
- VLAN misconfiguration causing traffic isolation
- Incorrect firewall rules blocking internal traffic
- DNS resolution failures across network segments
- Bridged interface issues between Proxmox and pfSense
- Wireless access point connectivity problems

---

## Security Focus
This lab emphasizes **defensive security concepts**, including:
- Network segmentation to reduce attack surface
- Controlled east-west traffic via firewall rules
- Visibility into network activity through logging and packet capture
- Secure configuration of routing and services

---

## Evidence / Documentation
- Firewall rules: [firewall-rules.md](firewall-rules.md)
- Network config: [network-config.md](network-config.md)
- Troubleshooting playbooks: [troubleshooting.md](troubleshooting.md)
- Security+ mapping: [securityplus-mapping.md](securityplus-mapping.md)
- Roadmap: [roadmap.md](roadmap.md)

---

## Future Enhancements
- IDS/IPS integration (Suricata or Snort)
- Centralized logging and monitoring
- Vulnerable VM deployment for detection practice
- SIEM-style log analysis
- Automated backups and configuration versioning

---

## Purpose
This homelab is maintained to support career development in **Cybersecurity, SOC Analysis, and System Administration**, complementing formal education and industry certifications such as **CompTIA Security+** and the **Google Cybersecurity Certificate**.

---

## Author
**Harrison Bourg**  
Cybersecurity Graduate  
GitHub: https://github.com/hpbourg
