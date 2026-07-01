# Administrator Access and Role-Based Control

This chapter documents FortiGate administrator role design. It shows how named accounts and restricted profiles reduce unnecessary privilege while preserving the access needed for monitoring, support, and controlled administration.


## Technical Context

Administrative access is separated into named profiles instead of giving every operator unrestricted `super_admin` privileges. One profile demonstrates a powerful but selectively restricted administrator, while the IT profile provides view-only access to the operational areas required for monitoring and support.

This design makes the administrative plane part of the security architecture. Firewall rules protect network traffic, but administrator profiles protect the configuration itself by controlling who can change policies, security profiles, interfaces, and logs.

> Role-based access control reduces the impact of account compromise and human error. An operator who only needs to review policies or logs should not automatically be able to alter VPN settings, disable protection, or create new administrators.

**Implemented controls:**

- Created a restricted Super_Admin-like profile.
- Assigned a named administrator to the custom profile.
- Created an IT read-only profile and three administrator accounts.
- Validated the menus and permissions visible to restricted sessions.

## Key Technical Terms

| Term | Meaning in this chapter |
|------|-------------------------|
| RBAC | Role-Based Access Control, where permissions are assigned according to job role instead of giving every administrator full access. |
| Administrator profile | A FortiGate permission template that controls which configuration areas can be read, changed, or hidden. |
| Named administrator | A unique admin identity used for accountability instead of shared privileged access. |
| Read-only access | Permission to inspect configuration and logs without changing the firewall state. |

---

## Detailed Walkthrough

### Step 01 - Create a restricted administrator profile

A custom profile named `Special_Admin_Profile` is created under the FortiGate administrator-profile settings. It keeps broad access where administration is required, changes Log & Report to read-only, and hides or restricts selected areas such as System and Security Profiles. This produces a controlled alternative to assigning unrestricted `super_admin` rights.

> An administrator profile defines capability, while the administrator account defines identity. Keeping those objects separate allows the same permission design to be reviewed and reused without turning multiple operators into shared or anonymous super administrators.

![Restricted administrator profile](../../images/02-user-management-and-rbac/01.png)

<p><sub><strong>Screenshot 005 - Restricted Administrator Profile:</strong> The custom profile is created with selected read, read-write, and hidden access permissions.</sub></p>

---

### Step 02 - Create a named administrator account

The named account `Special_Admin` is created and assigned to `Special_Admin_Profile`. The account inherits the profile's permission boundaries when it signs in, so the restriction is enforced by FortiGate rather than relying on the operator to avoid sensitive menus.

> Named administrative accounts improve accountability because configuration changes and login events can be associated with a specific identity. Shared accounts weaken auditing and make it harder to determine who performed a risky change.

![Named administrator account](../../images/02-user-management-and-rbac/02.png)

<p><sub><strong>Screenshot 006 - Named Administrator Account:</strong> `Special_Admin` is assigned to `Special_Admin_Profile` during account creation.</sub></p>

---

### Step 03 - Review the restricted administrator interface

After signing in with `Special_Admin`, several parts of the interface are reviewed. The screenshots show the active username, the reduced navigation, and the limited System and Security Profiles views. This confirms that the profile is actually applied to the session instead of existing only as an unused configuration object.

> Permission validation should be performed from the restricted account itself. Reviewing the profile as a super administrator proves how it was configured, but logging in as the delegated account proves what that operator can really see and use.

![Special administrator session](../../images/02-user-management-and-rbac/03.png)

<p><sub><strong>Screenshot 007 - Special Administrator Session:</strong> The header identifies the active `Special_Admin` account used to validate the delegated profile.</sub></p>

![Restricted system menu](../../images/02-user-management-and-rbac/09.png)

<p><sub><strong>Screenshot 008 - Restricted System Menu:</strong> The limited session can open the approved System area while unavailable functions remain outside the delegated profile.</sub></p>

![Restricted security profiles menu](../../images/02-user-management-and-rbac/04.png)

<p><sub><strong>Screenshot 009 - Restricted Security Profiles Menu:</strong> The delegated account sees only the permitted SSL/SSH Inspection entry under Security Profiles.</sub></p>

---

### Step 04 - Create a new IT administrator group and profile

The IT team is represented by an `IT_Group` administrator profile. Its permissions are limited to viewing operational areas such as firewall policies, logs, Virtual IPs, and interfaces. The profile centralizes this permission model so every assigned IT administrator receives the same controlled view.

> Read-only access supports troubleshooting without granting change authority. An IT operator can inspect the active configuration and logs, but sensitive modifications remain reserved for a more privileged administrative role.

![IT read-only profile](../../images/02-user-management-and-rbac/06.png)

<p><sub><strong>Screenshot 010 - IT Read-Only Profile:</strong> The `IT_Group` profile is configured with view-only access to the required operational sections.</sub></p>

---

### Step 05 - Create three IT administrators

Three named administrator accounts are created and assigned to `IT_Group`. Reusing one profile keeps the access model consistent across the team and makes later permission changes easier because the profile can be adjusted once instead of editing every account separately.

> Grouping administrators by job function is easier to audit than maintaining unrelated per-user permission sets. It also reduces configuration drift when several operators are expected to perform the same support duties.

![IT administrator accounts](../../images/02-user-management-and-rbac/05.png)

<p><sub><strong>Screenshot 011 - IT Administrator Membership:</strong> Three IT administrator accounts are listed with the shared `IT_Group` profile.</sub></p>

---

### Step 06 - Validate read-only IT access

An IT administrator session is opened to verify the final result. Only the approved operational sections are available, and the interface shows that the account can review information without receiving unrestricted configuration rights.

> Least privilege is demonstrated by both access and restriction: the administrator must be able to reach the required monitoring views while remaining unable to modify unrelated security controls. A limited menu alone is not enough unless the available pages also enforce read-only behavior.

![IT administrator menu](../../images/02-user-management-and-rbac/07.png)

<p><sub><strong>Screenshot 012 - IT Navigation Restrictions:</strong> The limited menu shows which operational areas remain available to the IT administrator.</sub></p>

![Read-only IT view](../../images/02-user-management-and-rbac/08.png)

<p><sub><strong>Screenshot 013 - Read-Only IT Session:</strong> The restricted interface confirms that the IT account can view approved information without unrestricted write access.</sub></p>

---

## Validation and Summary

The delegated-access checks are validated by signing in with the restricted accounts and reviewing the visible menus and read-only areas. This confirms that the profiles are not only configured but also affect the active administrator sessions.

This chapter establishes the administrative access model for the lab. The screenshots validate both the profile configuration and the restricted administrator experience, which makes the later firewall changes easier to audit and control.

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
