---
layout: post
title: LLMjacking Bills Are Incident Signals
date: 2026-11-06T10:08:00-05:00
tags: [cloud-ir, ai-security, llmjacking]
linkedin_url: https://www.linkedin.com/feed/update/urn:li:activity:7524487867889385472/
source: https://www.helpnetsecurity.com/2026/11/03/llmjacking-stolen-ai-access-cloud-bills/
type: post
---

A stolen cloud key used to mean a slow cryptomining bill. Now it can mean a near seven-figure AI bill before lunch.

Google Threat Intelligence Group and Mandiant describe 2026 LLMjacking incidents where attackers used stolen access to run AI models at the victim's expense. In one case, more than 500,000 model calls in under eight hours left the victim with nearly a million dollars in token costs. The way in usually wasn't exotic: a misconfigured service account key, a GitHub token, or an active session token. In several cases the AI services were switched off until the attacker turned them on. Would you see that as an alert?

For responders, that changes the reaction clock. Billing is now an incident signal, and containment has to happen in minutes, not at the next cost review.

Everyone should treat AI services as a guarded resource. Keep them disabled in accounts that don't need them, alert on any change that enables a model or raises a quota, and replace long-lived API keys with short-lived, scoped credentials. Good looks like on-call getting paged within minutes of a model being enabled or a spend spike, plus a rehearsed playbook to revoke keys and cap quotas. Wouldn't this make a great table top exercise?

Your cloud bill is now part of your detection stack. Make sure someone is watching it.

#IncidentResponse #CloudSecurity #AISecurity

https://www.helpnetsecurity.com/2026/11/03/llmjacking-stolen-ai-access-cloud-bills/
