# LDAP Integration and SSL VPN Tunnel Mode

This chapter documents Active Directory LDAP integration, SSL VPN Tunnel Mode, FortiClient setup, and restricted RDP validation. It connects identity, remote-access portal settings, firewall policy, and the final internal desktop test into one end-to-end workflow.


## Technical Context

SSL VPN Tunnel Mode gives a remote computer a protected network path into selected internal resources through FortiClient. Active Directory remains the identity authority, LDAP allows FortiGate to verify those directory users, and a FortiGate user group converts the directory membership into an object that VPN authentication and firewall policies can reference.

The access path is built in layers: prepare the directory group, connect FortiGate to LDAP, import the identities, create the tunnel portal, map the group to that portal, permit only RDP to the workstation, and then validate the complete session from FortiClient. Each layer has a different purpose, so a successful LDAP test alone does not mean the VPN or RDP access is ready.

> Remote access is safest when identity, network assignment, routing, and application permission are controlled separately. Authentication answers who the user is, the portal defines the tunnel behavior, and the firewall policy decides which internal service that authenticated user may actually reach.

**Implemented controls:**

- Created an Active Directory group for Sales VPN users.
- Connected FortiGate to AtlasAD through LDAP.
- Imported directory identities and mapped them to a FortiGate group.
- Configured split-tunnel SSL VPN access and an address pool.
- Restricted the tunnel-to-LAN policy to the internal RDP destination.
- Validated FortiClient authentication, tunnel establishment, and RDP access.

Production recommendation: the lab uses LDAP over port `389` with secure connection disabled. In a production VPN design, LDAPS or StartTLS, certificate validation, a dedicated least-privileged bind account, and multi-factor authentication should be used for remote access.

## Key Technical Terms

| Term | Meaning in this chapter |
|------|-------------------------|
| LDAP | Directory protocol FortiGate uses to validate Active Directory identities and group membership. |
| SSL VPN Tunnel Mode | Remote-access mode where FortiClient creates an encrypted tunnel and installs routes to approved internal networks. |
| Split tunneling | A design where only selected internal networks enter the VPN tunnel while other traffic uses the user's normal internet path. |
| Portal mapping | The FortiGate rule that connects a user group to a specific SSL VPN portal behavior. |
| RDP | Remote Desktop Protocol, the internal service allowed through the restricted VPN policy in this lab. |

---

## Detailed Walkthrough

### Step 01 - Prepare the LDAP Sales group in Active Directory

The `LDAP_Sales` security group and three users are created on AtlasAD before FortiGate is configured. This makes the directory group the central membership list for Sales remote access instead of maintaining a separate set of individual usernames inside the firewall.

> Group-based authorization separates identity administration from firewall administration. A directory administrator can add or remove a Sales user from the approved group, while the FortiGate policies continue to reference one stable group object.

![LDAP Sales users](../../images/04-ssl-vpn-tunnel-mode-with-ldap-users/01.png)

<p><sub><strong>Screenshot 015 - LDAP Sales Group:</strong> Three Active Directory users are shown as members of the `LDAP_Sales` group.</sub></p>

---

### Step 02 - Connect FortiGate to Active Directory

An LDAP server object is created with the AtlasAD address, port, domain components, and bind identity. FortiGate uses this object to locate the directory, authenticate the bind account, search for users and groups, and later verify VPN credentials. The successful connectivity test confirms that the firewall can query AtlasAD using the entered settings.

The lab uses regular LDAP on port `389` with secure connection disabled. This preserves the implemented configuration, but production deployments should use LDAPS or StartTLS with a trusted certificate and a dedicated least-privileged bind account rather than a general server administrator identity.

> LDAP is the query and authentication protocol; it is not the VPN tunnel itself. In this lab the directory connection is unencrypted, which is acceptable only for controlled demonstration. Production LDAP traffic can contain sensitive identity information and should be protected against interception.

![LDAP server configuration](../../images/04-ssl-vpn-tunnel-mode-with-ldap-users/02.png)

<p><sub><strong>Screenshot 016 - FortiGate LDAP Connection:</strong> The AtlasAD LDAP object contains the server, bind, and directory settings and reports a successful connection.</sub></p>

---

### Step 03 - Start the remote-user import

FortiGate is instructed to create remote LDAP users instead of local firewall users. The remote-user wizard is opened, the LDAP user type is selected, and AtlasAD is chosen as the directory source. This keeps the passwords in Active Directory while making the required identities visible to FortiGate policy objects.

> A remote user object does not copy the user's password into FortiGate. The firewall keeps a reference to the external identity source and asks LDAP to validate the credentials when the user authenticates.

![Remote LDAP user type](../../images/04-ssl-vpn-tunnel-mode-with-ldap-users/03.png)

<p><sub><strong>Screenshot 017 - Remote LDAP User Selection:</strong> The user creation wizard is set to import remote LDAP identities.</sub></p>

![LDAP server selection](../../images/04-ssl-vpn-tunnel-mode-with-ldap-users/04.png)

<p><sub><strong>Screenshot 018 - LDAP Directory Selection:</strong> The configured AtlasAD LDAP server is selected as the source for imported users.</sub></p>

---

### Step 04 - Select and verify the LDAP users

The required Sales accounts are selected from the LDAP directory and then reviewed in FortiGate's user list. At this point the firewall has the identity objects needed for group membership, but the users still do not have VPN access until the portal mapping and traffic policy are configured.

> Identity visibility and authorization are different stages. Seeing a user in FortiGate proves that the directory object can be selected; it does not prove that the user is allowed to establish a tunnel or reach an internal system.

![LDAP user selection](../../images/04-ssl-vpn-tunnel-mode-with-ldap-users/06.png)

<p><sub><strong>Screenshot 019 - Active Directory User Selection:</strong> The Sales users are selected from the LDAP directory for import into FortiGate.</sub></p>

![Imported LDAP users](../../images/04-ssl-vpn-tunnel-mode-with-ldap-users/05.png)

<p><sub><strong>Screenshot 020 - Imported LDAP Identities:</strong> The selected directory accounts appear in the FortiGate user definitions.</sub></p>

---

### Step 05 - Configure the Tunnel Mode portal

The SSL VPN portal is configured for Tunnel Mode with split tunneling. The VPN address pool supplies an internal tunnel address to each connected client, while the routing address identifies the protected network that FortiClient should send through the tunnel. The saved portal then becomes a reusable set of connection settings assigned to authorized users.

Split tunneling sends only traffic for approved internal networks through FortiGate. Other internet traffic continues through the user's normal connection, reducing unnecessary bandwidth consumption while still protecting access to company resources.

> The portal defines how the client connects, not whether the destination is allowed. Even when FortiClient learns a route to the internal network, the traffic must still match a firewall policy before FortiGate forwards it to the LAN.

![SSL VPN tunnel portal](../../images/04-ssl-vpn-tunnel-mode-with-ldap-users/07.png)

<p><sub><strong>Screenshot 021 - Tunnel Portal Configuration:</strong> Tunnel Mode, split tunneling, and the client address pool are configured for remote users.</sub></p>

![SSL VPN routing addresses](../../images/04-ssl-vpn-tunnel-mode-with-ldap-users/08.png)

<p><sub><strong>Screenshot 022 - Split-Tunnel Destination:</strong> The internal routing address and VPN client pool are selected for the tunnel portal.</sub></p>

![SSL VPN portal list](../../images/04-ssl-vpn-tunnel-mode-with-ldap-users/09.png)

<p><sub><strong>Screenshot 023 - Completed Tunnel Portal:</strong> The new SSL VPN portal appears in the portal list after its settings are saved.</sub></p>

---

### Step 06 - Configure SSL VPN listening settings

SSL VPN is bound to the WAN interface and port `443`, which defines where remote FortiClient connections arrive. The built-in `Fortinet_Factory` certificate is selected for this isolated exercise so the firewall can present a server certificate during the TLS handshake. The listener and certificate settings are applied before the user-to-portal mapping is tested.

> The factory certificate can encrypt a lab connection, but it does not prove the identity of a production VPN service to external users. A public deployment should use a trusted certificate matching the VPN FQDN and should normally combine directory credentials with MFA.

![SSL VPN listening settings](../../images/04-ssl-vpn-tunnel-mode-with-ldap-users/11.png)

<p><sub><strong>Screenshot 024 - SSL VPN Listener and Certificate:</strong> The WAN interface, HTTPS port, and laboratory server certificate are selected for SSL VPN access.</sub></p>

---

### Step 07 - Create the FortiGate LDAP user group

The imported Active Directory group is associated with a FortiGate user group. This object bridges external LDAP membership with FortiGate authentication rules, allowing the same directory-managed Sales group to be referenced by the SSL VPN mapping and traffic policy.

> FortiGate user groups are policy objects, while the actual member list remains in Active Directory. This arrangement keeps access decisions identity-aware without forcing the firewall administrator to recreate the directory structure manually.

![FortiGate LDAP user group](../../images/04-ssl-vpn-tunnel-mode-with-ldap-users/10.png)

<p><sub><strong>Screenshot 025 - LDAP Group Mapping:</strong> The `LDAP_Users` FortiGate group is associated with the remote AtlasAD LDAP source.</sub></p>

---

### Step 08 - Map LDAP users to the tunnel portal

Authentication and portal mapping connects the LDAP group to the Tunnel Mode portal. When a remote user authenticates successfully and belongs to the approved group, FortiGate selects this rule and returns the address-pool, routing, and split-tunneling behavior configured in the portal.

> Portal mapping is the decision point between identity and connection behavior. Without a matching rule, valid LDAP credentials may still receive the wrong portal or no usable remote-access configuration.

![SSL VPN mapping overview](../../images/04-ssl-vpn-tunnel-mode-with-ldap-users/12.png)

<p><sub><strong>Screenshot 026 - SSL VPN Mapping Overview:</strong> The authentication table shows `LDAP_Users` mapped to the Tunnel Mode portal and provides the control used to create a new mapping.</sub></p>

![Tunnel portal mapping](../../images/04-ssl-vpn-tunnel-mode-with-ldap-users/13.png)

<p><sub><strong>Screenshot 027 - Tunnel Portal Mapping Details:</strong> The authentication rule associates the `LDAP_Users` group with the configured `tunnel-access` portal.</sub></p>

---

### Step 09 - Allow RDP from the VPN to the workstation

The firewall policy accepts traffic from the SSL VPN tunnel interface toward the LAN workstation and limits the service to RDP. Its source includes the approved VPN identities and tunnel address range, while the destination identifies the internal computer. This converts an authenticated tunnel into a narrowly defined application path rather than general LAN access.

> A VPN should not automatically create full trust. Restricting the policy to the required destination and RDP service reduces lateral movement opportunities if a remote credential or endpoint is compromised.

![SSL VPN RDP policy](../../images/04-ssl-vpn-tunnel-mode-with-ldap-users/14.png)

<p><sub><strong>Screenshot 028 - SSL VPN RDP Policy:</strong> The tunnel-to-LAN policy allows authenticated LDAP VPN users to reach the internal workstation with RDP.</sub></p>

---

### Step 10 - Configure FortiClient on the remote computer

FortiClient is installed on the external computer and a connection profile is created with the FortiGate WAN gateway and HTTPS port. The profile tells the client where the VPN listener is located; the user then selects that profile and enters an Active Directory username and password for LDAP validation.

> FortiClient is the endpoint component that creates the encrypted tunnel and installs the assigned route on the remote computer. The client configuration must match the FortiGate listener, otherwise the request never reaches the authentication and portal rules.

![FortiClient VPN profile](../../images/04-ssl-vpn-tunnel-mode-with-ldap-users/16.png)

<p><sub><strong>Screenshot 029 - FortiClient VPN Profile:</strong> The remote FortiGate gateway and SSL VPN connection settings are entered in FortiClient.</sub></p>

![FortiClient LDAP login](../../images/04-ssl-vpn-tunnel-mode-with-ldap-users/15.png)

<p><sub><strong>Screenshot 030 - FortiClient LDAP Authentication:</strong> An Active Directory user signs in to the newly configured VPN connection.</sub></p>

---

### Step 11 - Validate the tunnel and RDP session

FortiClient shows an active tunnel and an assigned VPN address, proving that the user authenticated and received the Tunnel Mode portal settings. The Windows desktop then opens through RDP, confirming the rest of the path: split-tunnel routing, SSL VPN interface processing, firewall policy matching, LAN reachability, and the RDP service on the target workstation.

> End-to-end validation is stronger than a green VPN status alone. A connected tunnel proves remote access was established, while the successful RDP session proves that the intended internal application can actually be reached through the restricted policy.

![FortiClient connected](../../images/04-ssl-vpn-tunnel-mode-with-ldap-users/18.png)

<p><sub><strong>Screenshot 031 - Active FortiClient Tunnel:</strong> FortiClient reports a connected SSL VPN session and assigned tunnel address.</sub></p>

![Internal workstation over RDP](../../images/04-ssl-vpn-tunnel-mode-with-ldap-users/17.png)

<p><sub><strong>Screenshot 032 - RDP Through SSL VPN:</strong> The remote user reaches the internal Windows workstation through the encrypted VPN tunnel.</sub></p>

---

### Step 12 - Understand how RDP and VPN work together

RDP is Microsoft's remote-display protocol and normally uses TCP port `3389`. It carries the graphical desktop from the remote Windows system to the client and returns keyboard and mouse input to the remote host. The VPN does not replace RDP; it creates the protected network path that allows the RDP session to reach an internal address without exposing the workstation directly to the public internet.

> Publishing RDP directly to the internet would expose the service to scanning and credential attacks. Placing RDP behind authenticated VPN access adds an encrypted and identity-controlled entry point before the workstation becomes reachable.

---

## Validation and Summary

Validation is end to end: LDAP connectivity succeeds, the group is mapped to a Tunnel Mode portal, FortiClient receives a tunnel address, and the user reaches the internal Windows desktop through RDP. This confirms identity, portal, route, firewall policy, and application reachability together.

This chapter completes the Sales remote-access path. The evidence confirms that LDAP users can authenticate through FortiClient, receive Tunnel Mode settings, and reach the intended internal RDP destination through a restricted firewall policy.

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
