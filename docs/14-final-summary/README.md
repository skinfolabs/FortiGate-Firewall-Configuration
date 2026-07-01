# Final Summary

The lab demonstrates a layered FortiGate deployment rather than a single firewall rule. Identity controls determine who can administer the device and who can use remote access. Network controls define which paths and services are allowed. Security profiles then inspect permitted traffic and provide client-side and log-based validation.

The project also records the difference between a working laboratory configuration and a production-ready design. Production deployment would require a supported FortiOS release, encrypted LDAP, trusted VPN and inspection certificates, MFA, service separation, and stricter least-privilege firewall rules.

## Validation Summary

| Control | Validation captured |
|---------|---------------------|
| Administrator RBAC | Restricted menus and read-only IT access |
| LDAP | Successful directory connection and imported users |
| SSL VPN Tunnel Mode | FortiClient connected and RDP desktop reached |
| SSL VPN Web Mode | Portal login, published bookmark, and browser RDP session |
| Destination NAT | IIS validated locally and through the external FortiGate address |
| IPsec | Tunnel status, directional policy configuration, RDP testing, and denied reverse ping |
| SSL/TLS inspection | Full-inspection profile attached to outbound policy |
| Web Filter | Reddit block, authentication prompt, and FortiGate logs |
| DNS Filter | Client block page and DNS-filter logs |
| Antivirus | Safe test sample blocked and logged |
| IPS | Client timeout correlated with a dropped IPS event |
| Application Control | TeamViewer block logs and two-day quarantine workflow |

## Production Recommendations

- Use named administrator accounts with MFA, restricted management access, and centralized logging for all privileged FortiGate access.
- Replace regular LDAP on port `389` with LDAPS or StartTLS, validate the directory certificate, and use a dedicated least-privileged bind account.
- Replace the laboratory VPN certificate with a trusted certificate that matches the public VPN FQDN before exposing SSL VPN to real users.
- Keep public web services separate from domain controllers. In this lab, IIS and Active Directory share AtlasAD only because the environment is isolated.
- Restrict inbound destination NAT policies to the exact required services and source ranges where possible.
- Correct the Tel Aviv-to-New York IPsec service field from `ALL` to `RDP` before treating that reverse policy as production-ready least privilege.
- Distribute and test a trusted inspection CA before using full SSL/TLS inspection with managed users.
- Review FortiOS firmware and security advisories before exposing any internet-facing firewall service. The lab screenshots show a critical-severity warning, but the exact FortiOS version is not recorded.

## Skills Demonstrated

- FortiGate firewall administration and directional policy design
- Role-based administrator access and least-privilege validation
- Active Directory LDAP integration for identity-aware policy
- SSL VPN Tunnel Mode, Web Mode, FortiClient, and RDP access
- Destination NAT and controlled internal-service publishing
- Site-to-site IPsec VPN configuration and traffic restriction
- Full SSL/TLS inspection planning and policy attachment
- Web Filter and DNS Filter configuration
- Antivirus and IPS profile deployment and log analysis
- Application identification, blocking, and quarantine response
- End-to-end testing with client behavior and FortiGate security logs

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
