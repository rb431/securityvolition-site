---
layout: post
title: Optimizely Script Injection
date: 2026-11-17T13:25:01-05:00
tags: [dfir, supply-chain, web-security, csp]
linkedin_url: https://www.linkedin.com/feed/update/urn:li:activity:7528523606725840898/
source: https://www.bleepingcomputer.com/news/security/optimizely-supply-chain-attack-infects-major-brands-sites-with-malware/
type: post
---

On November 16th, at least 178 organizations served malware to their own website visitors. Most of them weren't hacked in the usual sense.

Attackers abused a flaw in Optimizely's Experimentation signup flow and wrote code into customers' Project JavaScript. Optimizely's CDN then delivered it, from a host every one of those sites already trusted. Report URI found it in 188 of the 1,611 snippets they checked, on sites like Zoom, BBC, CNN and HSBC. The payload swapped crypto addresses, skimmed cards typed into site-owned forms, grabbed cookies and screenshots, and pushed iOS exploit kits at iPhones and iPads.

If marketing owns the testing tool, security still owns the risk. Whether anyone told security is another question, amiright?

The control I'd push hardest is a Content Security Policy that really restricts where scripts load from and where they can send data. A poisoned script still has to phone home, and a tight connect-src gets in its way. Report URI checked the affected sites: a restrictive script-src blocked the payload at 24 organizations, and connect-src stopped the data theft at 17 more.

What I'd want to see:
- script-src and connect-src allowlists, no wildcards added "temporarily", and no 'unsafe-eval'
- CSP reports landing somewhere a human actually reads
- a short list of who (and which tokens) can edit vendor-hosted custom code
- monitoring of the scripts your visitors actually receive

Ran Optimizely Web on Nov 16? Treat cards typed into your own forms that day, and any script-readable session cookies, as exposed.

https://www.bleepingcomputer.com/news/security/optimizely-supply-chain-attack-infects-major-brands-sites-with-malware/

#IncidentResponse #WebSecurity #SupplyChainSecurity
