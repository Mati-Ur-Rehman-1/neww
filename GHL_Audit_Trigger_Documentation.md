GoHighLevel Workflow Documentation

Selr AI — Audit Onboarding Form Completed (Trigger Flow)

---

## Workflow Diagram

```
┌─────────────────────────────────────────────┐
│         📝 TRIGGER: Survey Submitted        │
│   Event: Deep Onboarding Form               │
│   Fires when client completes the audit form│
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         🏷️ Add Contact Tag                 │
│   Step 1: Tag Contact                       │
│   Applies 'onboarding-form-submitted' tag   │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         🔗 Webhook (Implicit/Background)    │
│   Step 2: Trigger n8n Pipeline              │
│   Sends payload to n8n /ai-audit endpoint   │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         🔍 Find Opportunity                 │
│   Step 3: Board 2 Mid Funnel                │
│   Looks for the client's existing deal      │
└──────────┬─────────────────────────┬────────┘
           │                         │
      Found / True              Not Found
           │                         │
           ▼                         ▼
┌─────────────────────────┐        [END]
│ 🔄 Update Opportunity   │
│ Step 4: Board 2 Stage 2 │
└──────────┬──────────────┘
           │
           ▼
┌─────────────────────────┐
│ ✉️ Send Email          │
│ Step 5: Form Received + │
│ What's Next             │
└──────────┬──────────────┘
           │
           ▼
┌─────────────────────────┐
│ 🔀 Condition: Phone?    │
│ Step 6: Phone Exists?   │
└─────┬──────────────┬────┘
      │              │
  Not Empty        Empty
      │              │
      ▼              ▼
┌─────────────┐  ┌───────┐
│ 💬 Send SMS │  │ Go To │
│ Form Recv'd │  │ Next  │
└─────┬───────┘  └─┬─────┘
      │            │
      └──────┬─────┘
             │
             ▼
┌─────────────────────────┐
│ 📧 Internal Email      │
│ Step 7: Harvey — Prep   │
│ AI Strategy Doc         │
└──────────┬──────────────┘
           │
           ▼
┌─────────────────────────┐
│ 🔀 Condition: Phone?    │
│ Step 8: Phone Exists?   │
└─────┬──────────────┬────┘
      │              │
  Not Empty        Empty
      │              │
      ▼              ▼
┌─────────────┐    [END]
│ 📱 Int. SMS │
│ To: Harvey  │
└─────────────┘
```

---

## Step-by-Step Node Documentation

### TRIGGER — Survey Submitted

**App:** GoHighLevel
**Action:** Survey Submitted
**Type:** Trigger

**Purpose:**
This is the starting point of the entire AI Audit Automation system. It fires immediately when a client successfully submits the Deep Onboarding Form.

**Configuration Notes:**
* Acts as the catalyst for both internal GHL pipeline movements and the external n8n AI processing pipeline.

---

### Step 1 — Add Contact Tag

**App:** GoHighLevel
**Action:** Add Contact Tag
**Type:** Action

**Purpose:**
Applies the `onboarding-form-submitted` tag to the contact. This provides a clear visual indicator in the CRM that the client has completed their required inputs.

---

### Step 2 — Webhook (n8n Handoff)

**App:** GoHighLevel
**Action:** Webhook
**Type:** Action (Data Handoff)

**Purpose:**
This critical step bridges GHL and n8n. It sends an HTTP POST request containing the full contact and survey payload to the n8n webhook URL.

**Configuration Notes:**
* Target URL: `https://selrai.app.n8n.cloud/webhook/ai-audit`
* Method: POST
* This triggers the external AI agents to begin generating the Shopping List PDF in the background.

---

### Step 3 — Find Opportunity

**App:** GoHighLevel
**Action:** Find Opportunity
**Type:** Action / Condition

**Purpose:**
Searches the "Board 2 Mid Funnel" pipeline to locate the client's existing deal card.

**Branches:**
* **Opportunity Found:** Proceeds to update the opportunity.
* **Opportunity Not Found:** Safely ends the workflow to prevent errors.

---

### Step 4 — Update Opportunity

**App:** GoHighLevel
**Action:** Update Opportunity
**Type:** Action

**Purpose:**
Moves the client's deal card to "Board 2 Stage 2" to reflect that their onboarding data has been received and their audit is currently being processed.

---

### Step 5 — Send Email

**App:** GoHighLevel
**Action:** Send Email
**Type:** Action

**Purpose:**
Sends a confirmation email to the client ("Form Received + What's Next") assuring them that their data was successfully submitted and setting expectations for the next steps — including delivery of their AI Audit Shopping List.

---

### Step 6 — Condition: Phone Exists?

**App:** GoHighLevel
**Action:** If/Else Condition
**Type:** Logic

**Purpose:**
Checks if the client provided a valid phone number during the onboarding process.

**Branches:**
* **If "Phone" is not empty:** Proceeds to send an SMS confirmation.
* **None (Empty):** Bypasses the SMS step and goes directly to internal notifications.

---

### Step 7 — Send SMS (Optional Branch)

**App:** GoHighLevel
**Action:** Send SMS
**Type:** Action

**Purpose:**
Sends a quick text message to the client ("Form Received") for immediate, high-visibility confirmation.

---

### Step 8 — Internal Email

**App:** GoHighLevel
**Action:** Send Internal Email
**Type:** Action

**Purpose:**
Sends an internal alert to Harvey (and the team) to prepare the AI Strategy Document. This runs in parallel while n8n is generating the automated PDF shopping list.

---

### Step 9 — Condition & Internal SMS

**App:** GoHighLevel
**Action:** Condition + Send SMS
**Type:** Logic / Action

**Purpose:**
Checks if the internal recipient's phone number is configured, and if so, fires an Internal SMS alert ("Harvey — Prep AI Strategy Doc") for immediate team awareness.

---

## Workflow Summary

This GoHighLevel workflow acts as the command centre for the post-purchase audit phase. Triggered by the submission of the Deep Onboarding Form, it immediately updates the client's CRM status, tags the contact, and sends them professional email/SMS confirmations. Crucially, it handles the data handoff by firing a webhook to n8n — which initiates the multi-agent AI pipeline to build the Automation Shopping List — while simultaneously alerting the Selr AI team to prepare for the upcoming discovery call.

| Step | App | Action | Role |
|---|---|---|---|
| Trigger | GoHighLevel | Survey Submitted | Starts the flow on Deep Onboarding Form completion |
| 1 | GoHighLevel | Add Contact Tag | Tags contact as `onboarding-form-submitted` |
| 2 | Webhook | POST | Handoff payload to n8n for AI processing |
| 3 | GoHighLevel | Find Opportunity | Locates the deal in Board 2 Mid Funnel |
| 4 | GoHighLevel | Update Opportunity | Moves deal to Stage 2 |
| 5 | GoHighLevel | Send Email | Sends client confirmation and next steps |
| 6 | GoHighLevel | If/Else | Checks if client phone number exists |
| 7 | GoHighLevel | Send SMS | Sends client SMS confirmation (if applicable) |
| 8 | GoHighLevel | Send Internal Email | Alerts Harvey to prep strategy document |
| 9 | GoHighLevel | If/Else & SMS | Sends internal SMS alert to Harvey |
