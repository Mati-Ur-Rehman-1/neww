n8n Workflow Documentation

Selr AI — Audit Initial Shopping List Generation

---

## Workflow Diagram

```
┌─────────────────────────────────────────────┐
│         🔗 Webhook                          │
│   Step 1: Webhook                           │
│   Receives GHL form data on /ai-audit       │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         💻 Code Node                        │
│   Step 2: Clean Webhook Data                │
│   Extracts and formats specific form fields │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         📊 Google Sheets                    │
│   Steps 3 & 4: Fetch Agent 1 References     │
│   Reads Tech Stack & Automation Patterns    │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         💻 Code Node                        │
│   Step 5: Parse data to Agent 1             │
│   Merges client data with sheet references  │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         🤖 OpenAI (GPT-5.2)                 │
│   Step 6: Opportunity Identifier            │
│   Agent 1: Identifies automation gaps       │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         💻 Code Node                        │
│   Step 7: Clean Agent 1 Output              │
│   Parses string to clean JSON               │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         📊 Google Sheets                    │
│   Steps 8 & 9: Fetch Agent 2 References     │
│   Reads Tier Classifications & Adjustments  │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         💻 Code Node                        │
│   Step 10: Parse Agent 1 Output & Ref Data  │
│   Prepares payload for Complexity Assessor  │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         🤖 OpenAI (GPT-5.2)                 │
│   Step 11: Complexity Assessor              │
│   Agent 2: Estimates hours and tiers        │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         💻 Code Node                        │
│   Step 12: Clean Agent 2 Output             │
│   Cleans AI output and merges data          │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         📊 Google Sheets                    │
│   Steps 13 & 14: Fetch Agent 3 References   │
│   Reads Pricing Tiers & Adjustment Factors  │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         💻 Code Node                        │
│   Step 15: Parse Data To Agent 3            │
│   Compiles data for the final pricing agent │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         🤖 OpenAI (GPT-5.2)                 │
│   Step 16: Pricing & Packager               │
│   Agent 3: Prices items and structures JSON │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         💻 Code Node                        │
│   Step 17: Clean Agent 3 Output             │
│   Strict Budget Enforcer & Totals Validator │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         💻 Code Node                        │
│   Step 18: Convert JSON to HTML             │
│   Generates branded HTML layout for PDF     │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         📄 PDF.co                           │
│   Step 19: PDFco API                        │
│   Converts HTML payload to a hosted PDF     │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         💻 Code Node                        │
│   Step 20: Prepare GHL Payload              │
│   Structures mapped fields for GHL push     │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         📞 GoHighLevel API                  │
│   Step 21: Create or Update a Contact       │
│   Updates Custom Fields with PDF and JSON   │
└─────────────────────────────────────────────┘
```

---

## Step-by-Step Node Documentation

### Step 1 — Webhook

**App:** n8n Core Node
**Action:** Listen for Event
**Type:** Trigger

**Purpose:**
Acts as the entry point for the automation. It listens for a POST request on the `/ai-audit` endpoint, which is triggered by GoHighLevel when a client submits their Deep Onboarding Form.

**Configuration Notes:**
* Path: `ai-audit`
* Method: POST

---

### Step 2 — Code Node: Clean Webhook Data

**App:** Code
**Action:** Run JavaScript
**Type:** Action

**Purpose:**
Extracts raw payload data from the GHL webhook and maps it into a clean, structured JSON object. It pulls out critical keys including Business Name, budget range, current tools, pain points, existing automations, and all 74 form responses.

---

### Step 3 — Google Sheets: Tech Stack Sheet

**App:** Google Sheets
**Action:** Get Many Spreadsheet Rows
**Type:** Action

**Purpose:**
Fetches reference data from the "Tech Stack Integrations" tab to understand the integration complexity of the client's current tools.

---

### Step 4 — Google Sheets: Automation Patterns Sheet

**App:** Google Sheets
**Action:** Get Many Spreadsheet Rows
**Type:** Action

**Purpose:**
Fetches reference data from the "Automation Patterns" tab to provide Agent 1 with standard automation templates and default complexity scores.

---

### Step 5 — Code Node: Parse Data to Agent 1

**App:** Code
**Action:** Run JavaScript
**Type:** Action

**Purpose:**
Consolidates the cleaned client data (Step 2), tech stack data (Step 3), and automation patterns (Step 4) into a single JSON object (`client_data` and `reference_data`) to pass safely to Agent 1.

---

### Step 6 — OpenAI: Opportunity Identifier (Agent 1)

**App:** OpenAI (LangChain)
**Action:** POST
**Type:** Action

**Purpose:**
This is **Agent 1**, running GPT-5.2. It acts as an expert automation consultant, analysing the client's complete form data to identify explicit and implicit automation opportunities. It is strictly instructed not to suggest tools outside the client's confirmed tech stack.

**Agent 1 System Prompt Summary:**
> You are an AI automation consultant for Selr AI. Analyse business intake forms and identify all automation opportunities — both explicit (stated in challenge/vision fields) and implicit (embedded in process descriptions). Cross-reference the client's tech stack. Output as structured JSON only.

**Output JSON Structure:**
```json
{
  "opportunities": [
    {
      "id": "OPP-001",
      "name": "Lead Response Automation",
      "category": "lead_capture",
      "description": "Automated response within 5 minutes of form submission",
      "source_questions": ["Q35", "Q36", "Q37"],
      "pain_points_addressed": ["Leads get forgotten", "Response takes too long"],
      "systems_involved": ["GoHighLevel", "Email", "SMS"],
      "client_priority": 1,
      "notes": "Client explicitly mentioned slow response times"
    }
  ],
  "tech_stack_summary": {},
  "constraints": {}
}
```

---

### Step 7 — Code Node: Clean Agent 1 Output

**App:** Code
**Action:** Run JavaScript
**Type:** Action

**Purpose:**
Removes any Markdown formatting (e.g. ` ```json `) from Agent 1's output, parses the string into a valid JSON object, and extracts the cleaned array of opportunities.

---

### Steps 8 & 9 — Google Sheets: Adjustment Factors & Tier Classification

**App:** Google Sheets
**Action:** Get Many Spreadsheet Rows
**Type:** Action

**Purpose:**
Fetches the dynamic rules for pricing adjustments and tier maximum hours. These are used by Agent 2 to accurately estimate build workload and classify each opportunity.

---

### Step 10 — Code Node: Parse Agent 1 Output & Reference Sheet Data

**App:** Code
**Action:** Run JavaScript
**Type:** Action

**Purpose:**
Combines Agent 1's identified opportunities with the freshly fetched Tier Classification and Adjustment Factor data so Agent 2 has full context for its complexity scoring.

---

### Step 11 — OpenAI: Complexity Assessor (Agent 2)

**App:** OpenAI (LangChain)
**Action:** POST
**Type:** Action

**Purpose:**
This is **Agent 2**. It estimates exact build hours for each opportunity by analysing triggers, integrations, data transformations, AI calls, and conditional logic. It classifies each automation into a tier based strictly on estimated hours, and flags anything exceeding 20 hours as a Retainer candidate.

**Tier Classification Rules:**

| Tier | Max Hours | Workflows | Steps | Integrations | AI Calls |
|---|---|---|---|---|---|
| Single-Stage | ≤ 5 hrs | 1 | 1–3 | 1–2 | 0–1 |
| Multi-Stage | ≤ 10 hrs | 1 | 4–8 | 2–4 | 1–2 |
| Multi-Workflow | ≤ 20 hrs | 2+ | 8+ | 4+ | 2+ |
| Retainer Flag | > 20 hrs | — | — | — | — |

**Output JSON Structure:**
```json
{
  "assessed_opportunities": [
    {
      "id": "OPP-001",
      "name": "Lead Response Automation",
      "tier": "single_stage",
      "estimated_hours": 4,
      "complexity_factors": {
        "integrations": 2,
        "data_transformation": "simple",
        "ai_calls": 1,
        "conditional_logic": "low",
        "trigger_type": "single"
      },
      "reasoning": "Single trigger (form submission), 2 integrations (GHL → Email/SMS), 1 AI call for personalisation"
    }
  ],
  "retainer_candidates": [],
  "total_estimated_hours": 45
}
```

---

### Step 12 — Code Node: Clean Agent 2 Output

**App:** Code
**Action:** Run JavaScript
**Type:** Action

**Purpose:**
Cleans markdown from Agent 2's response, parses the JSON, and merges the new tier classifications and hours back with the original client data for the next stage.

---

### Steps 13 & 14 — Google Sheets: Pricing Tier & Adjustment Factors

**App:** Google Sheets
**Action:** Get Many Spreadsheet Rows
**Type:** Action

**Purpose:**
Fetches the financial reference sheets (base prices, max prices, and adjustment costs) for Agent 3. Fetched fresh here to guarantee the most up-to-date pricing matrix is used.

---

### Step 15 — Code Node: Parse Data to Agent 3

**App:** Code
**Action:** Run JavaScript
**Type:** Action

**Purpose:**
Constructs the final payload for Agent 3. It includes: assessed opportunities, client's stated budget (from form), client's priority ranking, pricing tiers, and all adjustment factors.

---

### Step 16 — OpenAI: Pricing & Packager (Agent 3)

**App:** OpenAI (LangChain)
**Action:** POST
**Type:** Action

**Purpose:**
This is **Agent 3**. It calculates the dollar value of every automation, enforces tier price maximums, prioritises automations based on client ranking and business impact, groups items into a phased roadmap, and structures the complete final Shopping List JSON.

**Pricing Algorithm:**

| Tier | Base Price | Max Price |
|---|---|---|
| Single-Stage | $2,500 | $3,000 |
| Multi-Stage | $4,000 | $6,000 |
| Multi-Workflow | $6,000 | $8,000 |

**Adjustment Factors (applied per opportunity):**

| Factor | Adjustment |
|---|---|
| Additional Integration (beyond base) | +$250 |
| Complex Data Transformation | +$250 |
| Additional AI Agent | +$250 |
| High Uptime / Real-Time Requirement | +$500 |
| Existing Automation Foundation | −$250 |

**Output JSON Structure:**
```json
{
  "shopping_list": {
    "executive_summary": {
      "total_investment_range": "$15,500 - $18,000",
      "top_3_priorities": [],
      "recommended_starting_point": "...",
      "estimated_total_hours": 45
    },
    "automations": [
      {
        "id": "AUTO-001",
        "name": "Lead Response Automation",
        "description": "Instant personalised response to new leads via email and SMS within 5 minutes of form submission.",
        "tier": "Single-Stage",
        "price": 2750,
        "priority": 1,
        "estimated_hours": 4,
        "systems_involved": ["GoHighLevel", "Email", "SMS", "OpenAI"],
        "category": "Lead Capture & Follow-up",
        "business_impact": "Reduce response time from 24 hours to 5 minutes",
        "monthly_infrastructure": 300
      }
    ],
    "investment_breakdown": {
      "single_stage": { "count": 3, "total": 8250 },
      "multi_stage": { "count": 2, "total": 9000 },
      "multi_workflow": { "count": 1, "total": 7000 },
      "grand_total": 24250,
      "monthly_infrastructure_total": 850
    },
    "phased_roadmap": {
      "phase_1": [],
      "phase_2": [],
      "phase_3": []
    },
    "retainer_candidates": [],
    "next_steps": []
  }
}
```

---

### Step 17 — Code Node: Clean Agent 3 Output (Budget Enforcer)

**App:** Code
**Action:** Run JavaScript
**Type:** Action

**Purpose:**
Acts as the strict **"Budget Enforcer"**. This code node intercepts Agent 3's output and hard-verifies all pricing math. If the total proposed build cost exceeds the client's stated budget ceiling, it automatically defers lower-priority automations to a "Future Phase" to guarantee the final proposal never exceeds budget.

**What this node does:**
* Strips markdown and parses raw AI output to clean JSON
* Verifies that no individual price exceeds its tier maximum
* Checks if the total grand total exceeds the client's budget
* If over budget: defers lowest-priority items to a "future_phase" array until total is within range
* Rebuilds investment_breakdown totals dynamically
* Rebuilds phase_1 roadmap based on updated priorities

---

### Step 18 — Code Node: Convert JSON to HTML

**App:** Code
**Action:** Run JavaScript
**Type:** Action

**Purpose:**
Takes the validated JSON from Step 17 and injects it into a branded HTML/CSS template. Formats all sections including the cover page, executive summary, automation list, investment breakdown, phased roadmap, retainer candidates, and next steps.

---

### Step 19 — PDF.co: PDFco API

**App:** PDF.co
**Action:** URL/HTML to PDF
**Type:** Action

**Purpose:**
Takes the raw HTML string from Step 18 and converts it into a high-quality, client-ready PDF. Returns a secure hosted URL for the generated document.

**Configuration Notes:**
* API Docs: https://developer.pdf.co/
* Filename format: `{Business_Name}_Automation_Shopping_List_{Date}.pdf`

---

### Step 20 — Code Node: Prepare GHL Payload

**App:** Code
**Action:** Run JavaScript
**Type:** Action

**Purpose:**
Extracts the generated PDF URL from the PDF.co response and structures the final JSON payload needed to update the client's GoHighLevel contact record.

---

### Step 21 — GoHighLevel API: Create or Update Contact

**App:** HighLevel
**Action:** Update Contact
**Type:** Action

**Purpose:**
Pushes all generated assets back to the CRM using the `contact_id` from the original webhook payload.

**Fields Updated:**

| GHL Custom Field | Value |
|---|---|
| Initial Shopping List | PDF.co secure URL |
| Shopping List JSON | Raw JSON (used by revision workflow) |
| Total Estimated Investment | Calculated range string |
| Automation Count | Total number of automations |
| Audit Status | "Shopping List Generated" |

---

## Workflow Summary

This n8n workflow completely automates the Selr AI audit process. Triggered instantly upon the submission of a client's Deep Onboarding Form, it fetches current pricing and rules from Google Sheets. It then routes the data through a 3-agent pipeline: Agent 1 identifies opportunities, Agent 2 assesses technical complexity and hours, and Agent 3 packages the pricing. A custom Budget Enforcer node guarantees the proposal stays within the client's budget. Finally, the structured data is converted into a branded HTML PDF via PDF.co and pushed back to the client's GoHighLevel contact record.

| Step | App | Action | Role |
|---|---|---|---|
| 1 | n8n Webhook | Listen for Event | Trigger — receives GHL form data on /ai-audit |
| 2 | Code | Run JavaScript | Cleans and formats raw webhook data |
| 3 | Google Sheets | Get Many Rows | Fetches Tech Stack Integrations sheet |
| 4 | Google Sheets | Get Many Rows | Fetches Automation Patterns sheet |
| 5 | Code | Run JavaScript | Merges client data with reference data for Agent 1 |
| 6 | OpenAI GPT-5.2 | POST | Agent 1 — Opportunity Identifier |
| 7 | Code | Run JavaScript | Strips markdown, parses Agent 1 JSON output |
| 8 | Google Sheets | Get Many Rows | Fetches Adjustment Factors sheet |
| 9 | Google Sheets | Get Many Rows | Fetches Tier Classification sheet |
| 10 | Code | Run JavaScript | Prepares payload for Agent 2 |
| 11 | OpenAI GPT-5.2 | POST | Agent 2 — Complexity Assessor |
| 12 | Code | Run JavaScript | Cleans Agent 2 output and merges data |
| 13 | Google Sheets | Get Many Rows | Fetches Pricing Tiers sheet |
| 14 | Google Sheets | Get Many Rows | Fetches Adjustment Factors (final fetch) |
| 15 | Code | Run JavaScript | Compiles full payload for Agent 3 |
| 16 | OpenAI GPT-5.2 | POST | Agent 3 — Pricing & Packager |
| 17 | Code | Run JavaScript | Budget Enforcer — verifies math, defers over-budget items |
| 18 | Code | Run JavaScript | Generates branded HTML layout for PDF |
| 19 | PDF.co | HTML to PDF | Converts HTML to hosted PDF, returns secure URL |
| 20 | Code | Run JavaScript | Extracts PDF URL, structures GHL update payload |
| 21 | HighLevel | Update Contact | Pushes PDF URL + JSON back to GHL contact record |
