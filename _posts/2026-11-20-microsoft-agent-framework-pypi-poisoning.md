---
layout: post
title: Microsoft Agent Framework PyPI Poisoning
date: 2026-11-20T09:05:34-05:00
tags: [dfir, supply-chain, pypi, ai-agents, ci-cd, azure]
linkedin_url: https://www.linkedin.com/feed/update/urn:li:activity:7529545480033222657/
source: https://www.wiz.io/blog/malicious-version-of-microsofts-agent-framework-published-to-pypi
type: post
---

Tuesday afternoon, someone published two poisoned versions of Microsoft's agent-framework package to PyPI. Version 1.26.1 went up at 3:35 PM ET, 1.26.2 followed at 4:08, and both were gone by about 4:30. Under an hour. The package gets about 41,000 downloads a week. And I bet most people trust PyPI implicitly.

Each one drops a .pth file into site-packages. Python runs those on every start, so the stealer fires even if nothing ever imports the package. It sweeps environment variables and credential files, then asks Azure for managed-identity tokens and Azure DevOps OIDC tokens. Version 1.26.2 added GitHub Actions OIDC tokens on top.

How they got published is still unconfirmed. Wiz found no matching release in Microsoft's repo, which suggests an upload outside the normal release process.

Why go after an agent framework? It runs on CI runners and cloud workloads with identities attached, right where the keys live.

In practice, that means:
- pinned versions in lockfiles with hashes, so a new release can't slip into a build unannounced
- a cooldown before new versions reach builds. A few days' wait would have skipped this one entirely
- short-lived CI credentials through OIDC instead of static secrets, with managed identities scoped to what the job needs. OIDC tokens got stolen here too, but they expire. A static secret doesn't
- egress limits on build runners. This stealer posted everything to webhook.site. How about asking why your runners can reach a public request-inspection site at all?

If the bad versions ran, bumping to 1.26.0 is only the start. Scope it first: which runners and container images pulled agent-framework between 3:35 and 4:30 PM ET November 17th? Check internal mirrors and caches too. Then look for the .pth files, search proxy logs for the webhook.site URL, rotate every credential those hosts could reach, and review sign-ins for the managed identities and federated credentials they could touch. Wiz notes stolen managed-identity and OIDC-exchanged tokens stay valid for up to 24 hours.

For managers, the question is simple. Do your pipelines pull whatever is newest the minute it ships? Don't expect the public repositories to be inspecting code for you.

https://www.wiz.io/blog/malicious-version-of-microsofts-agent-framework-published-to-pypi

#SupplyChainSecurity #IncidentResponse #AISecurity
