---
name: automated-email-campaigns
description: Design, structure, and deploy automated lifecycle email sequences (onboarding, activation, retention, win-back). Use when setting up email triggers, cohort segmentations, messaging workflows, SPF/DKIM/DMARC authentication, or ESP automation flows.
version: 1.0.1
---

# Automated Email Campaigns

This skill governs how the agent designs, writes, and technically deploys **lifecycle email automation** — event-triggered and stage-based sequences (onboarding, activation, retention, win-back) that carry a subscriber from "just signed up" to "engaged, retained, and reactivated when they lapse." Treat a lifecycle email program as a **behavioral state machine, not a newsletter**: each subscriber sits in exactly one lifecycle stage, transitions are driven by what they *do* (or fail to do), and every email exists to move them to the next state. A blast to everyone is the failure mode this skill exists to replace.

The two non-negotiable foundations are **deliverability** (an email that lands in spam converts at zero, no matter how good the copy) and **relevance** (the right message to the right behavior at the right time), and they are scored before any cleverness.

> **Prime Directive:** *Trigger on behavior, segment by lifecycle stage, and earn the inbox before you earn the click. An automated email is a promise made at signup; every send must be relevant enough that the subscriber is glad it arrived, or it costs you the one asset you can't buy back — sender reputation. Authenticate first (SPF, DKIM, DMARC), personalize on behavior not just first name, design mobile-first, and measure conversions, not opens — because in a post-Apple-MPP world the open rate is a lie. If you can't say which subscriber action a send responds to and which next action it drives, don't send it.*

---

## When to Use

### Sequence-design triggers (building a flow)

- "Design an onboarding sequence / welcome series for [product]."
- "Set up a win-back / re-engagement / churn-prevention flow."
- "Build a retention / activation / nurture email sequence."
- "Create a lifecycle email program / drip campaign / automation flow."
- "Map out the emails a new user should get in their first 30 days."
- "We have signups but no activation — build the email flow to fix it."

### Trigger & segmentation triggers (wiring behavior to messaging)

- "Trigger an email when a user [does X / abandons Y / goes inactive for N days]."
- "Set up cohort / behavioral / lifecycle-stage segmentation for email."
- "Define the entry and exit conditions for this automation."
- "Segment lapsed users for a win-back campaign."

### Copy triggers (writing the emails)

- "Write the subject lines / body / CTA for this onboarding flow."
- "Write a win-back email that gets people to come back."
- "Improve / rewrite this lifecycle email for engagement."

### Deliverability & technical triggers (making it land and work)

- "Set up SPF / DKIM / DMARC / BIMI for our sending domain."
- "Our emails are going to spam — fix deliverability."
- "Why is our onboarding email landing in Promotions / spam?"
- "Set up the ESP automation (Klaviyo / Customer.io / HubSpot / Braze / Iterable)."

### Anti-triggers — do NOT use this skill when

- **One-off broadcast / newsletter / announcement blast** to the whole list with no behavioral trigger and no sequence logic (product launch email, weekly digest, holiday sale blast). That is *campaign* email, not *lifecycle* automation — different segmentation (send-time, not stage-based), different success metrics, no state machine. (Caveat: the *deliverability* principles here apply to broadcasts too — borrow the Deliverability section if asked, but the sequencing workflow does not.)
- **Transactional email** — password resets, receipts, OTP codes, shipping confirmations, security alerts. These are *system* emails triggered by a single action, must send regardless of marketing consent, route through a separate transactional stream/IP (so a marketing reputation problem never blocks a password reset), and are exempt from marketing-unsubscribe requirements. If asked to "send a receipt," that is transactional infrastructure, not lifecycle marketing — flag the distinction and route accordingly. (Caveat: a transactional email *with* a soft marketing cross-sell block is a gray zone — keep the transactional content primary and the marketing secondary, and respect that legal treats it as transactional.)
- **SMS / push / in-app messaging only** with no email component. Those are adjacent lifecycle channels with their own deliverability and consent regimes (TCPA/short-code compliance for SMS, OS-level opt-in for push). The *orchestration logic* (lifecycle stages, triggers) transfers; the channel mechanics do not. If the request is genuinely multichannel, say so and treat email as one channel in the orchestration.
- **The underlying product analytics / event instrumentation** that *emits* the behavioral events the triggers fire on (defining `Project Created`, wiring Segment/Mixpanel). That is upstream telemetry work — this skill *consumes* those events as triggers; it does not instrument them. If the events don't exist yet, flag that the event layer is a prerequisite and route to a telemetry/instrumentation skill.
- **Pure copywriting / brand voice with no lifecycle or automation context** (a landing page, ad copy, a value proposition). Route to a conversion-copywriting skill. (Caveat: email *subject lines and body copy* are in scope here, written to the lifecycle context — but a standalone copy task without sequence/trigger logic is not.)
- **Deep CAN-SPAM / GDPR / CASL legal interpretation** — whether a specific consent flow is lawful in a specific jurisdiction. This skill *applies* the well-established baseline rules (visible unsubscribe, honor opt-outs promptly, sender identity, consent before sending) as engineering defaults, but it is not a substitute for legal counsel on edge cases. Flag genuinely novel legal questions for a lawyer rather than inventing a ruling.

When a request straddles a boundary (e.g., "instrument the events *and* build the win-back flow"), instrument under the telemetry skill, then build the sequence here on top of those events — and state the handoff rather than silently assuming the event layer exists.

---

## Prerequisites

### Ground truth to resolve before any design (Phase 0)

Resolve these before building. If unknown, ask the user *once*, in a batch:

1. **The product and the "aha" milestone:** What does the product do, and what is the single **activation event** that correlates with retention (the "magic moment" — first project created, first message sent, first report generated)? Onboarding's entire job is to reach it.
2. **The ESP / automation platform:** Klaviyo, Customer.io, HubSpot, Braze, Iterable, Mailchimp, ActiveCampaign, or other? This determines the mechanics of triggers, segments, and dynamic blocks.
3. **Available behavioral data:** What user events and properties are piped into the ESP (signup, activation, feature usage, last-active, plan, purchases)? This caps how behavioral the personalization and triggers can be. If the event layer is thin, flag it as the limiting constraint.
4. **The sending domain and current auth state:** What domain sends mail, and are SPF/DKIM/DMARC already configured? Use a dedicated subdomain like `mail.yourapp.com` for marketing, isolated from the root domain's transactional/corporate mail reputation.
5. **The business goal and the conversion definition:** What does each flow drive toward — activation, paid conversion, retention, reactivation, revenue — and how is "success" measured downstream (not opens)?
6. **Consent model and existing list state:** How is consent collected (single/double opt-in)? Is there list-fatigue or deliverability history? Any existing suppression/preference infrastructure?
7. **Frequency tolerance and existing sends:** What other emails does this audience already get (so the new flows don't stack into over-mailing)?

### Reference files

Load these from the skill directory when the task calls for them:

- `references/deliverability-checklist.md` — Load when the user asks about SPF/DKIM/DMARC setup, spam issues, or inbox placement. Contains DNS record templates and verification commands.
- `references/lifecycle-stage-definitions.md` — Load when mapping a product's lifecycle stages or defining entry/exit conditions. Contains the stage-state-machine template.
- `references/esp-platform-guide.md` — Load when configuring triggers, segments, or dynamic content blocks in a specific ESP (Klaviyo, Customer.io, HubSpot, Braze, Iterable). Contains platform-specific mechanics.
- `references/email-copy-frameworks.md` — Load when writing subject lines, body copy, or CTAs for lifecycle emails. Contains the per-stage copy templates.
- `references/rendering-qa-checklist.md` — Load before deploying any email. Contains the cross-client rendering test matrix (Gmail, Apple Mail, Outlook).
- `scripts/` — Contains helper scripts for DNS record generation, seed-list inbox placement testing, and suppression-list validation. Reference by name when the task requires them.

---

## Procedure

Follow the phases in order. Do not write a single subject line (Phase 3) before the lifecycle map and triggers (Phases 1–2) exist, and do not deploy (Phase 5) before authentication and rendering are verified (Phase 4) — beautiful copy sent from an unauthenticated domain lands in spam, and a flow with no exit conditions over-mails from day one.

### Phase 1 — Map the lifecycle and the flows (the state machine)

1. **Place the audience on the lifecycle stages.** Define, for *this* product, the concrete boundary of each stage — what data condition puts a subscriber in "onboarding" vs. "activated" vs. "at-risk" vs. "dormant" (e.g., dormant = "no login in 30 days").

   | Stage | Definition | Goal of email here | Primary flow |
   |---|---|---|---|
   | **Lead / Subscriber** | Opted in, not yet a user/customer | Convert to first use/purchase | Welcome + nurture |
   | **New / Onboarding** | Signed up, not yet activated | Reach the activation milestone | Onboarding series |
   | **Activated** | Hit the "aha" / core-value milestone | Build the habit; deepen usage | Engagement / education |
   | **Engaged / Retained** | Using regularly, deriving value | Retain, expand, advocate | Retention, upsell, referral |
   | **At-risk / Declining** | Usage dropping vs. their own baseline | Re-engage before they churn | Churn-prevention |
   | **Dormant / Lapsed** | No meaningful activity for N days | Reactivate or sunset | Win-back |
   | **Churned / Unsubscribed** | Cancelled or opted out | Respect the exit; (maybe) win back later | Sunset / suppression |

2. **Choose the flows to build and their priority.** The core lifecycle set:
   - **Welcome / Onboarding** (Lead/New → Activated): highest-ROI flow; drives the activation milestone.
   - **Activation nudge** (signed up but not activated): behavior-absence triggered.
   - **Engagement / Education** (Activated → Retained): build the habit, surface unused value.
   - **Retention / Churn-prevention** (Retained → At-risk): triggered by declining usage.
   - **Win-back / Re-engagement** (Dormant): triggered by an inactivity threshold.
   - **Sunset / Suppression** (failed win-back): the graceful exit + list hygiene.

3. **Define entry AND exit conditions for every flow.** The exit condition is the moment the subscriber's goal-action makes the rest of the sequence wrong — they leave it immediately. (Someone in the "create your first project" nudge sequence who *creates a project* must immediately exit; continuing to nag them to do what they just did is the most common, most credibility-destroying lifecycle bug.)

4. **Configure global frequency capping** — a maximum number of automated emails per subscriber per day/week across *all* flows — and **mutual-exclusion suppression** (a subscriber in the win-back flow should not simultaneously receive onboarding nudges). Without these, parallel automations stack and a single user gets five emails in a morning.

### Phase 2 — Wire triggers and segmentation (behavior → message)

1. **Make every flow event-driven with time as the fallback.** Fire on the event (`activated`, `feature_used`, `cart_abandoned`); when the desired event *doesn't* happen, use a time-delay as the trigger for the nudge ("no project created within 24h of signup → send the activation nudge"). Time is the trigger for *absence* of behavior; behavior is the trigger for everything else. Every well-built flow is a tree of "did they do X? → branch," not a straight line of timed sends.

2. **Never trigger off "opened email."** Apple Mail Privacy Protection (MPP) pre-fetches images and inflates opens to meaninglessness. Trigger off **clicks** and **on-site/in-product behavior** instead.

3. **Build segments as live behavior-based queries** (auto-updating), not static lists. A segment like "signed up > 7 days ago AND no activation event AND not in win-back flow" is a live query, not a one-time snapshot.

4. **Wire dynamic content blocks** — conditional sections that render differently per segment within a single email template (an enterprise user and a free user get different CTAs in the same send) — fed by behavioral data piped from the product/warehouse into the ESP.

5. **If the event layer doesn't exist yet**, flag the missing event instrumentation as the **limiting prerequisite**, build the best time-based version possible in the interim, and route the instrumentation work upstream (telemetry/event-instrumentation skill) — then upgrade the flows to event-driven once the events flow.

### Phase 3 — Write the copy (behavioral, mobile-first)

1. **Every email has exactly one primary CTA.** Not two, not "choose your own adventure" — one. The CTA drives the next action in the lifecycle transition.

2. **Subject lines: ≤40 characters** (survive mobile truncation), behavioral hook, not a generic greeting. Write the **preheader text** deliberately as an extension of the subject — never leave it to auto-fill from the body's first line.

3. **Personalize on behavior, not `{{first_name}}`.** Reference what the subscriber did: the feature they tried, the item they left in the cart, the milestone they're one step from, the content category they engage with, days since last login, their plan tier. The test: *could this exact email have been sent to any subscriber?* If yes, it isn't personalized — it's a blast with a name on it.

4. **Every merge variable has a fallback default** and never renders raw. `{{first_name|fallback:"there"}}` — never `{{first_name}}` alone, which renders as literal `{{first_name}}` or "Hi ," when the field is empty.

5. **Mobile-first design:**
   - Single-column layout
   - Body font ≥14–16px, headlines ≥22px
   - Tappable CTAs ≥44×44px with generous padding
   - Bulletproof (HTML/CSS) button, not an image button — renders and is tappable everywhere
   - `alt` text on all images
   - Email must make sense and CTA must be reachable **with images disabled**
   - Legible in **dark mode** (test both light and dark)

6. **Footer:** sender identity + physical mailing address + visible unsubscribe link (CAN-SPAM). One-click unsubscribe headers (`List-Unsubscribe` + `List-Unsubscribe-Post`, RFC 8058) — mandatory for bulk senders.

### Phase 4 — Authenticate and verify deliverability (the hard gate)

This phase is a **hard prerequisite**, not an optimization. Following Google and Yahoo's bulk-sender requirements (in force since 2024), a program that isn't authenticated doesn't have a copywriting problem; it has a "we're not in the inbox" problem, and that is solved first.

1. **Publish SPF (Sender Policy Framework):** a DNS TXT record listing the servers authorized to send for your domain. Must `pass` and align. Only one SPF record per domain (multiple SPF records cause lookup failure).

2. **Publish DKIM (DomainKeys Identified Mail):** a cryptographic signature proving the message wasn't altered and came from your domain. Must be present and `pass`.

3. **Publish DMARC:** a DNS TXT policy (`p=none` → `quarantine` → `reject`) that tells receivers what to do when SPF/DKIM fail, and sends you reports. A published DMARC record is **required** for bulk senders. Start at `p=none` to monitor, then escalate to `p=quarantine`/`p=reject` once aligned.

4. **Enable one-click unsubscribe (RFC 8058):** the `List-Unsubscribe` and `List-Unsubscribe-Post` headers enabling one-click opt-out directly from Gmail/Yahoo. The opt-out must be honored within 2 days.

5. **Verify authentication on a live test send** — not merely "added to DNS." Run:

   ```powershell
   # Check SPF (Windows PowerShell)
   Resolve-DnsName -Name yourapp.com -Type TXT | Where-Object { $_.Strings -match "v=spf1" }

   # Check DMARC
   Resolve-DnsName -Name "_dmarc.yourapp.com" -Type TXT

   # Check DKIM (replace selector)
   Resolve-DnsName -Name "selector1._domainkey.yourapp.com" -Type TXT
   ```

   Or use an external verifier: send a test email to `mail-tester.com` or use `mxtoolbox.com/SuperTool` and confirm SPF=pass, DKIM=pass, DMARC=pass with alignment.

6. **Send from a dedicated subdomain** (e.g., `mail.yourapp.com`) for marketing, isolated from the root domain's transactional/corporate mail reputation. Transactional mail (password resets, receipts) must route through a separate stream/IP/subdomain so a marketing reputation problem never blocks a password reset.

7. **Warm up a new domain/IP gradually** — start with a small engaged segment and ramp volume over days/weeks. Never cold-blast from a new IP.

8. **Seed-test inbox placement** (Gmail/Yahoo/Outlook/Apple) and confirm in-inbox before the live send. Use a seed-list tool or manual test accounts across providers.

9. **Monitor spam-complaint rate** — Google's threshold is <0.3%; aim for <0.1%. Above it, delivery degrades for *all* your mail.

10. **(Post-enforcement) Pursue BIMI** with DMARC at `p=quarantine` or `p=reject` + VMC/CMC, where brand trust warrants it. BIMI displays your verified logo next to your emails in supporting clients (Gmail, Apple Mail, Yahoo). It requires DMARC at enforcement as a prerequisite.

### Phase 5 — Deploy, measure, and maintain

1. **Deploy the flows in the ESP** with the entry/exit conditions, frequency caps, and suppression rules configured in Phase 1.

2. **QA end-to-end in test mode:** enter the flow as a test subscriber, verify each email fires in the correct order, verify the exit condition removes you when you take the goal action, verify suppression prevents overlap with other flows.

3. **Measure success on downstream conversion** — activation rate, click-to-conversion, revenue per recipient, retention lift, reactivation rate — **not open rate**. Open rate is, at best, a directional, noisy signal (useful only for non-Apple segments or gross anomaly detection), never as the KPI.

4. **Run A/B tests** that change **one variable** at a time, test the highest-leverage element first (subject line, then CTA, then send time), and judge on conversion, not opens.

5. **Operate list hygiene on a cadence:**
   - Hard-bounced and role/spam-trap addresses removed immediately
   - Disengaged subscribers progressively down-throttled
   - Win-back campaign run for the declining/dormant segment
   - Non-responders **sunset/suppressed** — never mail a dead address forever
   - Never buy lists or mail purchased/scraped addresses

6. **Review DMARC aggregate reports** on a regular cadence; have a rising-complaint protocol (pause sends, investigate root cause, re-warm).

---

## Pitfalls

### The cardinal sins (most common, most damaging)

| Pitfall | Consequence | Fix |
|---|---|---|
| **No exit conditions** | Subscriber who completed the goal keeps getting nagged to do it | Every flow declares exit = goal-action taken; verified end-to-end in test |
| **Triggers keyed on opens** | Apple MPP fires false opens; automations misfire | Re-key all triggers off clicks / in-product behavior, never opens |
| **Unauthenticated sending domain** | Gmail/Yahoo bulk rules throttle or spam-file everything | Publish + align SPF/DKIM/DMARC before any send; verify with a live test |
| **Parallel flows stacking** | One subscriber gets 5 emails in a morning → unsubscribes/complaints | Global frequency caps + mutual-exclusion suppression across all flows |
| **Time-driven drip divorced from behavior** | "Create your first project" sent to someone who created three | Convert to event-driven with behavior branches; time triggers only the *absence* of an action |
| **Transactional mixed with marketing** | Password reset delayed by a marketing reputation issue | Separate streams/IPs/subdomains for transactional vs. marketing; keep transactional content primary in any hybrid |
| **Sending to opted-out users** | Legal exposure; trust breach | Treat the suppression list as authoritative; honor opt-outs within 2 days; QA that unsubscribe + one-click headers actually suppress |

### The Apple MPP open-rate distortion (the defining 2026 measurement trap)

Apple Mail Privacy Protection pre-fetches and caches all images, registering an "open" regardless of human action — and a large share of most lists is Apple Mail. **Consequence:** open rate is inflated and unreliable, and any automation or A/B decision keyed on opens is operating on noise. **Protocol:** (1) re-key all triggers and branches off **clicks and in-product behavior**, never opens; (2) judge every flow and test on **downstream conversion** (activation, revenue per recipient, reactivation rate); (3) if you must report opens, segment out Apple Mail and label the rest as directional only; (4) for deliverability signals, use complaint rate, bounce rate, and engagement, not opens.

### Re-permission and the inherited/stale list

Taking over a list with unknown consent provenance or a long-dormant list is a deliverability landmine — mailing it cold tanks reputation. **Protocol:** start with the *recently engaged* segment only, run a **re-permission / re-engagement** campaign to the rest in small warmed batches, and **suppress anyone who doesn't re-engage**. Never assume consent transfers; never blast a stale list to "wake it up."

### Double vs. single opt-in tradeoff

Double opt-in (confirm via a click in a confirmation email) yields a smaller but **cleaner, higher-engaging, better-delivering** list and is effectively expected in much of the EU; single opt-in grows the list faster but admits typos, spam traps, and low-intent addresses. **Protocol:** default to double opt-in where deliverability is fragile, where GDPR-style consent rigor applies, or where list quality matters more than raw size; use single opt-in only with strong downstream hygiene and bot/typo protection on the form.

### Incentive-conditioning in win-back

Always discounting to win people back **trains subscribers to lapse on purpose** to trigger the coupon. **Protocol:** lead win-back with *value and what's new* before any incentive; reserve discounts for the later steps and for segments where the reactivation economics justify it; vary the offer so it isn't a predictable lapse-reward.

### Global send-time and time zones

A flow that sends "at 9am" on the server clock hits subscribers at 3am elsewhere. **Protocol:** send on the **subscriber's local time zone**, use the ESP's **send-time optimization** where available, and respect **quiet hours**; for time-triggered absence nudges, anchor the delay to the subscriber's event time, not a global clock.

### When the event layer doesn't exist yet

Behavioral triggers require the product to *emit* the events (activation, feature usage, last-active). If those events aren't instrumented, the flows degrade to crude time-based drips. **Protocol:** flag the missing event instrumentation as the **limiting prerequisite**, build the best time-based version possible in the interim, and route the instrumentation work upstream (telemetry/event-instrumentation skill) — then upgrade the flows to event-driven once the events flow.

### Merge variable rendering failures

`{{first_name}}` with no fallback renders as literal `{{first_name}}` or "Hi ," when the field is empty — instantly signals a blast, not a personal email. **Protocol:** every merge variable has a fallback default (`{{first_name|fallback:"there"}}`); QA with a test send where all profile fields are empty.

### Image-only CTAs

Putting the only CTA inside an image means it's invisible and untappable when images are blocked (default in many clients). **Protocol:** always use a bulletproof HTML/CSS button; never an image button; the message and CTA must survive blocked images.

---

## Verification

The skill's output is successful only when **every applicable** box is satisfied. Treat any unchecked item as a blocking defect.

### Lifecycle architecture

- [ ] Every subscriber maps to exactly **one lifecycle stage**, and each flow's stage transition is explicit (entry → goal → exit).
- [ ] The product's **activation milestone** ("aha" moment) is identified, and the onboarding flow is built to drive toward it.
- [ ] Every automation has **explicit entry AND exit conditions**; taking the goal-action **removes** the subscriber from the flow (verified end-to-end in test).
- [ ] **Global frequency caps** and **mutual-exclusion suppression** are configured so parallel flows cannot over-mail a subscriber.

### Triggers & segmentation

- [ ] Flows are **event-driven** (behavior triggers), with **time used only as the trigger for the *absence* of an action** — not a fixed calendar drip divorced from behavior.
- [ ] **No automation triggers off "opened email"** (Apple MPP makes opens unreliable); triggers key off clicks / in-product events / time-since event.
- [ ] Segments are **live behavior-based queries** (auto-updating), and dynamic content blocks render correctly per segment.

### Copy & design

- [ ] Every email has **one primary CTA**, a mobile-safe subject (~≤40 chars), a deliberately written **preheader**, and a behavioral hook (not a generic greeting).
- [ ] Personalization is **behavioral**, not just `{{first_name}}`; every merge variable has a **fallback default** and never renders raw.
- [ ] Design is **mobile-first single-column**, legible in **dark mode** and **with images disabled**, with a **bulletproof (text/HTML) CTA button** and `alt` text — the message and CTA survive blocked images.
- [ ] Verified rendering across **Gmail, Apple Mail, and Outlook** (the Word-engine breaker).

### Deliverability & compliance (the hard gate)

- [ ] **SPF, DKIM, and DMARC** are published, **pass, and align** on a live test send (not merely "added to DNS"); a single SPF record only.
- [ ] **One-click unsubscribe** (`List-Unsubscribe` + `List-Unsubscribe-Post`, RFC 8058) is present and actually opts the user out; opt-outs honored within 2 days.
- [ ] Marketing mail sends from a **dedicated subdomain** isolated from transactional reputation; a new domain/IP is **warmed up** gradually.
- [ ] **Inbox placement seed-tested** (Gmail/Yahoo/Outlook/Apple) and confirmed in-inbox before the live send.
- [ ] Footer carries **sender identity + physical mailing address + visible unsubscribe**; consent provenance is sound; opted-out/suppressed users cannot receive sends.
- [ ] (Post-enforcement) **BIMI** pursued with DMARC at `quarantine`/`reject` + VMC/CMC, where brand trust warrants it.

### Measurement & hygiene

- [ ] Success is measured on **downstream conversion** (activation, click-to-conversion, revenue per recipient, reactivation) — **not open rate**.
- [ ] **Spam-complaint rate (<0.3%, target <0.1%)** and **bounce rate** are monitored; DMARC aggregate reports reviewed; a rising-complaint protocol exists.
- [ ] **List hygiene** is operational: hard bounces removed, disengaged down-throttled, win-back run, non-responders **sunset/suppressed**.
- [ ] A/B tests change **one variable**, test the highest-leverage element first, and are judged on conversion, not opens.

### Final report

When all gates pass, report:
- The lifecycle stage map and the flows built (with entry/exit conditions)
- The trigger logic per flow
- The email copy with its segmentation and dynamic blocks
- The authentication status (SPF/DKIM/DMARC/one-click-unsub verified passing)
- The rendering/inbox-placement results
- The measurement plan keyed to downstream conversion with its list-hygiene cadence

If any gate cannot be met — most often unauthenticated sending domain, missing exit conditions, triggers keyed on opens, or a thin event layer forcing time-based drips — state which, why, and the remediation (e.g., "DMARC not yet published → mail will be throttled/spam-filed by Gmail/Yahoo bulk rules → publish and align SPF/DKIM/DMARC before launch").

**Never ship a lifecycle program that sends from an unauthenticated domain, lacks exit conditions, over-mails through un-capped parallel flows, or optimizes on open rate — in 2026 you earn the inbox with authentication and the click with behavioral relevance, and the open rate is a number that lies.**
