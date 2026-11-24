---
layout: post
title: "Who Else Gets to Read Your Prompts?"
date: 2026-11-23T19:42:26-05:00
tags: [article, ai-supply-chain, vendor-management, canary-tokens, prompt-privacy, third-party-risk]
linkedin_url: https://www.linkedin.com/pulse/who-else-gets-read-your-prompts-richard-brackett-8tave/
source: https://www.dragos.com/blog/ai-supply-chain-security-canary-tokens
type: article
---

Who Else Gets to Read Your Prompts?

HD Moore, Chief Research Officer at Dragos, published a research post on November 19 that everyone buying or already using an AI service should read. Over five months, from July through November 2026, his team sent about two million prompts packed with unique canary tokens to widely used AI services, then watched where those secrets showed up ([Dragos](https://www.dragos.com/blog/ai-supply-chain-security-canary-tokens)). If someone fetched the URL or tried the key, Dragos knew that prompt had left the path you thought you paid for.

The part I want managers to contemplate is simpler than the incident write-ups. You are choosing partners, and you are choosing what organizational data those partners get to see. If you don't make those choices on purpose, price and convenience will make them for you. Dragos put it plainly: if you don't decide where your prompts go, the lowest price will decide it for you.

What the canaries showed

The prompt often does not stay with one company. It can pass through a router, then another router, then a hosting provider, then a reseller who won the request by posting the lowest price. Every hop can read the whole prompt and the whole response. The tokens started firing within days of the first seeds in July, and Dragos says the July tokens were still firing in November. They started with free [Thinkst Canarytokens](https://canarytokens.org/). Of about two thousand routes they later traced, several hundred included a system they could not identify, and a handful left the advertised path, including one marketplace route to Anthropic that also passed through OpenRouter, a hop the seller had not disclosed.

Attackers were actively stealing data from three hosting providers. The earliest incident dates to mid-July, and none of the three knew they were breached until Dragos told them. The largest case involved a couple hundred of Dragos's AWS keys, each sent in exactly one request during short windows in late October, and every request in those windows was stolen. Some came from requests the provider rejected before a model ever ran, so the copy was happening at the front door. In the other two breaches, some stolen requests were marked for Zero Data Retention. The people who got the keys tested whether they were live, in some cases within a few hours, then listed S3 buckets, searched Secrets Manager and Parameter Store, and tried to enable AWS Bedrock and add their own IAM users. The bait keys were used for tens of thousands of AWS API calls.

Some marketplace resellers did not need an exploit. They listed a popular model at the lowest price, turned on request logging, and waited. Dragos describes people on home broadband opening secret URLs, or typing stolen passwords at an SSH root prompt by hand. In one test, two prompts through a single seller were both touched about a day and a half later, while the same model through a different seller was never touched. The leak followed the seller.

Privacy settings were ignored even when nobody tried a credential. A Chinese AI lab's systems fetched the secret URLs from more than half of the prompts Dragos sent to its models between July and October, including roughly two-thirds marked "no data collection" or Zero Data Retention, typically two or three days later. Several U.S. hosting providers resell that lab's models, and prompts sent there triggered the same scraper. One large U.S. provider was still crawling a secret URL more than two months after a single call to its search endpoint. Twenty-four days after a prompt Dragos sent to a Chinese provider, the secret link showed up in a Feishu channel (known as Lark outside of China) and was opened within two minutes from three desktops and three phones in Beijing and Shanghai. Dragos's disclosure had included only the first eight characters of the link, so whoever pasted it had a stored copy of the full prompt, more than three weeks after the request was sent to a U.S.-based provider with the ZDR flag.

Dragos did not name the three breached hosting providers in the post. They said they were working with about a dozen providers, and that stolen keys and secret URLs were still being accessed months later.

What landed after the post

A few public notices have appeared since November 19. Read them as the vendors' own statements, not as a complete map of everyone Dragos touched.

On November 10, Phala published a [preliminary incident report](https://phala.com/posts/phala-november-preliminary-incident-report). On November 23 it published a [detailed incident report](https://phala.com/posts/phala-november-detailed-incident-report) that supersedes the preliminary one. A third-party researcher showed that request data sent to Phala's DeepSeek V4 Flash 0731 deployment, reached through OpenRouter, had been exposed. Phala now puts the established exposure window at October 21, 2026 at 02:25 UTC through November 1 at 02:03 UTC, narrower than the earlier precautionary range of October 21 at 00:00 UTC through November 8 at 05:35 UTC. Canaries planted in requests to Phala showed later misuse only of tokens sent on October 21 and 22. This lines up with Dragos's late-October batch and its note that one provider had already published a preliminary security notice, without proving Phala is one of the three unnamed hosting breaches.

On November 21, Together AI said an unauthorized party downloaded stored prompt samples after a credential for a private Hugging Face repository was committed to Together's public GitHub repository, xorl, by an AI coding agent ([Together AI](https://www.together.ai/blog/security-event-prompt-leakage-for-requests-in-june-and-july-of-2026)). The samples came from serverless requests to MiniMax M3, GLM-5.2, and Kimi K2.6, mostly on June 24, July 4, July 8, and July 14. Five of the credentials later tested were Dragos canaries. Together's review of the sample contents is still underway.

On November 22, OpenRouter said the research did not show a breach of OpenRouter itself, thanked the Dragos report, off-boarded three providers, removed 125 endpoints from Zero Data Retention eligibility, and notified potentially affected customers ([OpenRouter](https://openrouter.ai/blog/announcements/data-processing-incidents-on-inference-providers/)). It listed notices from Phala, Together AI, Novita, and Atlas Cloud. Novita said some MiniMax requests on its platform between May 28 and November 11, 2026, were forwarded to MiniMax's official API instead of being processed on Novita-hosted infrastructure, including traffic that reached Novita through OpenRouter ([Novita](https://novita.ai/blog/minimax-requests-privacy-notice/)).

Atlas Cloud's November 23 [security notice](https://www.atlascloud.ai/blog/ai-updates/security-and-privacy-commitment) says HD Moore's testing showed a third party opened canary links from MiniMax M3, M2.7, and M3.1 Flash prompts sent through Atlas in Q3 and early Q4 2026, after the requests reached MiniMax's official API rather than Atlas's own systems. Atlas stopped forwarding to that API and told affected customers to treat secrets in those requests as exposed.

"We turned on ZDR" and "we bought from a U.S. dashboard" are not the same thing as knowing who can read the prompt.

Where your data already goes

Before you argue about models, list the places your organization's data already leaves the building. Shadow AI will be on this list. Official pilots will be too. For each one, write down the data it can see and the control that matters most if that path is going to stay open. The product names below are examples of services companies commonly use, not a charge against those vendors. They should go through the same vetting and data-routing questions.

- General-purpose chat, such as ChatGPT Enterprise, Claude, and Gemini: whatever staff paste in, from strategy drafts to customer records. Decide what is allowed to be pasted.
- Productivity suites, such as Microsoft 365 Copilot and Gemini for Google Workspace: mail, files, calendars, and meeting content already sitting in the suite. The control that matters is which libraries and mailboxes it can see.
- Coding assistants and IDE copilots, such as GitHub Copilot, Cursor, and Amazon Q Developer: source code, comments, internal URLs, sometimes keys. Keep secrets out of the workspace and scan prompts before they leave.
- CI and pull-request review bots, such as GitHub Copilot and CodeRabbit: the patch, the discussion, leftover credentials. Allowlist which bot talks to which repository.
- Meeting transcription and summaries, such as Zoom AI Companion, Otter.ai, Fireflies, and Microsoft Teams Copilot: strategy talk, personnel issues, unreleased numbers, customer names. Default these tools off for sensitive meetings.
- Support chatbots and ticket copilots, such as Zendesk AI, Intercom Fin, and Salesforce Agentforce: customer names, account numbers, whatever someone pasted to close a case. Strip identifiers before the prompt leaves.
- Document, contract, and RFP analysis, such as Notion AI, Harvey, and Ironclad AI: deal terms, legal exceptions, sometimes the whole corpus. Use an approved instance with a no-training term, and keep M&A files off the general tool.
- SOC and alert triage assistants, such as Microsoft Security Copilot, CrowdStrike Charlotte AI, and Google SecOps Gemini: detections, internal hostnames, architecture notes, ticket text. Pin the model, scrub the prompt, and log every call.
- Browser assistants, agent extensions, and "skills," such as Grammarly and ChatGPT or Claude browser extensions and agents: whatever the user can see, the vendor can be sent. Inventory them and allowlist them.
- Email and inbox summaries, including Microsoft 365 Copilot and Gemini for Google Workspace: internal decisions, legal-hold mail, shared mailboxes. Don't point a third-party summarizer at privileged or regulated accounts.
- Knowledge-base and RAG tools over SharePoint, Confluence, or file shares, such as Glean, Microsoft 365 Copilot, and Notion AI: the corpus itself, including libraries nobody realized were included. Inherit permissions and exclude high-sensitivity sites.
- Sales call intelligence and CRM copilots, such as Gong and Salesforce Einstein: deal pricing, negotiation notes, customer conversations. Retention limits and access control matter as much as the model name.
- Recruiting, resume screening, and HR policy chatbots, such as Paradox, HireVue, and Eightfold: candidate PII, compensation, disciplinary questions. Legal and privacy review first, then a tight retention setting.
- Translation of internal documents, such as DeepL or a general chat tool used on a full file: the document, in full. Treat a translation request as sending the file to a third party.
- Notebook and data-analysis copilots, such as Databricks Assistant or a chat tool used against query results: query results, customer extracts, sometimes a paste from production. Use minimized or synthetic samples.
- Invoice, procurement, and intake extraction, such as Coupa and Ramp: bank details, amounts, vendor identifiers. Keep this on an approved path with a written retention cap.
- Customer-facing website or app assistants, such as Intercom Fin, Zendesk AI, and Salesforce Agentforce: whatever your customer types, plus the retrieval tool behind the bot. That is their data and your prompt history in one place.
- Model routers and inference aggregators, such as OpenRouter, AWS Bedrock, Azure AI Foundry, and Together AI: the prompts your apps and agents send, plus whatever context those apps attach. Pin the upstream providers, and ask where requests actually run.

If you can't produce that inventory, you are already behind.

How to vet an AI partner

Treat the model vendor, the router, the reseller, and the "thin" app your staff actually clicks as different partners, even when they arrive in one invoice. A dashboard in the United States is not a guarantee about the hop behind it.

The bar here:
- Who, exactly, runs inference for each model you use? Get a named subprocessor list, the region, and whether that path can change without written notice.
- Is the vendor pinning a provider, or sending the request into a default pool that awards work to the lowest bidder? If they can't tell you, you don't have a partner. You have a broker.
- What does Zero Data Retention actually bind? Ask for the contract language, not the badge on the model card, and ask whether the next hop is required to honor the same flag. Dragos's tracing and Novita's forwarding notice are why that question exists.
- Who can read prompt logs, for how long, and from where? A reseller who enables request logging and works from a home connection is not a theoretical risk. Dragos watched it happen.
- Will they notify you when a hop is off-boarded, when a ZDR label is removed, or when a researcher shows up with canary evidence? Put the notification trigger in the contract.
- Do they train on your prompts, keep samples for testing, or share data with a model lab? Together AI's notice is a reminder that even samples held for testing can walk out of the building if a credential does.
- Can they support private connectivity, customer-managed keys, and a no-training term for the data you actually care about? If the only answer is a marketing page, keep that vendor off the sensitive work.
- Payment and onboarding red flags matter. Dragos's advice is to stay away from reseller marketplaces, in part because some of that capacity comes from stolen keys, and a few of those services only accept cryptocurrency for a reason.

SOC 2 is fine, and it will not answer "who else got a copy last Tuesday?"

How to control what gets routed

What works:
- Make a living inventory of every official and unofficial AI path, including browser extensions, personal API keys, and "just a trial" accounts. Name an owner for each one.
- Classify the data that might enter a prompt, and write a simple rule for what is allowed to leave. Secrets, raw customer dumps, privileged email, and unreleased deal documents should never leave.
- Put an approved gateway or pinned route in front of the tools that will stay. Choose the upstream providers yourself. Do not accept a default marketplace pool for anything you would be embarrassed to see in someone else's logs.
- Minimize before you send. Some providers will scrub known credential formats, and that can't hurt. It is also not enough. Plenty of sensitive data is not shaped like an API key.
- Log what left, which user or service sent it, and which provider was supposed to receive it. When a router later says a hop was off-boarded, you will need that list.
- Seed canary tokens into the context that your agents and pipelines already send, and treat any use of one as an incident. A free Canarytoken from Thinkst is enough to start.
- Prefer a first-tier provider, with one contract and one privacy policy, for work that matters. Dragos notes this can still leak through a data-sharing agreement or a provider breach, and that it is still the smallest exposure you can buy if you are not hosting it yourself. Self-hosting is the only option where nobody else gets to read the prompt, and most mid-size teams should reserve it for the most sensitive work.
- Don't let an agent send whatever it can read. If it can see a secrets file, a mailbox, or a production dashboard, it will eventually include that in a prompt. That is a design problem, not a user-awareness problem.

If a token lights up

Handle it like any other suspected credential or data exposure, with one extra question: which hop actually saw this?

Confirm which canary fired and which vendor, model, and pipeline carried it. Assume the entire prompt is in someone else's hands, not just the token. Rotate any live secrets that were in that context, even if the canary was bait. Ask the named provider for logs, and ask whether the request was forwarded. Watch your own cloud logs for the pattern Dragos documented: key-validation calls, S3 list operations, Secrets Manager and Parameter Store reads, Bedrock enablement, new IAM users.

If a router or provider later mails you about a window, use your gateway logs to see whether you had traffic in it. Phala's established exposure window and Novita's May-through-November forwarding window are the kind of thing you can't answer from memory. If you have no logs, say so, and fix the logging before you declare the incident closed.

TLDR

Dragos spent five months planting unique canaries in about two million AI prompts and watched them get fetched, replayed, and abused. Attackers were already inside some hosting providers. Some resellers were reading prompts with ordinary logging. Privacy flags were ignored, and at least one full prompt showed up in a company chat weeks later. Public notices have started to fill in names and dates. They will not be the last.

Mid-size teams need a partner list, evidence about where requests actually go, and a rule for what data may leave. Inventory the use cases you already have. Pin the path. Minimize the prompt. Seed a canary. Treat a hit as an incident. A privacy setting that only binds the first hop is a starting point, not a control you can bet the company on.

If your team wants hands-on practice scoping leaked credentials, reading cloud audit logs, and tracing where a secret went after it left your environment, check out the DFIR CTF Board at https://ctf.securityvolition.com for a running list of DFIR and blue-team CTFs.

Sources:
Dragos, Three breaches, endless leaks: Your AI supply chain needs canaries, HD Moore (Nov 19, 2026): https://www.dragos.com/blog/ai-supply-chain-security-canary-tokens
Thinkst Canarytokens: https://canarytokens.org/
Phala, November preliminary incident report (Nov 10, 2026): https://phala.com/posts/phala-november-preliminary-incident-report
Phala, November detailed incident report (Nov 23, 2026): https://phala.com/posts/phala-november-detailed-incident-report
OpenRouter, Data Processing Incidents on Inference Providers (Nov 22, 2026): https://openrouter.ai/blog/announcements/data-processing-incidents-on-inference-providers/
Together AI, Security event: Prompt leakage for requests in June and July of 2026 (Nov 21, 2026): https://www.together.ai/blog/security-event-prompt-leakage-for-requests-in-june-and-july-of-2026
Novita, Privacy Notice Regarding MiniMax Requests on Novita (Nov 22, 2026): https://novita.ai/blog/minimax-requests-privacy-notice/
Atlas Cloud, Security notice: MiniMax's official API and how we protect your data (Nov 23, 2026): https://www.atlascloud.ai/blog/ai-updates/security-and-privacy-commitment
Ars Technica, Researcher plants 2m canaries into the AI supply chain, Dan Goodin (Nov 20, 2026, updated Nov 21): https://arstechnica.com/security/2026/11/a-leaky-ai-supply-chain-is-exposing-secret-user-data-at-an-alarming-rate/

#AISecurity #IncidentResponse #ThirdPartyRisk
