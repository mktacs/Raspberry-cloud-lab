# Raspberry PI cloud/homelab project

## Overview

This project is a personal infrastructure lab focused on building a self-hosted private cloud for file synchronization and personal data storage, while following security-first and enterprise-like practices.

## Main goals
- To design and deploy a secure, self-managed cloud environment
- To gain hands-on experience with containerization and infrastructure services
- To practice system hardening, network security, and access control
- To document infrastructure decisions and security trade-offs in a structured way

---

## Infrastructure overview
- **Hardware:** Raspberry PI 5 (16GB)
- **OS:** Ubuntu Server 24.04.3 LTS
- **Storage:** NVMe SSD 1TB
- **Network:**
 - Private LAN
 - VPN-based remote access
 - Public IPv4 with port forwarding

---

## High-level goals
[ x ] Install Ubuntu Server, initial configuration/hardening
[x] Configure netplan, update the system, harden the SSH
[x] Deploy internal PKI using EasyRSA. Generate CA, server and client certificates
[x] Deploy OpenVPN server, configure, teste
[x] Configure Firewall with default-deny policy
[ ] Explore MFA options for VPN access
[ ] Install Docker and Docker Compose
[ ] Deploy and harden first containers
[ ] Implement basic backup strategy
[ ] Deploy centralized logging stack (ELK/Wazuh)
[ ] Script repetitive setup tasks
