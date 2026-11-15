---
layout: post
title: Agent Skills Are the New Browser Extensions
date: 2026-11-15T08:41:01-05:00
tags: [ai-security, agent-skills, least-privilege]
linkedin_url: https://www.linkedin.com/feed/update/urn:li:activity:7527727360041844736/
source: https://www.helpnetsecurity.com/2026/11/10/acronis-ai-agent-cyberattacks-report/
type: post
---

Acronis researchers found more than 575 malicious OpenClaw skills posing as ordinary utilities. They delivered trojans, cryptominers, and infostealers to Windows and macOS machines.

The uncomfortable part: an agent skill is instructions plus executable code, and it runs with whatever permissions the agent already has. Install a malicious skill and you've handed an attacker your agent's access to files, tokens, and connected SaaS applications. The same report describes an operator whose AI tooling was configured to run without approval prompts. Plenty of defenders do that too, for convenience. 

For responders, this is the new browser-extension problem, and it needs the same answer: an allowlist for agent skills and plugins, backed by least-privilege agent identities.

Done right, that's:
- Skills come from an approved internal catalog, pinned to reviewed versions, not pulled ad hoc from public marketplaces
- Each agent runs under its own scoped identity with short-lived credentials, not a developer's full session
- Human approval stays on for anything that executes code, touches credentials, or sends data out
- Skill installs and agent tool calls are logged where your IR team can actually query them

When an agent workstation gets compromised, the first scoping question is simple: what could this agent reach?

https://www.helpnetsecurity.com/2026/11/10/acronis-ai-agent-cyberattacks-report/

#AISecurity #DFIR #IncidentResponse
