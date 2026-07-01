# Topology and Lab Environment

This chapter defines the FortiGate lab layout before the security controls are configured. The topology explains where the firewall is placed, which interfaces face the WAN and LAN, which internal systems are protected, and which lab components are used later for VPN, RDP, IIS publishing, filtering, and inspection validation.

## Topology

FortiGate is positioned between the external WAN and the protected internal environment. `Port1` represents the WAN-facing interface, while `Port2` represents the LAN-facing interface. The internal side includes the AtlasAD domain server and a Windows client used for RDP, SSL VPN validation, web access testing, and security-profile enforcement checks.

The topology matters because every later policy depends on traffic direction. VPN users, inbound web access, outbound browsing, DNS requests, and site-to-site traffic all cross different trust boundaries. Defining the WAN, LAN, internal systems, and remote VPN paths first makes the firewall policy logic easier to follow.

| Component | Purpose |
|-----------|---------|
| FortiGate VM | Firewall, VPN gateway, destination NAT device, and security-inspection point |
| Port 1 | WAN-facing interface connected to the external network path |
| Port 2 | LAN-facing interface connected to the internal lab environment |
| AtlasAD | Active Directory and LDAP server; also hosts IIS for the publishing exercise |
| Windows client | Internal workstation used for RDP, browsing, VPN, and validation tests |
| FortiClient VPN | Remote-access client used to validate SSL VPN Tunnel Mode |
| Envario | Virtual lab platform hosting the systems used in the exercise |

![FortiGate logical topology](../../images/01-fortigate-overview-and-network-topology/01.png)

<p><sub><strong>Screenshot 002 - FortiGate Logical Topology:</strong> The firewall is positioned between the internal LAN and the external internet path, establishing the trust boundary used by the later policies.</sub></p>

![FortiGate lab network overview](../../images/01-fortigate-overview-and-network-topology/02.png)

<p><sub><strong>Screenshot 003 - Lab Network Overview:</strong> The FortiGate VM, WAN path, internal server, workstation, and lab network segments are shown together.</sub></p>

### Site Addressing Used by the IPsec Exercise

| Site | Networks / endpoint |
|------|---------------------|
| New York | `10.90.1.0/24`, `10.90.11.0/24` |
| Tel Aviv | `10.74.1.0/24`, `10.74.11.0/24` |
| Tel Aviv WAN | `13.82.92.101` |

The addresses belong to the isolated lab evidence and make the VPN design reproducible. They should not be reused as production addressing documentation.

## Lab Environment

| Component | Role |
|-----------|------|
| FortiGate VM | Firewall, router, VPN endpoint, destination NAT device, and inspection engine |
| Active Directory / LDAP | Central identity source for VPN users and groups |
| IIS on AtlasAD | Internal web service used for destination NAT publishing validation |
| Windows workstation | RDP destination and client-side validation machine |
| FortiClient | SSL VPN Tunnel Mode client |
| Remote FortiGate peer | IPsec VPN endpoint for the site-to-site exercise |

The lab environment is intentionally compact so each FortiGate feature can be validated with visible client behavior or firewall logs. The same internal systems are reused across multiple chapters, which makes the project easier to follow while still showing realistic relationships between identity, remote access, service publishing, and traffic inspection.

---

## Project Chapters

| # | Chapter | Description |
|---|---------|-------------|
| 0 | [Project Overview](../../README.md) | Main project overview, objectives, tools, and skills |
| 1 | [Topology and Lab Environment](../01-topology-and-lab-environment/README.md) | Topology, lab roles, addressing, trust boundaries, and traffic flow |
| 2 | [Administrator Access and Role-Based Control](../02-administrator-access-and-rbac/README.md) | Named administrator accounts, custom admin profiles, and least-privilege validation |
| 3 | [FortiGate Password Policy Hardening](../03-password-policy-hardening/README.md) | Local password complexity controls for administrators and IPsec pre-shared keys |
| 4 | [LDAP Integration and SSL VPN Tunnel Mode](../04-ldap-and-ssl-vpn-tunnel-mode/README.md) | Active Directory LDAP integration, SSL VPN Tunnel Mode, FortiClient access, and restricted RDP validation |
| 5 | [SSL VPN Web Mode](../05-ssl-vpn-web-mode/README.md) | Browser-based SSL VPN access with LDAP group mapping and an RDP bookmark |
| 6 | [IIS Publishing with Destination NAT](../06-iis-publishing-with-destination-nat/README.md) | Internal IIS publishing through a FortiGate Virtual IP and inbound firewall policy |
| 7 | [Site-to-Site IPsec VPN](../07-site-to-site-ipsec-vpn/README.md) | FortiGate-to-FortiGate IPsec connectivity with directional service restrictions |
| 8 | [Full SSL/TLS Inspection](../08-full-ssl-tls-inspection/README.md) | Full SSL/TLS inspection profile creation and outbound policy attachment |
| 9 | [Web Filtering](../09-web-filtering/README.md) | Static URL filtering, category authentication, client testing, and FortiGate web-filter logs |
| 10 | [DNS Filtering](../10-dns-filtering/README.md) | DNS filter profile configuration, controlled domain blocking, and DNS-filter log review |
| 11 | [Antivirus Inspection](../11-antivirus-inspection/README.md) | Flow-based antivirus profile deployment and safe test-sample validation |
| 12 | [Intrusion Prevention](../12-intrusion-prevention/README.md) | IPS sensor deployment, controlled test traffic, and dropped-event validation |
| 13 | [Application Control and Quarantine](../13-application-control-and-quarantine/README.md) | Application signature blocking, TeamViewer validation, and a temporary quarantine workflow |
| 14 | [Final Summary](../14-final-summary/README.md) | Validation summary, production recommendations, skills, and project closure |
