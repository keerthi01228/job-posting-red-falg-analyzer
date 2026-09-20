# Pipeline Logic

PartyRock apps are built from chained prompt-driven widgets rather than traditional source code. This document captures the actual logic behind each of the 4 pipeline stages, as iteratively refined and tested throughout development.

---

## Stage 1 — 🔍 Signal Extraction

Reads the raw pasted job posting and extracts 9 structured facts, without judging them yet:
1. Fees / payment mentions
2. Salary / compensation figures
3. Contact method used
4. Urgency language present
5. Company website or LinkedIn reference
6. Interview process description
7. Offer letter or contract mention
8. Impersonation signals (does it name a real company, and is there proof?)
9. Positive legitimacy signals — counted out of 5: specific years of experience required, named tools/technologies, detailed responsibilities list, mandatory-vs-preferred skills breakdown, formal educational/certification qualifications

---

## Stage 2 — 📊 Category Scoring (full prompt)

This is the core rules engine — the most heavily iterated and tested part of the pipeline.

```
You are the second stage of a job scam detection pipeline. Using the signal extraction report below, score each of the 6 categories as TRIGGERED or CLEAR.

Signal Extraction Report:
[🔍 Signal Extraction]

---

For each category, state: TRIGGERED or CLEAR, a severity level if TRIGGERED (LOW / MEDIUM / HIGH), and a one-line reason.

---

CATEGORY 1 — MONEY RED FLAGS
TRIGGERED if the posting mentions any fees, deposits, payments, kit purchases, or training costs the candidate must pay. CLEAR if no such mention exists.

---

CATEGORY 2 — PERSONAL INFO OVERREACH
TRIGGERED if the posting requests any of the following before a formal interview has taken place: Aadhaar number, PAN number, bank account number, IFSC code, passport number, or any other sensitive government ID or financial detail; scanned copies of identity documents requested unusually early in the process; payment app screenshots or transaction proof.
CLEAR if no such request is made, OR if the only request is for name and date of birth as per Aadhaar for identity-matching purposes on an official application form (this is standard practice, not overreach) - only actual ID/account NUMBERS or document COPIES count as triggering this category, not identity-matching text fields.

---

CATEGORY 3 — URGENCY AND PRESSURE TACTICS
TRIGGERED if the posting uses language implying artificial time pressure, limited seats, or immediate joining demands in a way that discourages careful evaluation. CLEAR if urgency language is absent or is a standard reasonable phrase (e.g. 'immediate joiners preferred').

---

CATEGORY 4 — COMPANY LEGITIMACY

APPLY THESE RULES IN STRICT ORDER. The first matching rule determines the outcome. Do not skip ahead.

RULE A — IMPERSONATION CHECK (highest priority — always checked first — NEVER softened, NEVER given benefit of the doubt):
If the posting explicitly names one or more well-known, real, identifiable companies (e.g. Flipkart, Meesho, TCS, Amazon, Infosys, Google, Zomato, Swiggy, Wipro, HCL, Accenture, Deloitte, or any other specifically named real brand that is publicly recognisable as an established organisation) as the employer or hiring entity, check for verifiable proof of affiliation before triggering this rule.

WHAT COUNTS AS SUFFICIENT PROOF OF AFFILIATION (if any one of these is present, Rule A does NOT apply — move to Rule C instead):
- A domain-matching email address: an email ending in the named company's actual domain (e.g. someone@tcs.com for a posting claiming to be TCS, someone@flipkart.com for a posting claiming to be Flipkart). A domain-matching email is sufficient proof on its own. Do NOT require an additional printed clickable URL. Do NOT reason that a domain-matching email 'could be spoofed' or 'needs a URL to confirm' — this pipeline cannot verify links or spoofing, and demanding a printed URL as additional proof would incorrectly flag every genuine company posting.
- A named reference to the company's own official portal (e.g. 'TCS Next Step Portal', 'Infosys InfyTQ', 'Wipro Talent Next') — especially when combined with a domain-matching email, this further reinforces legitimacy and should not add suspicion.
- A named staffing agency with checkable registration details explicitly stated.

WHAT DOES NOT COUNT AS PROOF (if NONE of the above are present, Rule A applies):
- A generic personal email address (Gmail, Yahoo, Hotmail, Outlook free tier, or any address whose domain does not match the named company)
- No email address or portal reference of any kind
- Only a WhatsApp number, Telegram handle, or social media DM

IF Rule A applies (no proof of affiliation present):
Mark: Company Legitimacy TRIGGERED, severity HIGH
Label this explicitly as: IMPERSONATION RISK
State clearly: which named company is being claimed, and what proof of affiliation is absent.

When writing the explanation for this trigger, adapt the following clarification template by substituting the actual company name in place of the phrase "the named company":

"The named company is a real, legitimate company. The concern here is not with the named company itself — it is with this specific posting, which could not be verified as actually coming from them. This posting may be from an unaffiliated third party using their name without permission or any genuine connection. The named company likely has no knowledge of or involvement in this particular posting."

This rule applies unconditionally when proof is absent. It is NOT softened by any other rule in this section. Invoking a real, well-known company's name without any of the proof types listed above is fundamentally different from an unknown company making no claims about affiliation, and must always be treated as a strong trigger.

RULE B — UNVERIFIABLE UNKNOWN COMPANY (applies only if Rule A does not match):
If the posting does NOT name any specific well-known real company as the employer, but the company identity cannot be confirmed from the posting text (no website, no verifiable domain, no registration details), mark: Company Legitimacy TRIGGERED, severity LOW. Note explicitly that this is common for small businesses, early-stage startups, and lesser-known companies, and on its own is a weak signal that warrants only a simple verification check.

RULE C — LEGITIMATE SIGNALS PRESENT (applies only if Rule A does not apply):
If the posting references a domain-matching email address, a named official company portal, a verifiable company website, or a named and checkable staffing agency with registration details, mark: Company Legitimacy CLEAR.

---

CATEGORY 5 — VAGUE OR UNREALISTIC ROLE DESCRIPTION
Refer to the POSITIVE LEGITIMACY SIGNAL COUNT from the Signal Extraction report.
- Count 0-1: TRIGGERED, severity HIGH
- Count 2-3: TRIGGERED, severity LOW
- Count 4-5: CLEAR
Also assess the overall specificity of the role description. Generic buzzword-heavy postings with no concrete detail are a signal even if some positive markers are technically present.

---

CATEGORY 6 — UNPAID WORK AND GHOSTING RISK
Do NOT trigger this category simply because salary, compensation, or offer letter details are absent — that is standard and common in legitimate postings, especially bulk or campus hiring drives.
Only mark TRIGGERED if the posting explicitly shows one or more of these patterns:
- An explicit unpaid trial or probation period before formal pay begins
- Performance-based or commission-only pay framed as a condition before a real offer
- Real work deliverables assigned and described as an unpaid 'assignment' or 'task' during the hiring process itself
- Language suggesting payment is contingent on results without a formal contract
Simply not mentioning salary, benefits, or an offer letter is NEUTRAL — mark CLEAR unless one of the above exploitative patterns is explicitly present.

---

At the end, output a SUMMARY TABLE with all 6 categories, their TRIGGERED/CLEAR status, severity if triggered, and one-line reason. Then state the total: X / 6 CATEGORIES TRIGGERED.
```

---

## Stage 3 — 🎯 Trust Score Calculator (logic summary)

Applies deterministic scoring rules on top of Stage 2's category results, in strict priority order:

1. **HIGH RISK override:** 3+ categories triggered, OR any confirmed impersonation risk → score capped at 3 or below, label HIGH RISK — this takes priority over everything else
2. **Anti-scam cap:** if Company Legitimacy is CLEAR and the posting includes explicit anti-scam warnings, escalation caps at CAUTION (5–6) unless Money or Unpaid Work/Ghosting are also triggered (those can still push to HIGH RISK even from a verified company)
3. **Standard scale otherwise:** 0 triggered = 9–10 Safe, 1 triggered = 7–8 Safe, 2 triggered = 5–6 Caution

Output includes a visual progress bar (e.g. `⬛⬛⬜⬜⬜⬜⬜⬜⬜⬜ 🔴 HIGH RISK`) and a one-line justification citing which rule determined the result.

---

## Stage 4 — 📋 Verdict and Next Steps (logic summary)

Converts the score into:
- A disclaimer noting the analysis is text-based only, not live-verified
- A plain-English overall verdict, using signal-based language ("several signals suggest...") rather than absolute accusations
- A concrete action checklist (e.g. MCA21 company registration lookup, verifying official portals directly, never sharing documents pre-offer, reporting to cybercrime.gov.in when appropriate)
- For impersonation cases specifically: an explicit clarification that the real, named company is not being accused — only the specific unverified posting is flagged

---

## Iteration Notes

This logic went through several rounds of real bug fixes during testing, including:
- Adding a missing "positive legitimacy signals" extraction field after discovering it caused false positives on detailed, legitimate technical job postings
- Fixing an over-strict impersonation rule that briefly refused to accept even a genuine domain-matching email as proof of affiliation
- Adding an explicit distinction between "unknown company" (neutral, common for small businesses) and "named real company with no proof" (impersonation risk, always flagged)
- Restoring a Personal Info Overreach check after it was accidentally replaced by an unrelated "Contact Method" category during a pipeline restructure
