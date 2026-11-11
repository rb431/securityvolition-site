---
layout: post
title: Keep the Admin UI Off the Internet
date: 2026-11-11T11:38:01-05:00
tags: [barracuda, edge-devices, management-plane]
linkedin_url: https://www.linkedin.com/feed/update/urn:li:activity:7526322352071147520/
source: https://trust.barracuda.com/security/pre-authentication-authorization-bypass-command-injection
type: post
---

Barracuda confirmed recently that two Email Security Gateway flaws, CVE-2026-116606 and CVE-2026-111560, are being exploited against a small number of organizations. Chained, they give an attacker root on the appliance.

The important point, though, is exploitation *requires* access to the administrative interface. That's a precondition you, the defender, control.

Patch, yes. ESG 9.4.0.030 or later, and turn automatic patching back on if someone turned it off. But the control that makes this nearly irrelevant is management-plane isolation: admin interfaces for email gateways, firewalls, VPNs, and load balancers are reachable only from a dedicated management network or jump host, never from the internet. Here's an idea: how about a jump box in Azure, AWS, or other IaaS provider with a fixed IP address that you can allow-list on the appliances. And not just the appliances, how about an inbound ACL on the Internet link that drops anything to the management interface that isn't your fixed IP? Leave the jump box offline until you need it and furthermore, secure it with MFA-enabled access to get to it.

What this should look like:
- A scheduled external scan that alerts when any admin UI answers from outside, because change management isn't 100%, amiright?
- Admin access through a hardened jump host with phishing-resistant MFA
- Admin logins and config changes forwarded to your SIEM, not just stored on the box. Miss this one at your peril.

If your ESG admin UI was exposed before you patched, treat it as a threat hunting exercise, not a closed ticket. Review config changes, restored backups, and new accounts since early November.

Edge appliances keep getting hit because their management planes sit on the internet. That part is fixable.

https://trust.barracuda.com/security/pre-authentication-authorization-bypass-command-injection

#DFIR #IncidentResponse #EdgeSecurity
