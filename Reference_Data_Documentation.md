Google Sheets Reference Data Documentation

Selr AI — Audit Pricing Reference

---

## Overview

The Google Sheets reference data acts as the dynamic "database" for the entire audit automation system. All pricing rules, complexity factors, and automation patterns are stored here — not hardcoded in any workflow.

**Sheet Name:** `Selr AI - Audit Pricing Reference`

**Purpose:** Allows the Selr AI team to update pricing, adjustment factors, and automation patterns at any time without touching the n8n workflows. Changes take effect on the very next workflow run.

---

## How It Is Used

| Workflow Step | Sheet Tab Used |
|---|---|
| Agent 1 — Opportunity Identifier | Tech Stack Integrations, Automation Patterns |
| Agent 2 — Complexity Assessor | Tier Classification, Adjustment Factors |
| Agent 3 — Pricing & Packager | Pricing Tiers, Adjustment Factors |

---

## Tab 1 — Pricing Tiers

Defines the base price, maximum price, and hour thresholds for each automation tier.

| Tier | Base Price | Max Price | Base Hours | Max Hours |
|---|---|---|---|---|
| Single-Stage | $2,500 | $3,000 | 3 | 5 |
| Multi-Stage | $4,000 | $6,000 | 6 | 10 |
| Multi-Workflow | $6,000 | $8,000 | 12 | 20 |

**Notes:**
* Any automation estimated above 20 hours is flagged as a **Retainer Candidate** and is not priced in the shopping list.
* Prices are rounded to the nearest $500 after adjustments are applied.

---

## Tab 2 — Adjustment Factors

Defines the dollar adjustments applied on top of each tier's base price. Agent 3 reads these and applies them per automation based on its complexity profile.

| Factor | Adjustment ($) | Description |
|---|---|---|
| Additional Integration | +250 | Per integration beyond the tier base count |
| Complex Data Transformation | +250 | ETL, data mapping, or format conversion required |
| Additional AI Agent | +250 | Per additional LLM call in the workflow |
| High Uptime Requirement | +500 | Mission-critical or real-time processing required |
| Existing Automation Foundation | −250 | Client already has partial automation in place |

**Notes:**
* Multiple adjustments can be stacked on a single automation.
* The final price is always capped at the tier maximum regardless of adjustments.

---

## Tab 3 — Automation Patterns

Pre-defined automation templates with default tier classifications and typical complexity scores. Agent 1 references this sheet to match client pain points to known patterns.

| Pattern Name | Default Tier | Typical Hours | Base Integrations | Description |
|---|---|---|---|---|
| Lead Response Automation | Single-Stage | 3 | 2 | Auto-response to new leads via email/SMS |
| Appointment Booking Flow | Single-Stage | 4 | 2 | Calendar booking with confirmations |
| Lead Nurture Sequence | Multi-Stage | 7 | 3 | Multi-touch follow-up campaign |
| Client Onboarding Sequence | Multi-Stage | 8 | 4 | Welcome emails, forms, task creation |
| Invoice Generation | Single-Stage | 4 | 2 | Auto-create invoices from triggers |
| Report Automation | Multi-Stage | 6 | 3 | Data aggregation and scheduled reports |
| CRM Data Sync | Multi-Stage | 5 | 3 | Bi-directional sync between systems |
| AI Content Generation | Single-Stage | 3 | 2 | AI-powered content creation |
| Customer Support Routing | Multi-Stage | 7 | 4 | Ticket classification and assignment |
| Full Sales Pipeline | Multi-Workflow | 15 | 5 | Lead → Proposal → Contract → Onboarding |
| Complete Onboarding System | Multi-Workflow | 18 | 6 | Multi-workflow client setup |

**Notes:**
* New patterns can be added at any time by appending rows to this tab.
* "Typical Hours" and "Base Integrations" are used as starting estimates only. Agent 2 may adjust based on the client's specific form data.
* The sheet supports up to 1,000 pattern entries without workflow modification.

---

## Tab 4 — Tech Stack Integrations

A reference list of common tools, their categories, and integration complexity. Agent 1 uses this to assess feasibility and Agent 2 uses it to factor integration cost into hour estimates.

| Tool Name | Category | Integration Complexity |
|---|---|---|
| GoHighLevel | CRM | Low |
| HubSpot | CRM | Low |
| Salesforce | CRM | Medium |
| Gmail / Google Workspace | Email | Low |
| Microsoft 365 | Email | Low |
| Twilio | SMS/Voice | Low |
| Slack | Chat | Low |
| Xero | Accounting | Medium |
| QuickBooks | Accounting | Medium |
| Stripe | Payments | Low |
| Google Sheets | Data | Low |
| Airtable | Data | Low |
| Notion | PM/Data | Medium |
| Asana | PM | Low |
| Monday | PM | Low |
| OpenAI | AI | Low |
| Claude | AI | Low |
| Custom API | Various | High |

**Notes:**
* New tools can be added by appending rows to this tab.
* "High" complexity integrations typically add +$250 to the adjustment total.
* If a client's tool is not listed, Agent 1 defaults to Medium complexity.

---

## GHL Custom Fields Reference

All custom fields are stored under the **Audit Automation** folder inside GoHighLevel.

| Field Name | Type | Purpose |
|---|---|---|
| Initial Shopping List | File/URL | Link to initial auto-generated PDF (PDF.co hosted) |
| Post Discovery Call Shopping List | File/URL | Link to revised PDF after discovery call |
| Shopping List JSON | Large Text | Raw JSON used by the revision workflow |
| Total Estimated Investment | Currency | Sum of all quoted build costs |
| Automation Count | Number | Total automations in the shopping list |
| Audit Status | Dropdown | Pending / Shopping List Generated / Discovery Complete / Proposal Sent / Closed Won / Closed Lost |

---

## Maintaining Reference Data

**To update pricing:** Edit values in Tab 1 — Pricing Tiers. Changes apply immediately on the next workflow run.

**To add a new automation pattern:** Append a new row to Tab 3 — Automation Patterns with the pattern name, default tier, typical hours, base integrations, and description.

**To add a new tool:** Append a new row to Tab 4 — Tech Stack Integrations with the tool name, category, and integration complexity.

**To update adjustment factors:** Edit values in Tab 2 — Adjustment Factors. No code changes required.
