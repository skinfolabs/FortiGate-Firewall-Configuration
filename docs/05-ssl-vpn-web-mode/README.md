# SSL VPN Web Mode

This chapter documents SSL VPN Web Mode for a more focused browser-based remote-access workflow. HR users authenticate with LDAP credentials and receive a published RDP bookmark instead of a general routed tunnel.


## Technical Context

SSL VPN Web Mode provides browser-based access to selected internal services without giving the endpoint a general routed tunnel. In this exercise, LDAP-authenticated HR users sign in to the FortiGate portal and receive an RDP bookmark that represents one approved internal workstation.

The workflow deliberately repeats some identity steps from Tunnel Mode because HR access is kept separate from Sales access. A distinct Active Directory group, FortiGate user group, portal mapping, bookmark, and firewall policy make it possible to change one remote-access use case without affecting the other.

> Tunnel Mode extends selected network routes to FortiClient, while Web Mode publishes individual resources through the browser portal. Web Mode can reduce exposure when a user needs one defined application rather than broader network-level access.

**Implemented controls:**

- Created a dedicated LDAP group for HR remote users.
- Enabled Web Mode and created an internal RDP bookmark.
- Mapped the HR group to the browser portal.
- Created the SSL VPN-to-LAN policy for the published workstation.
- Validated portal authentication and browser-based RDP access.

## Key Technical Terms

| Term | Meaning in this chapter |
|------|-------------------------|
| Web Mode | Browser-based SSL VPN mode that publishes selected resources instead of routing a full tunnel. |
| Bookmark | A portal shortcut defining a specific internal destination such as RDP. |
| Authorization | The decision that maps an authenticated identity to an allowed portal or resource. |
| Portal policy | The access rule that permits the web portal session to reach the internal target. |

---

## Detailed Walkthrough

### Step 01 - Create the LDAP HR group and users

The `LDAP_HR` group and three directory users are prepared in Active Directory. A separate group keeps the HR Web Mode use case independent from the Sales Tunnel Mode configuration and allows FortiGate to apply a different portal and access policy.

> Department groups are useful security boundaries only when the firewall policies continue to reference them separately. Reusing one broad remote-access group would make it harder to limit each department to its intended resources.

![LDAP HR users](../../images/05-ssl-vpn-web-mode/01.png)

<p><sub><strong>Screenshot 033 - LDAP HR Group:</strong> Three HR users are shown as members of the Active Directory `LDAP_HR` group.</sub></p>

---

### Step 02 - Enable Web Mode on the portal

The SSL VPN portal is configured for Web Mode. Instead of receiving tunnel routes and a client address, the authenticated user interacts with resources explicitly published as portal bookmarks. This narrows the remote-access experience to the services selected by the administrator.

> A portal is a presentation and connection profile. It does not bypass firewall policy, so the bookmarked destination must still be allowed from the SSL VPN interface to the internal network.

![SSL VPN Web Mode portal](../../images/05-ssl-vpn-web-mode/02.png)

<p><sub><strong>Screenshot 034 - Web Mode Portal:</strong> Web Mode is enabled in the SSL VPN portal configuration.</sub></p>

---

### Step 03 - Create the RDP bookmark

An RDP bookmark stores the internal workstation address, TCP port `3389`, and connection security settings. The bookmark gives the HR user a named launch point in the portal and prevents the user from choosing arbitrary internal targets through the interface.

> The bookmark describes the application destination, while the firewall policy enforces the network permission. Both layers must agree; a visible bookmark without a matching policy will fail, and an overly broad policy could allow more access than the bookmark suggests.

![RDP bookmark](../../images/05-ssl-vpn-web-mode/03.png)

<p><sub><strong>Screenshot 035 - RDP Web Bookmark:</strong> The internal workstation is published as an RDP bookmark inside the SSL VPN portal.</sub></p>

---

### Step 04 - Map HR users to the web portal

The HR directory-backed group is mapped to the Web Mode portal under the SSL VPN authentication rules. After LDAP validates the credentials, FortiGate evaluates group membership and delivers the portal containing the RDP bookmark to matching HR users.

> Authentication proves the credentials, but authorization decides which portal the identity receives. This separation allows two valid domain users to receive different remote-access capabilities based on group membership.

![Web Mode portal mapping](../../images/05-ssl-vpn-web-mode/05.png)

<p><sub><strong>Screenshot 036 - HR Portal Mapping:</strong> The `LDAP_HR` group is directed to the configured `web-access` SSL VPN portal.</sub></p>

---

### Step 05 - Import and group the HR identities

The underlying `LDAP_HR` object referenced by the portal mapping is backed by the AtlasAD LDAP source. Documenting the group association after the mapping shows that FortiGate uses a directory-managed identity set rather than maintaining separate local VPN passwords.

> Keeping HR membership in the directory allows access to follow normal account administration. Removing a user from the approved LDAP group should remove the identity match without editing every VPN rule individually.

![FortiGate HR group](../../images/05-ssl-vpn-web-mode/04.png)

<p><sub><strong>Screenshot 037 - FortiGate HR User Group:</strong> The FortiGate `LDAP_HR` group references AtlasAD as its remote identity source.</sub></p>

---

### Step 06 - Create the Web Mode firewall policy

The firewall policy accepts traffic from the SSL VPN interface toward the internal workstation. The source is limited to the HR user group, and the destination is the workstation represented by the RDP bookmark. NAT is not required for this internal path because FortiGate routes between the SSL VPN context and the LAN.

> An authenticated portal session does not authorize all internal traffic. The policy is the enforcement point that permits the approved Web Mode flow and keeps unrelated LAN resources outside the HR access path.

![Web Mode firewall policy](../../images/05-ssl-vpn-web-mode/06.png)

<p><sub><strong>Screenshot 038 - Web Mode Access Policy:</strong> SSL VPN Web Mode users are allowed to reach the internal workstation through the LAN interface.</sub></p>

---

### Step 07 - Validate browser authentication and RDP

An HR user opens the FortiGate HTTPS portal and signs in with directory credentials. The authenticated portal displays the published RDP resource, and launching it opens the remote Windows desktop. The three screens document identity verification, resource presentation, and application access in the same order the user experiences them.

> This result validates more than the login page. It shows that LDAP authentication, group mapping, portal selection, the RDP bookmark, firewall policy, and internal service path work together end to end.

![SSL VPN portal login](../../images/05-ssl-vpn-web-mode/07.png)

<p><sub><strong>Screenshot 039 - SSL VPN Portal Login:</strong> The browser requests LDAP user credentials for the FortiGate web portal.</sub></p>

![SSL VPN web portal](../../images/05-ssl-vpn-web-mode/08.png)

<p><sub><strong>Screenshot 040 - Authenticated Web Portal:</strong> The logged-in HR user sees the published remote-access resource.</sub></p>

![Web Mode RDP desktop](../../images/05-ssl-vpn-web-mode/09.png)

<p><sub><strong>Screenshot 041 - Browser-Based RDP Session:</strong> The internal Windows desktop opens through the FortiGate SSL VPN web portal.</sub></p>

---

## Validation and Summary

Validation follows the user path: the browser portal accepts LDAP credentials, displays the RDP bookmark, and opens the internal Windows desktop. This confirms the Web Mode portal, HR group mapping, bookmark, and firewall policy are aligned.

This chapter completes the HR Web Mode access path. The screenshots validate login, portal selection, bookmark visibility, and the browser-based RDP session to the internal workstation.

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
