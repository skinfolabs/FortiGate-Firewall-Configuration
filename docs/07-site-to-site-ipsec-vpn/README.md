# Site-to-Site IPsec VPN

This chapter documents FortiGate-to-FortiGate IPsec connectivity with directional service restrictions. It shows that an encrypted tunnel and a least-privilege inter-site policy are related but separate security controls.


## Technical Context

IPsec protects traffic between the New York and Tel Aviv private networks across an untrusted external path. Each FortiGate acts as a VPN peer: the devices authenticate one another, negotiate cryptographic parameters, and create an encrypted path for traffic that matches the defined private-network selectors.

The tunnel itself does not grant unlimited trust between the locations. Directional firewall policies are edited after tunnel creation so New York can test Tel Aviv with ping, while the reverse direction is intended to allow only RDP toward the New York systems.

> IPsec protects confidentiality and integrity while traffic crosses the public network, but firewall policy still controls what the connected sites may do. Treating a VPN as unrestricted LAN extension would allow a compromise at one site to spread more easily to the other.

**Implemented controls:**

- Created a FortiGate-to-FortiGate site-to-site tunnel.
- Configured the remote WAN gateway, outgoing interface, and PSK.
- Defined the New York and Tel Aviv protected networks.
- Reviewed the negotiated tunnel state.
- Applied directional ping and RDP service requirements.
- Tested allowed and denied behavior from both sides.

Production recommendation: the visible Tel Aviv-to-New York policy still shows `ALL` in the saved service field while the service picker highlights `RDP`. The intended least-privilege result requires saving the service as `RDP` and validating the rule again before production use.

## Key Technical Terms

| Term | Meaning in this chapter |
|------|-------------------------|
| IPsec | A VPN framework that encrypts and authenticates traffic between private networks. |
| Phase 1 | The negotiation that authenticates peers and builds the secure control channel. |
| Phase 2 | The negotiation that protects the selected private subnet traffic. |
| Protected networks | The local and remote subnets allowed to use the site-to-site tunnel. |
| Directional policy | A firewall rule that permits a specific service in one direction without automatically allowing the reverse. |

---

## Detailed Walkthrough

### Step 01 - Start the FortiGate-to-FortiGate tunnel

The IPsec wizard is opened and the FortiGate-to-FortiGate site-to-site option is selected. This tells FortiOS that both endpoints are compatible firewall peers and prepares the Phase 1, Phase 2, routing, and policy objects required for the connection.

> Phase 1 establishes and authenticates the secure control channel between the peers. Phase 2 negotiates the IPsec security associations that protect the selected network traffic carried through the tunnel.

![IPsec tunnel wizard](../../images/07-site-to-site-ipsec-vpn/01.png)

<p><sub><strong>Screenshot 047 - Site-to-Site IPsec Wizard:</strong> A FortiGate-to-FortiGate VPN is selected for the two-site connection.</sub></p>

---

### Step 02 - Configure the remote gateway and PSK

The Tel Aviv WAN address `13.82.92.101` is entered as the remote gateway, `Port1` is selected as the outgoing interface, and both peers are configured with the same pre-shared key. These values allow the New York FortiGate to locate the correct remote peer and prove possession of the shared secret during negotiation.

> A PSK is a shared authentication secret, not an encryption algorithm. In production it should be long, randomly generated, stored securely, and rotated according to policy because disclosure would weaken peer authentication.

![IPsec peer settings](../../images/07-site-to-site-ipsec-vpn/02.png)

<p><sub><strong>Screenshot 048 - IPsec Peer and PSK Settings:</strong> The remote FortiGate WAN address, outgoing interface, and pre-shared key are configured.</sub></p>

---

### Step 03 - Define the protected networks

The New York networks `10.90.1.0/24` and `10.90.11.0/24` are paired with the Tel Aviv networks `10.74.1.0/24` and `10.74.11.0/24`. These local and remote selectors identify the private address ranges that the peers should protect and ensure unrelated internet traffic does not enter this site-to-site tunnel.

> The selectors must be mirrored correctly on both FortiGate devices. If one peer defines different local or remote subnets, Phase 1 may succeed while Phase 2 or actual data traffic still fails.

![IPsec network selectors](../../images/07-site-to-site-ipsec-vpn/03.png)

<p><sub><strong>Screenshot 049 - IPsec Network Selectors:</strong> The New York and Tel Aviv private subnets are entered as the local and remote protected networks.</sub></p>

---

### Step 04 - Confirm the tunnel state

After both peers complete their configuration, the IPsec monitor is opened to review the tunnel and Phase 2 state. An established tunnel confirms that the public endpoints can reach each other and agree on the authentication, encryption, and protected-network parameters.

> Tunnel status proves successful negotiation, not application permission. Cross-site ping or RDP can still fail because of firewall policy, routing, host firewalls, or an unavailable destination service.

![IPsec VPN monitor](../../images/07-site-to-site-ipsec-vpn/04.png)

<p><sub><strong>Screenshot 050 - IPsec Tunnel Monitor:</strong> The site-to-site VPN appears in the FortiGate monitor with its negotiated tunnel state.</sub></p>

---

### Step 05 - Limit New York-to-Tel Aviv traffic to ping

The New York-to-Tel Aviv policy is restricted to the `PING` service. ICMP echo traffic can therefore be used to verify basic cross-site reachability without granting general application access from New York into the Tel Aviv networks.

> Service restriction demonstrates least privilege between connected sites. The encrypted tunnel provides transport, while this directional policy limits the permitted use of that transport to the specific test required by the lab.

![IPsec ping policy](../../images/07-site-to-site-ipsec-vpn/05.png)

<p><sub><strong>Screenshot 051 - New York to Tel Aviv Ping Policy:</strong> The directional IPsec policy explicitly allows the PING service.</sub></p>

---

### Step 06 - Review the Tel Aviv-to-New York RDP rule

The reverse policy is intended to permit Tel Aviv users to reach New York systems only with RDP. The service-selection pane shows `RDP`, while the saved Service field in the same screenshot still displays `ALL`. This means the rule needs a final service correction before it fully matches the intended least-privilege design.

> For the least-privilege design to be complete, the saved service must be changed from `ALL` to `RDP` and validated again. Leaving `ALL` would allow any service accepted by the destination host, which is broader than the stated remote-administration requirement.

![IPsec RDP policy review](../../images/07-site-to-site-ipsec-vpn/06.png)

<p><sub><strong>Screenshot 052 - Tel Aviv to New York RDP Rule:</strong> RDP is selected in the service picker, while the policy field still shows `ALL` and therefore requires a final correction.</sub></p>

---

### Step 07 - Validate cross-site access

The two sides test the intended services after the tunnel is established. The combined validation screen records command-line reachability and an RDP connection attempt across the VPN, connecting the negotiated tunnel state with actual host-to-host traffic.

> Testing both the tunnel and the application separates cryptographic success from policy success. A VPN can be up while traffic is still blocked, so the final check must use the same source, destination, and service described by the rule.

![IPsec traffic validation](../../images/07-site-to-site-ipsec-vpn/07.png)

<p><sub><strong>Screenshot 053 - Cross-Site Service Validation:</strong> Command-line and RDP tests are performed after the IPsec policies are applied.</sub></p>

---

### Step 08 - Compare validation from the remote site

The remote-side screenshots provide the opposite perspective of the same VPN. The RDP client is opened toward the permitted destination, while ping fails in the direction where ICMP is not allowed. Together these results show that the tunnel carries traffic in both directions but the intended services are evaluated directionally.

> A denied test is valuable evidence when denial is the expected policy result. The failed reverse ping demonstrates that encrypted connectivity does not automatically override the service restriction configured for that direction.

![Remote-site RDP validation](../../images/07-site-to-site-ipsec-vpn/08.png)

<p><sub><strong>Screenshot 054 - Remote-Site RDP Test:</strong> The peer-side workstation attempts the RDP connection allowed by the directional VPN policy.</sub></p>

![Blocked remote-site ping](../../images/07-site-to-site-ipsec-vpn/09.png)

<p><sub><strong>Screenshot 055 - Blocked Reverse Ping:</strong> ICMP requests time out from the direction where the policy does not permit ping.</sub></p>

---

## Validation and Summary

Validation includes the negotiated tunnel state, allowed service tests, RDP behavior, and a denied reverse-ping result. The screenshots confirm the lab goal while also documenting the service-field issue that should be corrected for a production least-privilege rule.

This chapter completes the site-to-site VPN workflow. The evidence confirms tunnel negotiation and service testing, while the visible `ALL` service field in the reverse policy remains a documented hardening item before production use.

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
