# Job Posting Red Flag Analyzer

Built for **First Commit** (Bharat Builds Tour) — WeMakeDevs × AWS
**Track:** Build It

## The Problem

I've spent the last year job hunting as a fresher in India, and one of the most exhausting parts isn't the applications themselves — it's figuring out which postings are even real. I once applied to a company through an official national internship portal and got a "shortlisted" email within 15 minutes. It seemed legitimate until I found a LinkedIn post from someone who'd gone through the same company's process: weeks of unpaid "trial" work, a deadline met, then complete silence. Being on a trusted platform doesn't guarantee a fair employer — and freshers don't always know what to look for until they've already been burned once.

## What It Does

Paste any job posting's text in, and the app returns a **Trust Score (0–10)** with a clear, plain-English breakdown of exactly what's concerning and why.

It checks six patterns:
1. **Money red flags** — upfront fees, deposits, mandatory purchases
2. **Personal info overreach** — Aadhaar/PAN numbers, bank details requested pre-interview
3. **Urgency and pressure tactics** — artificial scarcity, instant shortlisting with no real screening
4. **Company legitimacy** — including a dedicated check for impersonation of real, well-known companies without verifiable proof of affiliation
5. **Vague or unrealistic role descriptions** — postings with zero substantive detail
6. **Unpaid work and ghosting risk** — unpaid trials, performance-gated pay, real work framed as an "assignment"

## Architecture

Built on **PartyRock**, which runs on **Amazon Bedrock** under the hood. Rather than a single AI call, the app is structured as a visible 4-stage pipeline:

```
Signal Extraction  →  Category Scoring  →  Trust Score Calculator  →  Verdict & Next Steps
```

- **🔍 Signal Extraction** — reads the raw posting and pulls out 9 structured facts (fees, contact method, urgency language, positive legitimacy signals, etc.)
- **📊 Category Scoring** — checks those facts against the 6 categories, with explicit rules to avoid both false positives and false negatives (see `PROMPTS.md` for the exact logic)
- **🎯 Trust Score Calculator** — applies deterministic override rules (e.g., 3+ triggered categories, or any unverified impersonation of a real company, forces High Risk regardless of other signals)
- **📋 Verdict and Next Steps** — plain-English explanation plus concrete action steps (MCA21 lookup, official portal verification, cybercrime.gov.in reporting)

## Where AWS Fits

- **Amazon Bedrock** (via PartyRock) powers every analysis in the pipeline
- **AWS Amplify Hosting** deploys a companion static landing page: [live link here]

## Testing

Stress-tested against real postings, not just synthetic examples — see `test-job-postings.md` and `critical-gap-tests.md` for the full test suite. Highlights:
- A real scam email I personally received (stipend paid only "post training/dummy project clearance") — correctly scored 2/10 High Risk
- An official TCS recruitment page — correctly scored Safe
- An official Accenture posting from their own careers page (10/10 Safe) vs. a LinkedIn repost of the same role using shortened redirect links and unrelated spam content (2/10 High Risk) — the tool correctly distinguished an official source from an unverified reshare of the same opportunity

Along the way, several real calibration bugs were found and fixed through iterative testing — including an over-correction that briefly stopped flagging real impersonation scams, caught before submission through a full regression pass.

## Honest Limitations

This analyzes posting text only — it doesn't check live company reviews, government registries, or real-time legitimacy, and the app says so explicitly. It also never claims a real company is fraudulent — only that a specific posting couldn't be verified as genuinely coming from them.

## Links

- **PartyRock Flow (live app):** https://partyrock.aws/u/Keerthimorugu/1rS53NjRA/Job-Posting-Red-Flag-Analyzer
- **Landing page (AWS Amplify):** https://staging.d1xi3v8e3ekis3.amplifyapp.com

## AI Tools Used

- **PartyRock (Whiskers)** — building and iterating the app's pipeline
- **Claude** — planning, test case design, debugging, and drafting this documentation

## Author

Keerthi Morugu ([@keerthi01228](https://github.com/keerthi01228))
