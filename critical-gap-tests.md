# Critical Gap Tests

## Test E — Impersonation Generalization (brand NOT in the rule's example list)
**Expected: Company Legitimacy TRIGGERED — HIGH, Impersonation Risk. Score should drop to High Risk (2-3/10) via the Step 2 override, exactly like Flipkart/Meesho. This tests whether the rule generalizes beyond its listed examples (Flipkart, Meesho, TCS, Amazon, Infosys, Google, Zomato, Swiggy, Wipro, HCL, Accenture, Deloitte) to any real, well-known brand not explicitly listed. (Note: this test was originally run against a real large IT company's name; replaced here with a fictional placeholder for public sharing.)**

```
Job Opening — Immediate Hiring!

Company: Meridian Tech Solutions

We are conducting walk-in interviews for multiple roles including Customer Support Executive, Data Entry Operator, and Back Office Assistant.

Location: Remote / Work from home
Salary: ₹3.5 LPA to ₹8 LPA
Qualification: Any graduate

Interested candidates send your resume on WhatsApp: 7xxxxxxxxx or comment "interested" below.

Interview will be conducted this week itself.
```

---

## Test F — Verified Domain Email + Actual Red Flag (does the override still win?)
**Expected: Company Legitimacy should be CLEAR (domain-matched email present), BUT Money Red Flags should TRIGGER (explicit deposit request) — and the Money trigger should force the score to High Risk regardless of the clean Company Legitimacy, confirming the anti-scam cap does NOT protect a posting that has a real red flag present.**

```
Job Title: Junior Data Analyst
Company: Nexbridge Analytics

We're hiring Junior Data Analysts for our growing team.

Responsibilities:
- Clean and analyze datasets using Excel and SQL
- Prepare weekly reports for the operations team
- Support the senior analyst with dashboard creation

Requirements:
- Familiarity with Excel, basic SQL
- 0-1 years experience, freshers welcome

Selection Process: Shortlisted candidates will be required to pay a one-time refundable equipment fee of ₹3,000 for a company laptop and software licenses, to be reimbursed in your first salary.

Apply to: careers@nexbridgeanalytics.com
```
