---
layout: post
title: "Who Is Watching the People Who Run Your IT?"
date: 2026-11-17T16:15:04-05:00
tags: [article, third-party-risk, msp, rmm, screenconnect, remote-access, vendor-oversight]
linkedin_url: https://www.linkedin.com/pulse/who-watching-people-run-your-richard-brackett-hpwre/
source: https://www.huntress.com/blog/cve-2026-84869-screenconnect-technician-exploitation
type: article
---

On November 12, a technician at a managed service provider joined a blank-named remote session on their own ScreenConnect server. Moments later, their ScreenConnect client ran a batch file that the other side of the session had pushed over. No prompt. No click. The script relaunched itself hidden, reported the new victim to a Telegram bot, downloaded an installer from an attacker domain, and started a rogue ScreenConnect client named after Windows Update that pointed at the attacker's own relay. Huntress clocked the whole thing at about 9 seconds.

Now think about what sits on that technician's workstation. RMM consoles. Saved credentials. The password vault. A path into every customer that MSP supports. Huntress calls a technician's workstation an exceptionally attractive target, and that's an understatement. If your company runs on that MSP, that laptop is part of your attack surface whether you have ever seen it or not.

What happened

The flaw is CVE-2026-84869, a ScreenConnect client-side bug that ConnectWise disclosed on September 8 with a CVSS score of 9.9. CISA added it to the Known Exploited Vulnerabilities catalog on September 11. In both Support and Access sessions, a guest could trigger file-transfer actions that the technician's client processed without authorization or confirmation, including running the file with elevated rights. The fix is host client 26.6.5 or later. Patching the server alone doesn't help, because the vulnerable code runs on the technician's machine. ConnectWise's interim mitigation is to remove the TransferFiles permission.

Huntress's November 16 write-up confirms exploitation in the wild and fleshes out the details:

- The November 12 compromise above, only about 9 seconds start to finish.
- On November 13, a technician at a second MSP joined a session, and two seconds later the guest pushed a harmless marker.txt file. About three seconds after that, the technician's client ran it. That's a canary, a test to prove the exploit works before anything real gets sent. About a minute earlier, the same technician had opened one of the fake crypto-named sessions I'll get to shortly.
- In October, an attacker joined real customer support sessions as an extra guest and tried a path-traversal trick to drop a Sync.bat file into technicians' Startup folders, where it would run at their next logon.
- Between October 20 and November 16, attacker guests made at least 688,000 "run file" requests for update.bat-style files against 42 MSP ScreenConnect servers from 29 IP addresses. They just sit there and keep asking, waiting for any technician to join.

Then there's the bait. On November 13, Huntress reported more than 2,200 unauthorized guest sessions with names like KUCOIN-SRV and LAPTOP-BINANCE planted on 118 self-hosted ScreenConnect servers belonging to 109 organizations, starting November 3. Huntress hasn't seen those sessions push files or run anything. Its leading hypothesis is bait. A server that looks like it belongs to a crypto exchange is exactly the kind of session a curious technician opens. How did the attacker get them registered? A guest only needs the server's relay address, which is embedded in the guest installer, and ScreenConnect serves those installers without authentication by design. On the first server Huntress examined, an online file-scanning service had grabbed the MSP's installer weeks before the fake sessions showed up.

This is part of a bigger RMM season. On November 11, Huntress responded to SCKit, worm-like activity that used Run commands on cloud-hosted panels to install rogue ScreenConnect clients and session-cookie relays on hundreds of managed endpoints, apparently by abusing a vulnerable third-party extension on the server side. Over on the N-able side, a proof of concept targeting N-central dropped on November 12 as part of a series of public zero-day disclosures, and N-able reported exploitation in the wild on November 13. Rapid7 says the flaw N-able reported strongly correlates to CVE-2026-90436, the Apache Axis remote code execution bug. N-able shipped N-central 2026.4 HF3 on November 16. That release disables the Legacy API that exposed the bug.

Here's the bombshell that should bother everyone reading this. Huntress noted that many technicians in these cases were still running clients as old as 22.x weeks after the fix shipped. A 9.9 on the KEV list since September, and some of the people holding keys to hundreds of customer networks hadn't updated the tool they use to turn those keys.

Remote access is a door somebody else holds the key to

Every RMM agent or vendor VPN account in your environment is a door, and you don't hold the key. Most companies can't produce a complete list of those doors in under a day. Can yours? There's *always* the remote support tool a line-of-business vendor installed years ago that nobody remembers.

Start by finding every path in, then take away the always-on part.

In practice, that means:

- A current inventory of every remote-access path: RMM agents, remote support tools, vendor VPN accounts, jump hosts, and delegated admin rights in your cloud tenants
- No standing access to sensitive systems. Sessions get approved against a ticket and expire on their own
- Unattended access only where it's justified, and pulled when the reason goes away
- Installer and enrollment links kept private, and rebuilt if they leak. Huntress recommends exactly this after seeing how easily the bait sessions got registered

Named people, scoped tools

Shared logins are how you end up unable to answer the most basic question in an investigation: who was on that session? Every technician who can touch your environment should be a named individual you can identify.

What I'd want to see:

- Per-technician named accounts on the RMM and remote support consoles, with no shared admin logins
- Phishing-resistant MFA (FIDO2 keys or passkeys) on those consoles and on the MSP's identity provider
- Role permissions scoped to the job. TransferFiles is the perfect example. ConnectWise's own stopgap for this CVE was removing it. How many technicians touching your environment actually need to push and run files every day?
- A standing rule that technicians don't join sessions they can't tie to a customer. Huntress recommends that one too, and it would have defused the crypto bait entirely

See it with your own eyes

If the only record of what your MSP did lives in your MSP's console, you're trusting the party you'd be investigating. Ask any IR team how that goes.

The bar here:

- Session logs and recordings you can pull yourself without opening a ticket, kept for at least 90 days
- Egress controls so remote tools in your environment can only reach your MSP's known relay. Huntress lists outbound relay connections to domains you don't own, often on port 8041, as a detection opportunity. On your network, that's a firewall rule
- An alert any time a new RMM or remote-access agent appears, especially a ScreenConnect client pointed at a relay that isn't yours
- Segmentation so whoever owns the management plane can't reach your backups and domain controllers in one hop
- If you run ScreenConnect yourself, a hunt for the host client launching scripts from its Temp folder. That's the host-side fingerprint of this exploit, and Huntress spells it out

Patch SLAs for the vendor's own tools

Everyone writes patch SLAs for servers and laptops. Almost nobody writes them for the MSP's own tooling, which is the stuff with the most access. Put a number on it. My opinion: anything on the KEV list in the provider's own stack gets fixed within 7 days, technician clients included, and they show you the version report. If they can't produce one, that tells you something.

Put it in the contract

Nobody reads the MSA until something breaks, amiright? Read it now.

Done right, that's:

- Breach notification measured in hours, with a named contact on both sides
- Right to audit, plus a SOC 2 Type II report that actually covers the tools they use on you
- Your access to the logs that cover your environment, during the contract and for a set period after
- Incident response cooperation spelled out: preserve evidence, join your incident bridge, hand over the artifacts your responders need, and keep their people engaged until it's closed
- Offboarding terms: when a technician leaves or the contract ends, access and agents come out within a set window and you get proof

Outsourcing all of your IT is the biggest trust grant you'll ever sign

Now the harder conversation. Plenty of small and midsize companies outsource everything to one provider. Help desk, servers, cloud tenants, backups, security monitoring, the works. That can be a perfectly rational business decision. It also means one company, and every technician laptop inside it, holds the keys to the kingdom.

You can hand off the work. The accountability stays with you. When the breach notice goes out, it carries your company's name. Regulators and your board will ask what leadership did to oversee the provider, and "we trusted them" won't hold up for long.

Oversight means management actually looking at how the provider operates, on a schedule. How do they secure their technicians' workstations? Is the machine running the RMM console the same one people browse the web and read email on? How fast do they patch their own tooling, and can they prove it? What happens on their side when a technician gets phished?

What works:

- Quarterly service reviews with a security section and real metrics: technician client version compliance, MFA coverage, time to fix KEV-listed bugs in their stack, the number of accounts with access to your environment, and access removals completed on time
- Evidence over attestation. Exports and reports beat a checkbox on a questionnaire every time
- A joint tabletop at least once a year where the MSP's own tooling is the way in. Who calls whom? Who can cut their access at 2 AM if they're the compromised party?
- An exit and contingency plan: break-glass admin accounts you own and they can't touch, your own copy of network documentation and credentials, a list of every account and agent they control, and a way to keep running for a couple of weeks if you have to sever the relationship overnight

That last one matters more than people think. If your MSP gets hit, the safest move might be cutting them off completely while they sort it out. Can you actually do that without shutting down your own business? If the honest answer is no, your MSP has quietly become a single point of failure with your logo on it.

Takeaways

For business leaders: treat your MSP relationship like the privileged access it is. Put someone on your side in charge of overseeing it with the authority to ask hard questions, and budget for the reviews and the tabletop. How about asking your MSP this week what ScreenConnect or RMM client version every technician touching your environment is running, and how fast they got there?

For IT and security managers: you can start on the inventory and the alerting for new remote-access agents without anyone's permission. Then bring the contract gaps to leadership with this week's timeline as the example. A 9.9 sat on the KEV list for two months while attackers sprayed hundreds of thousands of requests at MSP servers waiting for one technician to join. That's the case for oversight, written for you.

If your team wants hands-on reps hunting rogue remote-access tools and reading process trees, the DFIR CTF Board at https://ctf.securityvolition.com keeps a running list of DFIR and blue-team CTFs.

Sources:
Huntress, CVE-2026-84869 exploited against MSP technicians (Nov 16, 2026): https://www.huntress.com/blog/cve-2026-84869-screenconnect-technician-exploitation
Huntress, crypto-named unauthorized ScreenConnect sessions (Nov 13, 2026): https://www.huntress.com/blog/fake-crypto-exchange-sessions-screenconnect
Rapid7, CVE-2026-90436 exploitation against Confluence Data Center and N-able N-central (Nov 16, 2026): https://www.rapid7.com/blog/post/etr-rapid7-detected-exploitation-of-cve-2026-90436-against-atlassian-confluence-data-center-n-able-n-central-also-exploited

#ThirdPartyRisk #MSP #IncidentResponse #DFIR #RemoteAccess
