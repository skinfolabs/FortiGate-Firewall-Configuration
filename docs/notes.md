# Project Notes

## Scope

This repository documents a controlled FortiGate lab. It focuses on configuration workflow, security intent, and validation evidence. It does not include live FortiGate configuration exports or reusable automation scripts.

## Technical Notes

### 1. Role-Based Administration

The administrator profile and restricted IT administrator examples demonstrate least privilege. In production, administrator roles should be reviewed regularly and tied to named accounts, MFA, and centralized logging.

### 2. LDAP Authentication

LDAP integration allows FortiGate to use Active Directory users and groups for VPN access. The lab uses regular LDAP on port 389 with secure connection disabled and a server administrator bind identity. In production, use LDAPS or StartTLS, validate the directory certificate, and create a dedicated least-privileged bind account. Remote access should also be combined with MFA.

### 3. SSL VPN Access

SSL VPN Tunnel Mode gives users broader network access than Web Mode. Tunnel access should be restricted by user group, destination, service, and posture requirements where possible.

SSL VPN Web Mode is more focused because access is provided through bookmarks such as RDP. It is useful when users need a specific internal resource rather than full network access.

The built-in `Fortinet_Factory` server certificate is retained because it is sufficient for this isolated exercise. A public service should use a trusted certificate whose subject matches the VPN FQDN.

### 4. Destination NAT and IIS Publishing

The Virtual IP example performs destination NAT to publish an internal IIS service through FortiGate. It is not a load-balancing configuration. In production, public service publishing should be limited by source, required HTTP/HTTPS services, logging, TLS settings, IPS, and WAF or reverse-proxy controls where appropriate.

IIS and Active Directory share the Atlas server in this lab. This is acceptable for an isolated exercise, but a production web server should be separated from the domain controller so an internet-facing service does not increase the attack surface of directory services.

### 5. Site-to-Site IPsec VPN

The IPsec VPN section intentionally restricts traffic by service. A VPN should not automatically mean full trust between sites. Directional policies and specific allowed services reduce the attack surface between connected networks.

The New York-to-Tel Aviv policy correctly shows `PING`. The reverse policy is intended for RDP, but the captured policy field still displays `ALL` while the service picker highlights `RDP`. Before production use, the saved service must be changed from `ALL` to `RDP` and validated again.

### 6. SSL/TLS Inspection

Full SSL/TLS inspection can improve detection for encrypted traffic. The laboratory FortiGate CA is adequate for demonstrating the profile, while production deployment requires trusted certificate distribution, privacy review, exception handling, and application compatibility testing.

### 7. Web and DNS Filtering

The Web Filter and DNS Filter examples demonstrate user-facing enforcement and log validation. Both controls should be tested from a client endpoint because policy configuration alone does not prove that users are actually affected.

### 8. Antivirus and IPS Validation

The antivirus and IPS sections use client behavior and FortiGate logs to validate enforcement. The antivirus test uses safe Palo Alto WildFire test samples rather than live malware. Standard FortiGate Antivirus scanning should not be described as sandboxing unless FortiSandbox or the appropriate cloud sandbox service is separately configured.

### 9. Application Control and Quarantine

Application Control is used to block remote-access tools such as TeamViewer and demonstrate user quarantine. In production, quarantine actions should be scoped carefully to avoid disrupting legitimate support workflows.

## Files Intentionally Not Included

No separate command or script files were added because this project is based on GUI-based FortiGate configuration and screenshot validation. If future versions include CLI configuration, automation, or exported firewall rules, those should be stored under `configs/`, `scripts/`, or `docs/commands.md`.

## Firmware Status

The FortiGate interface displays a critical-severity vulnerability warning, but the exact FortiOS version was not recorded. The repository therefore does not claim a patched production state. An internet-facing deployment must use a currently supported release and apply relevant Fortinet security updates before service exposure.
