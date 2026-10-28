# Cloudflare Puts $3,000 on the Table to Let Hackers Break Its Own Firewall

*A report on Cloudflare's PHP Capture the Flag challenge*
*Reporting date: 28 October 2026*

---

## The short version

On 27 October 2026, Cloudflare launched a public **Capture the Flag (CTF)** challenge that pays security researchers **$3,000 per novel bypass** to defeat its own Web Application Firewall (WAF). The target is a deliberately vulnerable PHP server; the goal is to punch an exploit through Cloudflare's live defenses and steal a flag. It is running right now, administered through Cloudflare's HackerOne bug bounty program.

The story is not the game. It is what the game admits: a major security vendor is paying outsiders to publicly disprove its own marketing, and it is doing so because AI has made that marketing impossible to take on faith.

---

## What Cloudflare built

The challenge centers on **"php2shell,"** a disclosure of two serious PHP vulnerabilities — **CVE-2026-109618** and **CVE-2026-109619** — that surfaced the week of 20 October 2026. In response, Cloudflare shipped WAF rules to block exploitation and rolled out a new feature it calls **"full transaction detection,"** now available on every plan including Free.

Rather than simply asserting those defenses work, Cloudflare stood up a live test:

- **Target:** `https://php-rce.cloudflare.app/`
- **Stack:** stock **PHP 8.5.11**, Apache 2 with mod_php, an empty `index.php`, running inside a **Cloudflare Container**.
- **Defense:** Cloudflare's default WAF protection rules for the two CVEs sit in front of the box.
- **Objective:** get remote code execution past the WAF and read a file at `/flag`.
- **Reset:** the sandbox and its flag reset every few minutes, so a winning exploit must be verified fast.

The PHP vulnerabilities are intentional and already public. The challenge is explicitly **not** about finding a PHP bug — it is about whether Cloudflare's firewall can stop a known exploit from landing.

## The money and the rules

Cloudflare's HackerOne program policy states the terms plainly:

> **Bounty: $3,000 USD per eligible, materially unique report.**

The important nuances for anyone reporting on this:

- **$3,000 is per unique technique, not a single grand prize.** New flags, resets, attempt IDs, or "equivalent encodings" count as duplicates and earn nothing. Only the *first* complete, reproducible report for a given bypass is paid.
- **The bar is full RCE, not partial progress.** File disclosure without code execution, RCE without recovering and verifying the flag, local-only exploits, and rediscovering the PHP bugs are all explicitly ineligible.
- **Scope is deliberately narrow:** authorized testing is `POST` requests to `https://php-rce.cloudflare.app/php/*` only. Attacking the verification UI, the origin, other hostnames, or other participants is prohibited, as is any denial-of-service or high-volume scanning.

**Timeline:** testing opened **27 October 2026 at 13:30 UTC**. No end date has been announced — Cloudflare says it will call the campaign closed when it is ready, and the HackerOne page is the authoritative source for whether it remains live. As of this report, it is **open and accepting submissions.**

## Why it matters

Strip away the gamification and this is a notable shift in posture.

**1. "Prove it" replaces "trust us."** The conventional security pitch is an assertion: *our WAF blocks this class of attack.* Cloudflare inverted it — publishing the target, the stack, and a cash incentive to make the assertion fail in public. Every successful bypass feeds directly back into the rules. It is crowdsourced red-teaming wired into a product-QA loop.

**2. The AI arms race is the stated motive.** Cloudflare's own framing is blunt: *"The widespread adoption of AI allows attackers to discover new vectors with unprecedented speed and efficiency."* The CTF is the company's answer to a problem it is conceding it cannot outrun alone — if attackers can now iterate on exploits at machine speed, defenders need adversarial pressure at the same tempo, so Cloudflare is buying it.

**3. Unusual candor about limits.** The program is refreshingly honest for vendor-adjacent material. It repeats that a WAF is a stopgap, not a fix: *"WAF protections are not a substitute for patching PHP; response blocking cannot undo origin-side execution."* The message to operators is explicit — patch your software; the firewall buys you time, not immunity.

**4. It is also, plainly, good marketing.** The critical read belongs in any fair report: Cloudflare turned a week of ugly news — a PHP exploit disclosure and a separate compromise of eight eMudhra intermediate CA certificates — into a confidence display, and gets free penetration testing of a new feature in the bargain. Both things can be true at once.

## The revealing contrast

Cloudflare's sanctioned challenge did not happen in isolation. The same php2shell disclosure spawned an **independent, unofficial** challenge — dubbed "Steal My Bitcoin" — in which an organizer put roughly **$2,000 in Bitcoin** on an *unpatched* PHP server and dared anyone to drain the wallet.

It fell fast. A UK researcher operating as "killa" / "KillaBoi" **cracked it in about 19 hours**, claiming 0.0251 BTC (~$2,021 at the time).

That contrast is the sharpest angle in the whole story:

| | Cloudflare PHP CTF | "Steal My Bitcoin" (independent) |
|---|---|---|
| Server | PHP 8.5.11 **behind a WAF** | Unpatched PHP **with no WAF** |
| Reward | $3,000 per unique bypass | ~$2,000 in BTC |
| Status | Live, open-ended | Solved in ~19 hours |
| Tests | Whether the firewall holds | Whether the raw bug falls |

The unofficial challenge proved the underlying PHP bug is trivially exploitable once nothing stands in the way. Cloudflare's challenge asks the follow-on question that actually sells firewalls: *does the WAF change that outcome, and for how long?* The honest answer is what the company is paying to discover in public.

## For the record

- **Primary source:** Cloudflare's HackerOne program policy, `hackerone.com/cloudflare` (status confirmed open; bounty figure of $3,000 stated verbatim).
- **Announcement:** Cloudflare blog, "Introducing the Cloudflare PHP Capture the Flag challenge," `blog.cloudflare.com/php-ctf/`, published 27 Oct 2026.
- **Vulnerabilities:** CVE-2026-109618, CVE-2026-109619.
- **Target:** `https://php-rce.cloudflare.app/`.
- **Related reporting on the independent challenge:** Tom's Hardware, GIGAZINE, coin-turk (the ~$2,000 Bitcoin CTF solved in ~19 hours by "killa").

## A note on method

This report is built entirely from Cloudflare's public announcement, its live HackerOne program policy, and public reporting on the parallel challenge. It does not include, and this author did not develop, any working exploit or WAF-bypass payload. Participating in the challenge — building the actual RCE chain to claim the bounty — is authorized only through a researcher's own HackerOne account under the program's terms.
