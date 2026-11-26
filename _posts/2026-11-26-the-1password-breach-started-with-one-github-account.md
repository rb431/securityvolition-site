---
layout: post
title: The 1Password Breach Started With One GitHub Account
date: 2026-11-26T07:51:00-05:00
tags: [incident-response, 1password, github, developer-security, supply-chain]
linkedin_url: https://www.linkedin.com/pulse/1password-breach-started-one-github-account-richard-brackett-uurxe/
source: https://1password.com/blog/update-on-nov-2026-security-incident
type: article
---

On November 25, 1Password told customers that an attacker had been inside its backend systems. The line most people will remember is the reassuring one: vaults were not accessed. I think that claim is likely true, and I'll explain why below. The rest of the disclosure is where the useful lessons live, though, and several of them apply whether or not you ever paid 1Password a dime.

This is for the people who have to act on a vendor incident: IT leads, security folks at smaller shops, and the managers who get the "are we exposed?" question. Everything below comes from 1Password's own post and reporting from November 25 and 26. I'll call out the known unknowns.

What 1Password says happened

According to 1Password's incident post ([1Password](https://1password.com/blog/update-on-nov-2026-security-incident)), the company began investigating on November 19 after seeing signs that an attacker had gained access to an employee's corporate GitHub account. The attacker used it to read source code and push malicious code to backend services, which then downloaded administrative data about customers. The attacker also copied source code from private repositories and got into the CI/CD system, which 1Password says holds infrastructure secrets the product needs to run.

The customer records taken from a subset of users include names and sign-in email addresses, account and user identifiers, account creation and last sign-in times, public keys, Secure Remote Password (SRP) protocol parameters, and some internal metadata. The detail I would pay attention to: the attacker also got tokens from emailed invitation, provisioning and recovery links.

On the other side of the ledger, 1Password says vault contents, the keys that protect them, account recovery keys, and its apps and browser extensions were not affected. It also says it doesn't store your account password or Secret Key.

The response, per the company: it disabled the compromised GitHub account, revoked the stolen token and removed access the attacker created. Internal credentials that might have been exposed were rotated or revoked. Then came tighter GitHub, CI/CD and endpoint controls, new detections, and independent experts brought in to validate containment. It also says it found no internal evidence of account takeovers using the exposed tokens, and that customers don't need to change anything. BleepingComputer ([BleepingComputer](https://www.bleepingcomputer.com/news/security/1password-customer-data-stolen-after-employee-github-account-breach/)) and The Hacker News ([The Hacker News](https://thehackernews.com/2026/11/1password-hacked-customer-data-and.html)) match that account.

What nobody has said yet

Here's the part the disclosure leaves open, as of this morning:

- How the attacker got into the employee's GitHub account, and what kind of token was stolen. TechCrunch reports the method is unclear ([TechCrunch](https://techcrunch.com/2026/11/25/1password-said-hackers-broke-into-its-internal-systems-but-customer-passwords-are-safe/)).
- How many customers are affected. 1Password says "a subset," and The Verge notes the count isn't given ([The Verge](https://www.theverge.com/tech/1042409/1password-data-breach-leak)).
- When the attacker first got in. November 19 is when the investigation began, which says nothing certain about when the intrusion did.
- Whether the malicious push slipped past code review or sidestepped it entirely.
- Who the attacker is, and whether any demand has been made. Reporters have asked; no answers yet.
- What the independent experts conclude. Nothing from them is published.

Until outside validation shows up, I read every "no impact" line as the company's current position. No knock on 1Password. Any vendor's first week of statements gets that treatment from me, fog of war and all.

Why "your vault is safe" is believable

By 1Password's account, it doesn't store your account password or Secret Key, and those are what unlock your vault. The Hacker News quotes the company: "Your vault secrets are encrypted with keys that only you hold, so we can't access them, and neither can an attacker who gets into our servers." An attacker running code on the backend can only take what the backend has, and it never had those keys.

The stolen SRP parameters and public keys sound alarming, and 1Password says they can't be used to get into an account. Public keys are public by design. I can't verify the SRP claim and haven't seen outside cryptographers weigh in, so file it under "plausible, vendor-stated."

The bigger worry is the delivery chain. If an attacker can push code to backends, does anything reach the apps and extensions on your laptop? 1Password says no. That's the claim I most want outside experts to confirm.

The real exposure is a verified customer list

Strip away the cryptography and a criminal now holds real people, real email addresses, a confirmed password manager relationship, and a rough idea of each person's last sign-in. BleepingComputer makes the same point, that (Tada!) the data is valuable as a target list for phishing built around the breach itself.

Think about the lure: "We detected unusual activity on your account on [accurate date]. Verify to keep it secure." The date could be right and the sender could look right. The recipient just read a news story about the company. A password manager login is about the most valuable thing a fake page can capture.

Then there are the tokens. The ones in emailed invitation and recovery links can't open an account alone. 1Password says completing the process also requires either an SSO sign-in or a six-digit code sent to the user's registered email. Six digits is a million possibilities, and the company lengthened the code to ten alphanumeric characters. My read, which is inference and not something 1Password said, is that they decided guessing the second step was a realistic enough path to close it fast. A small number of users who were mid-invitation or mid-recovery were notified directly. No notice from them is a decent sign you weren't in that group, though I wouldn't take that for granted.

What to do this week as a customer

1Password's own advice is short: be careful with unexpected messages or calls that mention 1Password, invitations or recovery, and only enter your password or Secret Key in the real app or a site you typed yourself. Here's how I'd turn that into work for a team.

In practice, that means:

- Brief your staff today. A five-line note: expect lures about 1Password invitations, recovery requests and "secure your account" warnings. Open the app directly. Never type the password or Secret Key into a link from email.
- Ask your mail admin to flag or hold inbound messages that mention 1Password invitations or recovery and come from anywhere other than the senders your tenant legitimately uses.
- Review the admin console. Look at pending invitations, recent recovery requests, newly added members, new devices and permission changes. Go back well past November 19, since that's when 1Password started investigating, which isn't necessarily when the attacker showed up.
- If you use SSO, check your identity provider's logs for odd sign-ins to the password manager app, and confirm your conditional access rules and session lifetimes are what you think they are. If you use SCIM provisioning, look hard at anything that was in flight.
- Confirm two-step verification is on for every account, and use passkeys where your setup supports them. Keep the Secret Key and any emergency kit stored offline, away from the vault itself.

What should you skip? A company-wide password reset. 1Password says nothing needs to change, and a mass reset trains your people to click "reset your password" emails, which is exactly what attackers want. Reset when someone has entered credentials into something suspicious, and treat that as an account compromise: tell your admin, contact 1Password support, and review that account's activity. However, this would be a great time to review your playbook for emergency credential rotation. *Could you have rotated credentials quickly?* Particularly for service accounts or other automated process credentials?

The employee, the GitHub account, and what we actually know

Plenty of people are guessing how this account fell, so here's what's confirmed and what isn't.

Confirmed by 1Password: it was an employee's corporate GitHub account, so a work identity. It had enough reach to read private repositories and push code to backend services. A token was involved, because the company says it revoked "the stolen token" and removed "the access the attacker created." It also says it tightened GitHub, CI/CD and endpoint controls afterward.

Not confirmed: that the employee was a software developer by title (the access points to engineering-level permissions, but that's my guess), what kind of token was stolen, how it was stolen, and what the attacker-created access looked like. Nothing published says the cause was a secret committed to a repository, a malicious dependency, a poisoned workflow, a phished login or an infostealer on a laptop. Any of those can take down a developer account. None is confirmed today, and SiliconANGLE ([SiliconANGLE](https://siliconangle.com/2026/11/25/attacker-used-1password-employees-github-account-to-copy-customer-records/)) and The Next Web ([The Next Web](https://thenextweb.com/news/1password-breach-github-account-customer-data)) both report the method as unexplained. Whether the malicious code went in by direct push or through a workflow is also unstated.

What matters is the shape, because it's familiar. One developer identity often carries write access across many repositories, and GitHub's own Actions guidance notes that anyone with write access to a repository can read every secret configured in it ([GitHub Docs](https://docs.github.com/en/actions/reference/security/secure-use)). One stolen token can become source code, build secrets and a route to production. That matches what 1Password describes: source copied, CI/CD reached, backend code pushed.

Here's what I would put in front of a development team without delay. Each item maps to a common way developer accounts get taken, and most are settings you already own.

What works:

- Passkeys or hardware keys for sign-in, with SSO enforced for organization access. Passkeys resist phishing ([GitHub Docs](https://docs.github.com/en/authentication/authenticating-with-a-passkey/about-passkeys)), and SSO puts your identity provider's conditional access and session rules in front of the org ([GitHub Docs](https://docs.github.com/en/enterprise-cloud@latest/authentication/authenticating-with-saml-single-sign-on/about-authentication-with-saml-single-sign-on)).
- Separate work and personal GitHub identities. Work repos belong to accounts your company controls, such as managed users ([GitHub Docs](https://docs.github.com/en/enterprise-cloud@latest/admin/managing-iam/understanding-iam-for-enterprises/about-enterprise-managed-users)), so a hobby account with a reused password never holds the keys to production.
- Fine-grained, short-lived tokens. Retire broad, never-expiring personal access tokens, require expirations, and put an approval step in front of organization access ([GitHub Docs](https://docs.github.com/en/organizations/managing-programmatic-access-to-your-organization/setting-a-personal-access-token-policy-for-your-organization)). Keep an inventory so revocation takes minutes.
- Secret scanning with push protection on every repository, private ones included. Push protection blocks the push before the secret lands in history, and bypasses create alerts and audit log entries ([GitHub Docs](https://docs.github.com/en/code-security/secret-scanning/introduction/about-push-protection)).
- Rulesets that require review and block direct pushes to production branches, with admins and automation accounts included ([GitHub Docs](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/about-rulesets)). Add signed commits ([GitHub Docs](https://docs.github.com/en/authentication/managing-commit-signature-verification/about-commit-signature-verification)). A signature won't save you if the attacker owns the laptop and the signing key, but it makes a stolen token alone far less useful.
- GitHub Actions hardening. Pin third-party actions to a full-length commit SHA, set the default GITHUB_TOKEN to read-only and raise it per job, and be wary of self-hosted runners on repositories many people can fork ([GitHub Docs](https://docs.github.com/en/actions/reference/security/secure-use)). Swap stored cloud credentials for OIDC wherever the provider supports it ([GitHub Docs](https://docs.github.com/en/actions/concepts/security/openid-connect)).
- Audit log streaming to a place someone watches ([GitHub Docs](https://docs.github.com/en/organizations/keeping-your-organization-secure/managing-security-settings-for-your-organization/reviewing-the-audit-log-for-your-organization)). Alert on new tokens, SSH and deploy keys, app authorizations and webhooks, because "access the attacker created" is exactly what you'd go hunting for.
- Developer endpoint hygiene. Managed, patched machines with EDR, work in its own browser profile, and no long-lived tokens parked in dotfiles or shell history. 1Password says it tightened endpoint controls, though it hasn't said a device was involved, so treat this one as general guidance.

If you ever have to run this incident yourself, the first hour looks something like this. Disable the account and kill its sessions. Revoke every token, SSH key and app authorization tied to it. Then pull the audit log and look for anything the account created in the days before you noticed. Review its pushes, workflow runs and workflow file changes across all repositories, and rebuild affected services from known-good source, which is the same instinct behind 1Password restoring clean versions of the affected service. Finally, rotate every secret that account's access could reach, including the ones in CI.

That last step is where teams stall. 1Password says it rotated or revoked every credential that might have been exposed. Is your rotation playbook written down, with named owners and an inventory of what lives in CI? If the answer is "kind of," how about running a tabletop on exactly this scenario before you need it?

What to ask your vendors

Managers will get asked, "Could this happen to us through a vendor?" They need answers, not cryptography.

Done right, that's:

- A written notification commitment with a clock, and a named contact on both sides.
- A plain description of what the vendor stores about your users, including anything in emailed links or one-time tokens.
- Evidence of how the vendor limits what one compromised engineer account can reach.
- A statement of how they would prove, after an incident, that no one used a stolen token against your tenant.

1Password's disclosure is better than most here. It names the data fields, explains the token mechanics and the fix, and says what wasn't affected. Plenty of breach notices say "limited information" and stop.

TLDR;

An attacker reached customer account metadata and source code through a single employee's GitHub account, and 1Password says vaults, keys and its apps were not affected. Believe that for now, verify it as outside findings arrive, and spend your energy on the likely follow-on: phishing that uses the breach as bait. Brief your people and review your admin console. Then lock down the developer accounts and pipelines in your own house, because that's where this one started.

If you want hands-on practice working through account takeover and cloud audit logs, check out the DFIR CTF Board at https://ctf.securityvolition.com for a running list of DFIR and blue-team CTFs.

Sources:
1Password, Update on a security incident involving 1Password's backend systems (Nov 25, 2026): https://1password.com/blog/update-on-nov-2026-security-incident
BleepingComputer, 1Password customer data stolen after employee GitHub account breach (Nov 25, 2026): https://www.bleepingcomputer.com/news/security/1password-customer-data-stolen-after-employee-github-account-breach/
The Hacker News, 1Password Hacked, Customer Data and Source Code Stolen in GitHub Account Breach (Nov 26, 2026): https://thehackernews.com/2026/11/1password-hacked-customer-data-and.html
TechCrunch, 1Password said hackers broke into its internal systems, but customer passwords are safe (Nov 25, 2026): https://techcrunch.com/2026/11/25/1password-said-hackers-broke-into-its-internal-systems-but-customer-passwords-are-safe/
The Verge, 1Password breach leaked customer names and emails, but not passwords (Nov 25, 2026): https://www.theverge.com/tech/1042409/1password-data-breach-leak
SiliconANGLE, Attacker used 1Password employee's GitHub account to copy customer records (Nov 25, 2026): https://siliconangle.com/2026/11/25/attacker-used-1password-employees-github-account-to-copy-customer-records/
The Next Web, Attacker breaches 1Password systems via an employee's GitHub account: https://thenextweb.com/news/1password-breach-github-account-customer-data
GitHub Docs, Secure use reference (Actions): https://docs.github.com/en/actions/reference/security/secure-use
GitHub Docs, About push protection: https://docs.github.com/en/code-security/secret-scanning/introduction/about-push-protection
GitHub Docs, About passkeys: https://docs.github.com/en/authentication/authenticating-with-a-passkey/about-passkeys
GitHub Docs, SAML single sign-on: https://docs.github.com/en/enterprise-cloud@latest/authentication/authenticating-with-saml-single-sign-on/about-authentication-with-saml-single-sign-on
GitHub Docs, Enterprise Managed Users: https://docs.github.com/en/enterprise-cloud@latest/admin/managing-iam/understanding-iam-for-enterprises/about-enterprise-managed-users
GitHub Docs, Commit signature verification: https://docs.github.com/en/authentication/managing-commit-signature-verification/about-commit-signature-verification
GitHub Docs, About rulesets: https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/about-rulesets
GitHub Docs, OpenID Connect: https://docs.github.com/en/actions/concepts/security/openid-connect
GitHub Docs, personal access token policy: https://docs.github.com/en/organizations/managing-programmatic-access-to-your-organization/setting-a-personal-access-token-policy-for-your-organization
GitHub Docs, Reviewing the audit log for your organization: https://docs.github.com/en/organizations/keeping-your-organization-secure/managing-security-settings-for-your-organization/reviewing-the-audit-log-for-your-organization

#IncidentResponse #PasswordManagers #CyberSecurity
