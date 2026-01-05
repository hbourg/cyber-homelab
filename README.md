# Cybersecurity & Networking Homelab

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
- **Wireshark** – Packet capture and traffic analysis
- **Managed switch & access point** – Physical networking integration

---

## Network Architecture
- Dedicated **WAN and LAN interfaces** routed through pfSense
- **VLAN-segmented networks** to isolate services and clients
- Virtual NICs bridged through Proxmox
- DHCP centrally managed via pfSense
- Firewall rules enforcing least-privilege traffic flow

- High-Architecture

           Internet
               |
         [ ISP / Modem ]
               |
            (WAN)
           pfSense
        Firewall / Router
               |
            (LAN Trunk)
               |
        ┌─────────────────┐
        │     Proxmox     │
        │ Virtualization  │
        └─────────────────┘
           |          |
        VLAN 10    VLAN 20
        Clients    Servers


- Logical Network Segemntation

-
                     pfSense
        ┌──────────────┼────────────────┐
        │               │                │
     VLAN 10          VLAN 20         Management
   User LAN        Server LAN       Proxmox Host
  192.168.10.0/24 192.168.20.0/24   192.168.99.0/24
        │               │
   Windows VM       Linux VM
   Client Test      Server Test


- Virtualization and Bridging (Proxmox)
  
           Physical NICs
        ┌────────┬────────┐
        │  NIC0  │  NIC1  │
        │  WAN   │  LAN   │
        └────┬───┴───┬────┘
             │       │
          vmbr0    vmbr1
          (WAN)    (VLAN-aware)
             │       │
          pfSense   Internal VMs
                    VLAN 10 / 20


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

## Documentation
This repository includes:
- Network diagrams
- Configuration notes
- Firewall rule explanations
- Troubleshooting walkthroughs
- Lessons learned and future improvements

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
