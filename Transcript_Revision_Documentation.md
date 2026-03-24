n8n Workflow Documentation

Selr AI — Post-Discovery Transcript Revision Workflow

---

## Workflow Diagram

```
┌─────────────────────────────────────────────┐
│         🔗 Webhook                          │
│   Step 1: Webhook                           │
│   Receives discovery call transcript & JSON │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         💻 Code Node                        │
│   Step 2: Clean Webhook Data                │
│   Extracts Transcript, Name & Shopping List │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         🤖 OpenAI (GPT-5.2)                 │
│   Step 3: Revision Agent                    │
│   Amends shopping list based on transcript  │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         💻 Code Node                        │
│   Step 4: Hallucination Safety Net          │
│   Fixes AI hallucinations & rebuilds math   │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         💻 Code Node                        │
│   Step 5: Convert JSON to HTML              │
│   Generates "Revised" PDF layout template   │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         📄 PDF.co API                       │
│   Step 6: PDFco API                         │
│   Converts HTML to hosted PDF document      │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         📞 GoHighLevel API                  │
│   Step 7: Create or Update a Contact        │
│   Saves Revised PDF & updates Audit Status  │
└─────────────────────────────────────────────┘
```

---

## Trigger Conditions

This workflow is triggered by an internal GHL form submission. The form is submitted by a Selr AI team member after completing a discovery call with the client.

**Required Payload:**
* `contact_id` — GHL contact ID for the client
* `shopping_list_json` — The original Shopping List JSON stored in GHL
* `transcript` — Full pasted text of the discovery call
* `manual_notes` — Any additional notes from the call (optional)

**Endpoint:** `/zoom-integration`

---

## Step-by-Step Node Documentation

### Step 1 — Webhook

**App:** n8n Core Node
**Action:** Listen for Event
**Type:** Trigger

**Purpose:**
Triggers the revision workflow when an internal team member submits the post-discovery form from GoHighLevel.

**Configuration Notes:**
* Endpoint path: `/zoom-integration`
* Accepts a POST payload containing the original Shopping List JSON, the pasted call transcript, and the Contact ID.

---

### Step 2 — Code Node: Clean Webhook Data

**App:** Code
**Action:** Run JavaScript
**Type:** Action

**Purpose:**
Acts as a data normaliser. It standardises the incoming payload, handling potential field name variations (e.g. typos like `Shooping List` vs `Shopping List`). It safely parses the stringified original JSON and extracts the raw transcript text.

**Configuration Notes:**
* Also extracts `business_name`, `email`, and `full_name` from the raw GHL form payload for use in the revised PDF header.

---

### Step 3 — OpenAI: Revision Agent

**App:** OpenAI (LangChain)
**Action:** POST
**Type:** Action

**Purpose:**
Acts as the **Revision Agent**. It analyses the discovery call transcript against the original shopping list JSON and intelligently applies requested changes — without modifying items that were not discussed in the call.

**What the Revision Agent does:**

* Parses the transcript for:
  * New automation requests mentioned
  * Clarifications on existing items
  * Priority changes discussed
  * Items the client explicitly rejected
  * Budget constraints mentioned
  * Timeline pressures raised
* For each original automation item:
  * Confirms, adjusts, or removes based on call
  * Updates priority if discussed
  * Refines description with specifics from the call
* Adds new items discovered in the call
* Re-runs pricing for any adjusted items
* Updates the executive summary
* Generates a `changes_from_discovery_call` object logging what was added, removed, or modified

**Agent System Prompt Summary:**
> You are a revision agent for Selr AI. You are given an original Shopping List JSON and a discovery call transcript. Update the shopping list to reflect what was discussed. Do not modify items that were not mentioned. Do not hallucinate new prices below tier minimums unless the client explicitly stated a lower price. Output a complete revised JSON including a changes_from_discovery_call summary.

**Configuration Notes:**
* Strictly instructed NOT to change prices or tiers without explicit transcript justification.
* Outputs a `changes_from_discovery_call` object with sub-arrays: `added`, `removed`, `modified`, `priority_shifts`.

---

### Step 4 — Code Node: Hallucination Safety Net

**App:** Code
**Action:** Run JavaScript
**Type:** Action

**Purpose:**
A critical safety net that scrubs the AI output and performs a **Smart Original Fields Restore**. It compares the AI's revised output with the original JSON and overrides any AI hallucinations — specifically unauthorised price or tier changes — if no explicit reason was logged in `changes_from_discovery_call`.

**What this node does:**
* Strips markdown and parses raw AI output to clean JSON
* Compares each automation item against the original
* Overrides any price or tier change not justified in the change log
* Applies **Retainer Bleed Fix** — ensures ongoing maintenance items are pushed out of the build automations array
* Dynamically recalculates all financial totals, infrastructure costs, and total hours
* Rebuilds Phase 1 roadmap based on updated priorities
* Generates a mathematical verification summary

---

### Step 5 — Code Node: Convert JSON to HTML

**App:** Code
**Action:** Run JavaScript
**Type:** Action

**Purpose:**
Maps the validated revised JSON into the Selr AI HTML PDF template. This node contains additional UI blocks specific to the revision phase:

* **Changes from Discovery Call** section with four sub-sections:
  * ✅ Added items
  * ❌ Removed items
  * ✏️ Modified items
  * 🔼 Priority shifts
* **Budget Constraints** notes (if budget was discussed in the call)

---

### Step 6 — PDF.co: PDFco API

**App:** PDF.co
**Action:** URL/HTML to PDF
**Type:** Action

**Purpose:**
Converts the revised HTML string from Step 5 into a professional, client-ready PDF document.

**Configuration Notes:**
* File is named with a `Revised` suffix to clearly differentiate from the initial automated audit document.
* Filename format: `{Business_Name}_Automation_Shopping_List_REVISED_{Date}.pdf`

---

### Step 7 — GoHighLevel API: Create or Update Contact

**App:** HighLevel
**Action:** Update Contact
**Type:** Action

**Purpose:**
Pushes the revised proposal back to the client's CRM record, ready for final team review before sending to the client.

**Fields Updated:**

| GHL Custom Field | Value |
|---|---|
| Post Discovery Call Shopping List | New PDF.co secure URL (revised PDF) |
| Shopping List JSON | Updated JSON reflecting revision changes |
| Audit Status | "Discovery Complete" |

**Fields Preserved (not overwritten):**

| GHL Custom Field | Status |
|---|---|
| Initial Shopping List | Unchanged — original PDF preserved |

---

## Workflow Summary

This n8n workflow automates the amendment of existing proposals after a client discovery call. Triggered by an internal form submission containing the call transcript, it uses an AI Revision Agent to intelligently update the original shopping list JSON based on explicit client feedback. A robust code node then scrubs the AI output to prevent hallucinations and recalculates all financial metrics. The updated data is injected into a revised HTML template, converted into a new PDF via PDF.co, and synced back to the GoHighLevel contact record — ensuring the sales team always has an accurate, up-to-date proposal ready to send.

| Step | App | Action | Role |
|---|---|---|---|
| 1 | n8n Webhook | Listen for Event | Trigger — receives transcript and original JSON |
| 2 | Code | Run JavaScript | Normalises payload and extracts transcript text and JSON |
| 3 | OpenAI GPT-5.2 | POST | Revision Agent — updates list based on call insights |
| 4 | Code | Run JavaScript | Safety net — fixes hallucinations and recalculates totals |
| 5 | Code | Run JavaScript | Generates revised HTML with Changes summary section |
| 6 | PDF.co | HTML to PDF | Converts revised HTML to PDF (Revised suffix) |
| 7 | HighLevel | Update Contact | Saves revised PDF + sets Audit Status to Discovery Complete |
