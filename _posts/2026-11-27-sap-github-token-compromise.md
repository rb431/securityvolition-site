---
layout: post
title: SAP GitHub Token Compromise
date: 2026-11-27T08:10:00-05:00
tags: [dfir, github, tokens, supply-chain, incident-response]
linkedin_url: https://www.linkedin.com/feed/update/urn:li:activity:7532068281029935105/
source: https://www.securityweek.com/sap-confirms-cyberattack-source-code-reportedly-compromised/
type: post
---

SAP confirmed a security incident after detecting suspicious activity in its environment. Insiders told Der Spiegel that attackers used stolen SAP employee authentication tokens to copy and alter source code on GitHub. Handelsblatt reports SAP replaced signing keys, restricted GitHub access, and paused automated development pipelines. SAP says it has found no evidence of unauthorized access to customer business data, and no report so far says altered code reached customer releases.

IR teams will still get the question from managers: do we trust the SAP components we already pulled, and should we be watching for new signing keys?

This week, one employee's GitHub account was enough to reach 1Password's source code and CI/CD. A long-lived token with broad access is still the shortest path into both.

What works: short-lived, tightly scoped developer tokens, with a revocation and rotation path you've actually rehearsed. Then verify vendor components against the current signing keys, not last month's.

How fast could your team revoke a developer's tokens and rotate every secret those tokens could have touched?

https://www.securityweek.com/sap-confirms-cyberattack-source-code-reportedly-compromised/

#DFIR #IncidentResponse
