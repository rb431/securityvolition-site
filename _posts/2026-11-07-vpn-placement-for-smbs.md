---
layout: post
title: VPN Placement for SMBs
date: 2026-11-07T09:15:00-05:00
tags: [edge-devices, vpn, network-architecture, sonicwall]
linkedin_url: https://www.linkedin.com/feed/update/urn:li:activity:7524837057559588865/
source: https://www.cisa.gov/news-events/alerts/2026/11/06/cisa-adds-one-known-exploited-vulnerability-catalog
type: post
---

A SonicWall VPN bug this week is a good reminder: plan for the day your VPN gateway is the problem. Especially with SMB's, a TA connection to the VPN is an all-experience ticket to the network.

CVE-2026-19868 lets someone holding valid credentials for an alternate login method skip SAML on SonicOS SSL-VPN, potentially including the MFA you enforced there. SonicWall says it was exploited, and CISA added it to KEV on Nov 6. Fix: Gen7 7.3.4-7013 or Gen8 8.2.3-8015. No workaround so far. Patch first, then review VPN logins that never touched your identity provider.

Patching fixes the flaw. Architecture decides how much an attacker gets when the next one lands.

Cisco's 2016 SAFE remote access VPN design terminates the VPN at the edge, then puts a separate next-gen firewall and IPS in line "for inspection of the remote users sessions after tunnel termination." Translation: decrypted VPN traffic is still untrusted and you get full security boundary logging for what's hitting your LAN via the VPN.

I would adapt that for a small shop like this (my take, not Cisco's):
- Put the VPN pool in its own zone. Treat it like a DMZ, not an extension of the LAN.
- Enforce policy at a boundary the VPN box doesn't control. VLANs on the same appliance aren't independent: if the gateway is owned, so are its rules. An existing internal firewall works if it's truly separate. If not, a small second firewall is often within reach financially. It just adds a little complexity.
- Allowlist only required destinations and services. No broad LAN access; permit AD-dependent traffic only where the application requires it, and keep management interfaces out of reach.
- Admin access goes through one hardened jump host with its own MFA, never straight to switches, hypervisors, or the firewall UI.
- Keep MFA, just don't make it the only control.
- Send VPN, IdP, and firewall logs somewhere the appliance can't erase.

If most users only need two or three apps, consider app-scoped access instead of broad network access.

None of this stops an auth bypass. It shrinks what the bypass buys and if you are monitoring correct, you can alert on unusual traffic that your day to day users don't generate.

https://www.cisa.gov/news-events/alerts/2026/11/06/cisa-adds-one-known-exploited-vulnerability-catalog
https://www.bleepingcomputer.com/news/security/sonicwall-warns-of-new-ssl-vpn-mfa-bypass-flaw-exploited-in-attacks/
https://www.cisco.com/c/dam/en/us/solutions/collateral/enterprise/design-zone-security/safe-design-guide-edge-remote-access-vpn-ddos.pdf

#DFIR #IncidentResponse #NetworkSecurity
