# FortiGate Firewall Security, VPN, and Threat-Prevention Lab

![Firewall](https://img.shields.io/badge/Firewall-FortiGate-EE3124)
![VPN](https://img.shields.io/badge/VPN-SSL%20VPN%20%7C%20IPsec-2176FF)
![Security](https://img.shields.io/badge/Security-Web%20%7C%20DNS%20%7C%20AV%20%7C%20IPS-2E8B57)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen)

> Project by Samuel Kim. All rights reserved. See [LICENSE](LICENSE).

## Project Overview

This project documents a FortiGate firewall lab focused on secure access, identity-aware policy design, VPN connectivity, internal service publishing, and layered threat prevention. FortiGate is used as the central enforcement point between the WAN, the internal LAN, remote VPN users, and protected internal services.

The lab demonstrates how several FortiGate capabilities work together in an enterprise-style workflow: administrator role-based access control, password hardening, LDAP integration, SSL VPN Tunnel Mode, SSL VPN Web Mode, Virtual IP publishing, site-to-site IPsec VPN, SSL/TLS inspection, web filtering, DNS filtering, antivirus inspection, intrusion prevention, application control, and quarantine.

From a cybersecurity perspective, the project focuses on least privilege, controlled remote access, service-restricted firewall policy, encrypted traffic inspection, content filtering, malware-prevention testing, botnet/C2 protection, and validation through client behavior and FortiGate logs.

## Objectives

- Define the FortiGate placement, interfaces, trusted networks, and external traffic path.
- Implement role-based administrative access with named accounts and restricted profiles.
- Apply a FortiGate password policy to local administrators and IPsec pre-shared keys.
- Integrate Active Directory identities through LDAP for group-based remote access.
- Configure and validate SSL VPN Tunnel Mode and browser-based Web Mode access.
- Publish an internal IIS service through destination NAT and a controlled inbound policy.
- Build a site-to-site IPsec VPN with directional service restrictions.
- Inspect encrypted and application traffic with FortiGate security profiles.
- Validate allowed and blocked behavior through client results, VPN status, and FortiGate logs.

## Project Chapters

| # | Chapter | Description |
|---|---------|-------------|
| 1 | [Topology and Lab Environment](docs/01-topology-and-lab-environment/README.md) | Topology, lab roles, addressing, trust boundaries, and traffic flow |
| 2 | [Administrator Access and Role-Based Control](docs/02-administrator-access-and-rbac/README.md) | Named administrator accounts, custom admin profiles, and least-privilege validation |
| 3 | [FortiGate Password Policy Hardening](docs/03-password-policy-hardening/README.md) | Local password complexity controls for administrators and IPsec pre-shared keys |
| 4 | [LDAP Integration and SSL VPN Tunnel Mode](docs/04-ldap-and-ssl-vpn-tunnel-mode/README.md) | Active Directory LDAP integration, SSL VPN Tunnel Mode, FortiClient access, and restricted RDP validation |
| 5 | [SSL VPN Web Mode](docs/05-ssl-vpn-web-mode/README.md) | Browser-based SSL VPN access with LDAP group mapping and an RDP bookmark |
| 6 | [IIS Publishing with Destination NAT](docs/06-iis-publishing-with-destination-nat/README.md) | Internal IIS publishing through a FortiGate Virtual IP and inbound firewall policy |
| 7 | [Site-to-Site IPsec VPN](docs/07-site-to-site-ipsec-vpn/README.md) | FortiGate-to-FortiGate IPsec connectivity with directional service restrictions |
| 8 | [Full SSL/TLS Inspection](docs/08-full-ssl-tls-inspection/README.md) | Full SSL/TLS inspection profile creation and outbound policy attachment |
| 9 | [Web Filtering](docs/09-web-filtering/README.md) | Static URL filtering, category authentication, client testing, and FortiGate web-filter logs |
| 10 | [DNS Filtering](docs/10-dns-filtering/README.md) | DNS filter profile configuration, controlled domain blocking, and DNS-filter log review |
| 11 | [Antivirus Inspection](docs/11-antivirus-inspection/README.md) | Flow-based antivirus profile deployment and safe test-sample validation |
| 12 | [Intrusion Prevention](docs/12-intrusion-prevention/README.md) | IPS sensor deployment, controlled test traffic, and dropped-event validation |
| 13 | [Application Control and Quarantine](docs/13-application-control-and-quarantine/README.md) | Application signature blocking, TeamViewer validation, and a temporary quarantine workflow |
| 14 | [Final Summary](docs/14-final-summary/README.md) | Validation summary, production recommendations, skills, and project closure |

## Tools and Technologies

- FortiGate / FortiOS web administration
- Active Directory Domain Services and LDAP authentication
- SSL VPN Tunnel Mode, Web Mode, and FortiClient
- Remote Desktop Protocol (RDP)
- Internet Information Services (IIS)
- Destination NAT through a FortiGate Virtual IP
- Site-to-site IPsec VPN
- SSL/TLS inspection
- Web Filter, DNS Filter, Antivirus, IPS, and Application Control

## Skills Demonstrated

- FortiGate firewall administration and directional policy design
- Role-based administrator access and least-privilege validation
- Active Directory LDAP integration for identity-aware policy
- SSL VPN Tunnel Mode, Web Mode, FortiClient, and RDP access
- Destination NAT and internal-service publishing
- Site-to-site IPsec VPN configuration and traffic restriction
- SSL/TLS inspection planning and policy attachment
- Web Filter, DNS Filter, Antivirus, IPS, and Application Control deployment
- FortiGate log review and evidence-based validation
