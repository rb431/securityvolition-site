---
layout: post
title: "Your API Keys Should Have an Expiration Date. Most Don't."
date: 2026-11-21T10:00:01-05:00
tags: [secrets-management, api-keys, non-human-identity]
linkedin_url: https://www.linkedin.com/pulse/your-api-keys-should-have-expiration-date-most-dont-richard-brackett-tuyje/
source: https://blog.gitguardian.com/the-state-of-secrets-sprawl-2026-pr/
type: article
---

Your API Keys Should Have an Expiration Date. Most Don't.

Last month, attackers spent just under 20 hours inside Railway, a cloud hosting platform. Railway says its customers lost at least $740,000 in AI usage on AI provider keys stored in Railway ([Railway](https://blog.railway.com/p/bulletin-compensation)). Under a day of access, and the bill was already well into six figures.

That number is the whole article in miniature. A secret sitting in an environment variable, valid forever, waiting for someone to find it. I'd bet most small and mid-size organizations have dozens of these right now and couldn't tell you who owns half of them. Right?

This one is written for teams without a dedicated security department. You don't need a big budget to fix this. You need a process and a manager who asks the right questions on a regular schedule. Most of the settings you need are already sitting in consoles you pay for.

The real problem is the key that never dies

API keys, cloud access keys, CI tokens, GitHub personal access tokens, and now AI provider keys for OpenAI, Anthropic and everyone else. They all share the same flaw when they're created the lazy way. They work for anyone holding them until a human remembers to kill them. Humans don't remember without a process to remind them.

GitGuardian's State of Secrets Sprawl 2026 report puts numbers on it ([GitGuardian](https://blog.gitguardian.com/the-state-of-secrets-sprawl-2026-pr/)). About 29 million secrets showed up on public GitHub in 2025. Leaks of AI service credentials grew 81% year over year to 1,275,105. The stat that should bother you most is this one: of the secrets GitGuardian confirmed valid back in 2022, 64% were still valid when it retested them in January 2026. Four years of exposure in plain sight, and nobody rotated them.

GitGuardian's explanation for why lines up with what IR teams are seeing too. Teams are afraid rotation will break production, so they don't do it. The same report says about 60% of policy violations involve long-lived credentials, and that internal repositories are roughly six times more likely than public ones to contain hardcoded secrets. So "our repos are private" doesn't buy you much.

What the last month looked like

If you want evidence that this matters right now, the last few weeks handed us plenty.

Cursor and Fireworks. Cursor's November 13 incident update says an attacker obtained an access key Cursor had issued to Fireworks, a third-party training and inference vendor, after Fireworks exposed it ([Cursor](https://cursor.com/blog/security-incident-update)). That key opened a read-only storage bucket of historical coding agent traces. A small number of those traces contained credentials that had never been redacted. From there the attacker reached SaaS tools Cursor uses, and on October 29 used another stolen credential to plant malicious copies of Anthropic's Claude Code extension on the cache behind Cursor's extension marketplace. Cursor says at most 5,000 users may have received the bad build during an 11-hour window. One vendor key opened the door, and a chain of credentials that should have died long ago did the rest. Notice what Cursor did afterward: it rotated every credential in use across its internal systems, even ones with no evidence of exposure, and added network restrictions so stolen credentials can't be used outside their intended location.

Microsoft's agent-framework package on PyPI. On November 17, two malicious versions of the agent-framework package were published to PyPI with a credential stealer bolted on ([Wiz](https://www.wiz.io/blog/malicious-version-of-microsofts-agent-framework-published-to-pypi)). Wiz says the package gets almost 200,000 downloads a month. The stealer runs every time Python starts in an affected environment. It grabs environment variables with names like TOKEN, SECRET, KEY and OPENAI, and reads credential files like .npmrc and .git-credentials. It also requests Azure managed identity and CI OIDC tokens. Here's the part I want you to contemplate. Wiz notes that the stolen managed identity and OIDC-exchanged tokens are valid for up to 24 hours. The npm tokens and static API keys sitting in the same environment stay valid until somebody rotates them. And how did the attacker publish in the first place? Wiz says they appear to have compromised a client certificate and private key for Microsoft's internal release pipeline. Another long-lived credential.

LLMjacking. Google's threat intelligence team published research on November 2 describing incidents where attackers hijacked enterprise access to AI models ([Google Threat Intelligence](https://cloud.google.com/blog/topics/threat-intelligence/llmjacking-monetizing-compromised-cloud-models)). Fraudulent charges topped $100,000 in a single day in some cases, and cumulative charges at some organizations reached $1 million before access was revoked. (Gosh, sounds like the old toll-fraud days when the attackers used your voicemail for long-distance calls.) The dominant way in was exposed or stolen identity material, including IAM credentials, service account keys, GitHub PATs and active session tokens. Once inside, attackers created more API keys to scale up. If nobody is watching key creation, they can mint their own and you'll never notice until the invoice lands.

What the target state looks like

The goal is simple to say. Every credential you have should either be issued just in time and expire on its own, or have a hard expiration date and a rotation process so routine nobody even talks about it.

What works:
- Workload identity federation wherever the platform supports it. GitHub Actions can use OIDC to get a cloud access token that is only valid for a single job and then expires ([GitHub Docs](https://docs.github.com/en/actions/concepts/security/openid-connect)). No cloud secret stored in GitHub at all. OpenAI and Anthropic both now support workload identity federation for supported workloads, exchanging an identity from your provider for a short-lived token instead of a stored API key ([OpenAI](https://help.openai.com/en/articles/5112595-best-practices-for-api-key-safety), [Anthropic](https://platform.claude.com/docs/en/manage-claude/authentication)).
- Managed identities for anything running in your cloud, so the app never holds a static key.
- Expiring keys where you still need a key. OpenAI lets you set an expiration on project keys and lets admins enforce a maximum key lifetime for the whole organization. Anthropic's Console offers presets from 3 hours up to 30 days, and an organization maximum expiration policy removes the "Never" option entirely. GitHub lets organization owners set a maximum lifetime for both fine-grained and classic PATs, and restrict classic PATs entirely, since they have no expiration requirement by default ([GitHub Docs](https://docs.github.com/en/organizations/managing-programmatic-access-to-your-organization/setting-a-personal-access-token-policy-for-your-organization)).
- Keys tied to a service account or a person, never orphaned. Anthropic's documentation makes a good point here: identity-backed keys stop working when the account that owns them is removed, so they don't outlive the person or workload.

Short-lived tokens can still be stolen, and the agent-framework stealer went after them on purpose. The difference is the window. A token that dies in an hour or a day gives an attacker a sprint. A key that never expires gives them a career.

A practical path for teams without a security department

How about we make this concrete? If I were running IT for a 50 or 200 person company with no security staff, here's the order I'd go in.

1. Inventory. Pull the list of keys from every console you can reach: your cloud provider, GitHub, your CI system, OpenAI, Anthropic, your payment processor, your email provider. Write down what each key does, where it lives, who created it and when. You'll find keys created by people who left two years ago. Everyone does.
2. Put a secrets manager in the middle. Your cloud provider's built-in vault is fine. So is a hosted secrets manager. The point is one place where secrets live, with access logs, so apps and pipelines pull secrets at runtime instead of reading them from .env files and wiki pages.
3. Name an owner for every credential. A named person. Team aliases don't count. If nobody will own a key, that's your sign it should be deleted.
4. Set expiry everywhere the provider supports it, and turn on the organization-level maximum lifetime policies. That one setting stops the next "Never expires" key from being created at 11 PM by someone in a hurry.
5. Write a rotation runbook for each important credential. Create the new key, deploy it, confirm it works, revoke the old one. OpenAI's own guidance describes exactly that sequence. Then automate it wherever you can.

While you're in those consoles, set hard spend limits on your AI provider accounts. OpenAI notes that spend alerts alone don't stop API traffic, and hard enforcement does. That won't stop a theft, but it caps how bad the LLMjacking bill can get.

Rotation should be boring

The reason 64% of those leaked secrets were still valid after four years is that rotation is scary when you've never done it. The fix is to do it so often it stops being an event.

Pick a cadence and stick to it. Rotate on a schedule shorter than the key's lifetime, so expiry is a backstop instead of an outage. Anthropic emails the key's creator before expiration on longer-lived keys, but I wouldn't rely on someone's inbox as your reminder. Put the dates in a calendar or a ticket queue that the owner and their manager both see.

Test rotation during a quiet week. The first time you rotate your production database credential should never be at 2 AM with an attacker in the environment. If rotating a key means a code change and a prayer, that's the thing to fix first.

Managers own this part

Secrets management usually breaks down on governance. Engineers create keys to get work done, and nobody ever asks them to clean up. Then, years pass.

This is where human management oversight earns its keep. A manager doesn't need to understand OIDC claims to run a good review.

The bar here:
- Every credential has a named owner, and the list is reviewed on a regular cadence, monthly for anything touching production or money.
- A one-page report a manager can read in five minutes: how many keys exist, how many have no expiration, how many are past their rotation date, and how many have no owner.
- Any key that truly can't expire needs a written exception, approved by a manager, with a reason, an owner, a compensating control like an IP allowlist or tight scope, and a review date.
- Departing employees and ending vendor contracts trigger a credential review, every time.

That exception process matters more than people think. Some legacy systems really can't do short-lived credentials. Fine. But "we can't" should be a decision with someone's name on it.

When a key leaks anyway

It will happen eventually. Here's how an IR team scopes it, and you can follow the same steps.

First, revoke or disable the key right away, then rotate everything that was stored next to it. If a stealer had the environment, assume every secret in that environment is burned. Cursor rotated credentials even where it had no evidence of exposure, and I think that was the only call.

Then answer the scoping questions from the logs:
- When was the key created, when did it leak, and when was it revoked? That's your exposure window.
- What could the key reach? Scope matters more than anything else here.
- What did it actually do? Pull provider audit logs and AI usage reports for that window, and look for unfamiliar IPs, new regions, sudden spikes in model usage, and new keys or service accounts created by the compromised identity. (I sincerely hope this isn't the first time you're looking at those...)
- Where else was it used? Check whether the same secret was copied into other repos, pipelines, chat messages or tickets. GitGuardian found about 28% of incidents start in collaboration and productivity tools.

If the key was an AI provider key, open a support case with the provider fast. Railway's bulletin shows refund policies vary a lot by provider, and some are case by case.

The short version

Non-expiring keys are a bet that nobody will ever find them. The last month showed what happens when that bet loses. Move what you can to short-lived, federated credentials. Put an expiration date on everything else. Make rotation so routine it's boring. And give a manager a short, regular report so this stops depending on someone's memory.

If you want hands-on practice scoping leaked credentials and digging through cloud audit logs, check out the DFIR CTF Board at https://ctf.securityvolition.com for a running list of DFIR and blue-team CTFs.

Sources:
Railway, Compensation for unauthorized AI usage (Nov 12, 2026): https://blog.railway.com/p/bulletin-compensation
GitGuardian, State of Secrets Sprawl 2026 announcement (Mar 17, 2026): https://blog.gitguardian.com/the-state-of-secrets-sprawl-2026-pr/
Cursor, An update on our recent security incident (Nov 13, 2026): https://cursor.com/blog/security-incident-update
Wiz, Microsoft agent-framework compromised (Nov 17, 2026, updated Nov 18): https://www.wiz.io/blog/malicious-version-of-microsofts-agent-framework-published-to-pypi
Google Threat Intelligence, The Rise of LLMJacking (Nov 2, 2026): https://cloud.google.com/blog/topics/threat-intelligence/llmjacking-monetizing-compromised-cloud-models
GitHub Docs, OpenID Connect: https://docs.github.com/en/actions/concepts/security/openid-connect
GitHub Docs, personal access token policy: https://docs.github.com/en/organizations/managing-programmatic-access-to-your-organization/setting-a-personal-access-token-policy-for-your-organization
OpenAI, Best practices for API key safety: https://help.openai.com/en/articles/5112595-best-practices-for-api-key-safety
Anthropic, Claude API authentication: https://platform.claude.com/docs/en/manage-claude/authentication

#SecretsManagement #IncidentResponse #CyberSecurity
