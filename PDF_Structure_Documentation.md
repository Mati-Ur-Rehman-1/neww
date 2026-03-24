PDF Output Structure Documentation

Selr AI — Automation Shopping List PDF

---

## Overview

Both the initial and revised Shopping List PDFs follow a consistent 8-section structure. The document is generated from structured JSON via an HTML/CSS template and rendered by PDF.co.

**Filename Format:**
* Initial: `{Business_Name}_Automation_Shopping_List_{Date}.pdf`
* Revised: `{Business_Name}_Automation_Shopping_List_REVISED_{Date}.pdf`

---

## Section 1 — Cover Page

| Element | Content |
|---|---|
| Logo | Selr AI logo |
| Title | "Automation Shopping List" |
| Client Name | Business name from form data |
| Date | Date the document was prepared |
| Prepared For | Full name of the contact |

---

## Section 2 — Executive Summary

A single-page overview designed for quick client consumption.

| Element | Content |
|---|---|
| Total Investment Range | Min–max range across all automations (e.g. "$15,500 – $18,000") |
| Automation Count | Total number of automations identified |
| Top 3 Priorities | Name and one-line description of the highest-priority automations |
| Recommended Starting Point | The single best first automation to implement |
| ROI / Time Savings Summary | Expected hours saved per week or efficiency gains |

---

## Section 3 — Detailed Automation List

One entry per identified automation. Spans 2–4 pages depending on the number of items.

**Per Automation:**

| Element | Content |
|---|---|
| Name | Clear automation name |
| Description | 2–3 sentence client-facing explanation |
| Tier Badge | Single-Stage / Multi-Stage / Multi-Workflow |
| Price | Fixed build cost (e.g. $2,750) |
| Priority | Ranking number (1 = highest) |
| Systems Involved | List of tools used (e.g. GoHighLevel, Stripe, OpenAI) |
| Estimated Hours | Build time estimate |
| Business Impact | One-line outcome statement (e.g. "Reduce response time from 24 hours to 5 minutes") |
| Monthly Infrastructure Cost | Ongoing API/tool costs to run the automation |

---

## Section 4 — Investment Breakdown

A single-page financial summary.

| Element | Content |
|---|---|
| Breakdown Table | Tier \| Count \| Build Cost \| Monthly Infrastructure |
| Total Build Investment | Sum of all automation build costs |
| Total Monthly Infrastructure | Sum of all ongoing costs |
| Payment Terms Reminder | 80% upfront / 20% on completion |

---

## Section 5 — Recommended Roadmap

A phased implementation plan grouped by complexity and dependency.

| Phase | Timeframe | Contents |
|---|---|---|
| Phase 1 | 1–2 weeks | Quick wins — high-impact, low-complexity automations |
| Phase 2 | 2–4 weeks | Core systems — foundational automations |
| Phase 3 | 4–8 weeks | Advanced automation — complex multi-workflow builds |

Includes a visual timeline representation.

---

## Section 6 — Retainer Candidates *(if applicable)*

Only included if one or more automations exceeded the 20-hour threshold.

| Element | Content |
|---|---|
| Item Name | Name of the automation |
| Reason | Why it exceeds the threshold (e.g. "Estimated 35+ hours — scope requires ongoing development") |
| Recommended Path | Invitation to discuss a retainer engagement |

---

## Section 7 — Next Steps

A clear call to action for the client.

**Pre-Discovery Call version includes:**
* Schedule a discovery call
* Review the shopping list and mark priorities

**Post-Discovery Call (Revised) version includes:**
* Approve the builds and sign the agreement
* Payment and kickoff timeline
* Contact information

---

## Section 8 — Terms & Conditions

| Element | Content |
|---|---|
| Quote Validity | 90 days from the date prepared |
| Payment Terms | 80% upfront / 20% on completion |
| Scope Protection | Notes on what is and is not included |
| Inclusions | What is covered in the build price |
| Exclusions | Ongoing maintenance, hosting, third-party API costs |

---

## Revised PDF Additions

When a PDF is generated from the Transcript Revision Workflow, two additional sections are included:

### Changes from Discovery Call

Inserted after the Executive Summary, before the Automation List.

| Sub-section | Content |
|---|---|
| ✅ Added | New automations identified during the call |
| ❌ Removed | Items the client explicitly rejected |
| ✏️ Modified | Items that were updated (description, price, priority) |
| 🔼 Priority Shifts | Items that moved up or down in priority |

### Budget Constraints Note *(if applicable)*

A brief note if the client raised budget limitations during the call that affected the final proposal.
