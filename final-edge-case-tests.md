# Final Edge-Case Tests — Job Posting Red Flag Analyzer

Run each of these and check against the expected result. All company names are fictional to avoid any real-world reputational risk.

---

## Test A — Money Red Flag (should TRIGGER)
**Expected: Category 1 (Money) TRIGGERED, score should drop to High Risk (likely 2-3/10) since this is a hard scam signal.**

```
Job Title: Data Entry Executive (Work From Home)
Company: BrightPath Solutions

We are hiring for Data Entry Executive roles, work from home, flexible hours.

Selected candidates must pay a one-time refundable security deposit of ₹2,500 to receive the training kit and login credentials. This amount will be refunded after 3 months of successful work.

Interested candidates message us on WhatsApp: 9xxxxxxxxx
```

---

## Test B — Personal Info Overreach (should TRIGGER)
**Expected: Category 5 (Personal Info Overreach) TRIGGERED — asking for actual Aadhaar/PAN numbers and bank details before any interview, not just name/DOB matching.**

```
Job Title: Customer Support Associate
Company: Zentrix Services

We're expanding our support team! To proceed with your application, please reply with the following details so we can process your candidature:

- Aadhaar card number
- PAN card number
- Bank account number and IFSC code (for future salary processing)
- A scanned copy of your 10th and 12th marksheets

Once submitted, our HR will schedule your interview within 24 hours.
```

---

## Test C — Unpaid Work / Ghosting Pattern (should TRIGGER)
**Expected: Category 6 (Unpaid Work and Ghosting) TRIGGERED — explicit unpaid trial before pay begins.**

```
Job Title: Content Writer Intern
Company: WordCraft Media

Looking for enthusiastic content writers to join our team!

Selection process: Complete a 3-week unpaid trial period where you'll write live articles for our website. Based on your performance and engagement metrics during this period, we will decide your stipend and whether to convert you to a paid role.

No prior experience needed. Apply by DM.
```

---

## Test D — Small but Legitimate Company (should be SAFE, not just Caution)
**Expected: Even though it's an unknown/small company, decent specific detail should let it score reasonably Safe (7+/10), not capped at Caution just for being small.**

```
Job Title: Junior Frontend Developer
Company: PixelForge Technologies (5-person startup, Pune)

We're a small product studio building a SaaS tool for restaurant inventory management. Looking for a Junior Frontend Developer to join our 5-person team.

Responsibilities:
- Build and maintain UI components using React and Tailwind CSS
- Work directly with our founder (ex-Zomato engineer) on product features
- Participate in weekly sprint planning

Requirements:
- 0-1 years experience, familiarity with React, HTML/CSS, Git
- Portfolio or GitHub profile required

Compensation: ₹4.5-5.5 LPA, formal offer letter provided, 3-month probation with full pay from day one.

Apply with your resume and portfolio to careers@pixelforge.dev
```

---

## Test E & F — Re-confirm previous cases still hold after all recent fixes
Pull from `test-job-postings.md`:
- **TCS official posting** → should still be Safe
- **Virtusa UiPath JD** → should still be Safe
