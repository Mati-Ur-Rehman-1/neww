GoHighLevel Workflows Documentation

Selr AI — Internal Automation & Client Journey

---

## Overview

The GoHighLevel (GHL) workflows act as the central nervous system for the AI Audit process. They handle everything before and after the n8n AI pipelines run. This includes capturing the initial form data, sending webhooks to n8n, notifying the internal team when PDFs are generated, sending proposals to the client, managing contracts, and closing deals.

All workflows documented below reside in the **Selr AI Internal Automation** folder.

---

## 1. Send Form Data to n8n

**Purpose:** Triggers the Initial Shopping List generation in n8n when a client completes their Deep Onboarding Form.

### Workflow Diagram

```
┌─────────────────────────────────────────────┐
│         📝 TRIGGER: Survey Submitted        │
│   Event: Deep Onboarding Form               │
│   Fires when client completes the audit form│
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         🔗 Action: Webhook                  │
│   Step 1: Send Data to n8n                  │
│   Sends full payload to n8n /ai-audit       │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         🏷️ Action: Add Contact Tag          │
│   Step 2: Tag Contact                       │
│   Tag: 'Shopping list in process'           │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         👤 Action: Update Contact Field     │
│   Step 3: Update Status Fields              │
│   Updates relevant initial status fields    │
└─────────────────────────────────────────────┘
```

### Step-by-Step Node Documentation

#### TRIGGER — Survey Submitted

**App:** GoHighLevel
**Action:** Survey Submitted
**Type:** Trigger

**Purpose:**
This is the starting point of the entire AI Audit Automation system. It fires immediately when a client successfully submits the Deep Onboarding Form.

**Configuration Notes:**
* Acts as the catalyst for both internal GHL pipeline movements and the external n8n AI processing pipeline.

---

#### Step 1 — Webhook (n8n Handoff)

**App:** GoHighLevel
**Action:** Webhook
**Type:** Action (Data Handoff)

**Purpose:**
This critical step bridges GHL and n8n. It sends an HTTP POST request containing the full contact and survey payload to the n8n webhook endpoint, triggering the multi-agent AI pipeline to begin generating the Shopping List PDF in the background.

**Configuration Notes:**
* Target URL: `https://selrai.app.n8n.cloud/webhook/ai-audit`
* Method: POST

---

#### Step 2 — Add Contact Tag

**App:** GoHighLevel
**Action:** Add Contact Tag
**Type:** Action

**Purpose:**
Applies the `Shopping list in process` tag to the contact, providing a clear visual indicator in the CRM that the AI pipeline is actively generating their shopping list.

---

#### Step 3 — Update Contact Field

**App:** GoHighLevel
**Action:** Update Contact Field
**Type:** Action

**Purpose:**
Updates the relevant initial status fields on the contact record to reflect that onboarding data has been received and the audit pipeline has been triggered.

---

### Workflow Summary Table

| Step | App | Action | Role |
|---|---|---|---|
| Trigger | GHL | Survey Submitted | Fires when the Deep Onboarding Form is submitted |
| 1 | Webhook | Send Data to n8n | Hands off the full payload to the n8n AI pipeline |
| 2 | GHL | Add Contact Tag | Applies the `Shopping list in process` tag |
| 3 | GHL | Update Contact Field | Updates initial status fields on the contact record |

---

## 2. Initial Shopping List

**Purpose:** Alerts the internal team when n8n successfully generates the initial PDF and syncs it back to GHL.

### Workflow Diagram

```
┌─────────────────────────────────────────────┐
│         🔄 TRIGGER: Contact Changed         │
│   Field: Initial Shopping List              │
│   Fires when n8n populates this field       │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         🔔 Action: Internal Notification    │
│   Step 1: Notify Team                       │
│   Notifies Luke & Harvey                    │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         🏷️ Action: Add Contact Tag          │
│   Step 2: Tag Contact                       │
│   Tag: 'Initial Shopping List updated'      │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         👤 Action: Update Contact Field     │
│   Step 3: Update Status                     │
│   Status: 'Shopping List Generated'         │
└─────────────────────────────────────────────┘
```

### Step-by-Step Node Documentation

#### TRIGGER — Contact Changed

**App:** GoHighLevel
**Action:** Contact Changed
**Type:** Trigger

**Purpose:**
Fires automatically when the n8n pipeline completes and populates the `Initial Shopping List` custom field on the contact record with the PDF URL.

---

#### Step 1 — Internal Notification

**App:** GoHighLevel
**Action:** Send Internal Notification
**Type:** Action

**Purpose:**
Immediately alerts Luke and Harvey that the AI pipeline has finished and the initial Shopping List PDF is ready for review in the contact record.

---

#### Step 2 — Add Contact Tag

**App:** GoHighLevel
**Action:** Add Contact Tag
**Type:** Action

**Purpose:**
Applies the `Initial Shopping List updated` tag to the contact for easy CRM filtering and pipeline visibility.

---

#### Step 3 — Update Contact Field

**App:** GoHighLevel
**Action:** Update Contact Field
**Type:** Action

**Purpose:**
Sets the Audit Status field to `Shopping List Generated` to reflect the current stage in the audit pipeline.

---

### Workflow Summary Table

| Step | App | Action | Role |
|---|---|---|---|
| Trigger | GHL | Contact Changed | Fires when n8n populates the Initial Shopping List field |
| 1 | GHL | Send Internal Notification | Alerts Luke & Harvey that the list is ready for review |
| 2 | GHL | Add Contact Tag | Applies `Initial Shopping List updated` tag |
| 3 | GHL | Update Contact Field | Sets Audit Status to `Shopping List Generated` |

---

## 3. Revised Shopping List

**Purpose:** Alerts the team when the post-discovery revision workflow in n8n is complete and the revised PDF is ready.

### Workflow Diagram

```
┌─────────────────────────────────────────────┐
│         🔄 TRIGGER: Contact Changed         │
│   Field: Post Discovery Call Shopping List  │
│   Fires when n8n populates this field       │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         🔔 Action: Internal Notification    │
│   Step 1: Notify Team                       │
│   Notifies Luke & Harvey                    │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         🏷️ Action: Add Contact Tag          │
│   Step 2: Tag Contact                       │
│   Tag: 'Revised shopping list updated'      │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         👤 Action: Update Contact Field     │
│   Step 3: Update Status                     │
│   Status: 'Discovery Complete'              │
└─────────────────────────────────────────────┘
```

### Step-by-Step Node Documentation

#### TRIGGER — Contact Changed

**App:** GoHighLevel
**Action:** Contact Changed
**Type:** Trigger

**Purpose:**
Fires automatically when the n8n Transcript Revision workflow completes and populates the `Post Discovery Call Shopping List` custom field with the revised PDF URL.

---

#### Step 1 — Internal Notification

**App:** GoHighLevel
**Action:** Send Internal Notification
**Type:** Action

**Purpose:**
Immediately alerts Luke and Harvey that the revised Shopping List PDF is ready for final review before sending to the client.

---

#### Step 2 — Add Contact Tag

**App:** GoHighLevel
**Action:** Add Contact Tag
**Type:** Action

**Purpose:**
Applies the `Revised shopping list updated` tag to the contact for CRM tracking and pipeline visibility.

---

#### Step 3 — Update Contact Field

**App:** GoHighLevel
**Action:** Update Contact Field
**Type:** Action

**Purpose:**
Sets the Audit Status field to `Discovery Complete` to reflect the updated stage in the pipeline.

---

### Workflow Summary Table

| Step | App | Action | Role |
|---|---|---|---|
| Trigger | GHL | Contact Changed | Fires when n8n populates the Post Discovery Call Shopping List field |
| 1 | GHL | Send Internal Notification | Alerts Luke & Harvey that the revised list is ready |
| 2 | GHL | Add Contact Tag | Applies `Revised shopping list updated` tag |
| 3 | GHL | Update Contact Field | Sets Audit Status to `Discovery Complete` |

---

## 4. Send Email to Client

**Purpose:** Automatically sends the generated proposal to the client and runs a follow-up drip sequence based on whether the client opens the email.

### Workflow Diagram

```
┌─────────────────────────────────────────────┐
│         🔄 TRIGGER: Contact Changed         │
│   Field: Post Discovery Call Shopping List  │
│   Fires alongside the internal notification │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         ✉️ Action: Send Email               │
│   Step 1: Send Proposal to Client           │
│   Sends the revised Shopping List PDF       │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         👤 Action: Update Contact Field     │
│   Step 2: Update Status                     │
│   Status: 'Proposal Sent'                   │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         ⏱️ Action: Wait                     │
│   Step 3: Monitor Email Event               │
│   Waits for open event or 24hr timeout      │
└──────────┬──────────────────────────────────┘
           │
     ┌─────┴────────────────────────┐
     │                              │
Email Opened                 Time Out (24hrs)
     │                              │
     ▼                              ▼
┌──────────────────┐    ┌───────────────────────┐
│ 🏷️ Tag:         │    │ 🏷️ Tag:               │
│ 'Email Opened'   │    │ 'Email Not Opened'    │
└──────────────────┘    └───────────┬───────────┘
                                    │
                                    ▼
                         ┌───────────────────────┐
                         │ ✉️ Action: Send Email  │
                         │ Step 4: Follow-up      │
                         │ Sends follow-up email  │
                         └───────────┬───────────┘
                                     │
                                     ▼
                         ┌───────────────────────┐
                         │ ⏱️ Action: Wait        │
                         │ Step 5: Monitor Again  │
                         │ Monitors next event    │
                         └───────────────────────┘
```

### Step-by-Step Node Documentation

#### TRIGGER — Contact Changed

**App:** GoHighLevel
**Action:** Contact Changed
**Type:** Trigger

**Purpose:**
Fires on the same event as the Revised Shopping List workflow — when the `Post Discovery Call Shopping List` field is populated — running this parallel client-facing email flow simultaneously.

---

#### Step 1 — Send Email

**App:** GoHighLevel
**Action:** Send Email
**Type:** Action

**Purpose:**
Dispatches the revised Shopping List PDF proposal to the client. The email contains the proposal link and a clear call-to-action approval button.

---

#### Step 2 — Update Contact Field

**App:** GoHighLevel
**Action:** Update Contact Field
**Type:** Action

**Purpose:**
Sets the Audit Status field to `Proposal Sent` to track the current client stage in the pipeline.

---

#### Step 3 — Wait (Email Event Monitor)

**App:** GoHighLevel
**Action:** Wait
**Type:** Logic / Timer

**Purpose:**
Pauses the workflow and monitors for an email open event. If the client opens the email, the flow branches to the "Email Opened" path. If no open event is detected within 24 hours, the workflow takes the timeout branch.

**Branches:**
* **Email Opened:** Tags contact as `Email Opened` — no further action needed.
* **Time Out (24 hrs):** Tags contact as `Email Not Opened` and triggers the follow-up sequence.

---

#### Step 4 — Send Follow-Up Email (Timeout Branch Only)

**App:** GoHighLevel
**Action:** Send Email
**Type:** Action

**Purpose:**
Sends a follow-up email to the client if they have not opened the original proposal within 24 hours.

---

#### Step 5 — Wait (Second Monitor)

**App:** GoHighLevel
**Action:** Wait
**Type:** Logic / Timer

**Purpose:**
Continues monitoring for client engagement after the follow-up email is sent.

---

### Workflow Summary Table

| Step | App | Action | Role |
|---|---|---|---|
| Trigger | GHL | Contact Changed | Fires when Post Discovery Call Shopping List is populated |
| 1 | GHL | Send Email | Dispatches the PDF proposal to the client |
| 2 | GHL | Update Contact Field | Sets Audit Status to `Proposal Sent` |
| 3 | GHL | Wait | Monitors for email open event or 24hr timeout |
| 4 | GHL | Branch | Tags contact as `Email Opened` or `Email Not Opened` |
| 5 | GHL | Send Email (Fallback) | Sends follow-up email if not opened within 24 hours |
| 6 | GHL | Wait | Continues monitoring after follow-up is sent |

---

## 5. Send Contract & Agreement

**Purpose:** Sends the official contract when the client clicks the approval link inside the proposal email.

### Workflow Diagram

```
┌─────────────────────────────────────────────┐
│         🖱️ TRIGGER: Trigger Link Clicked    │
│   Link: 'Shopping List Approved'            │
│   Client clicks approval link in email      │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         📄 Action: Send Contract            │
│   Step 1: Dispatch Agreement                │
│   Sends the final contract for signature    │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         🏷️ Action: Add Contact Tag          │
│   Step 2: Tag Contact                       │
│   Tag: 'Contract Sent'                      │
└─────────────────────────────────────────────┘
```

### Step-by-Step Node Documentation

#### TRIGGER — Trigger Link Clicked

**App:** GoHighLevel
**Action:** Trigger Link Clicked
**Type:** Trigger

**Purpose:**
Fires when the client clicks the "Shopping List Approved" link embedded in the proposal email. This signals that the client has reviewed and agreed to the shopping list and is ready to proceed.

---

#### Step 1 — Send Contract

**App:** GoHighLevel
**Action:** Send Contract / Document
**Type:** Action

**Purpose:**
Dispatches the official Internal Audit Contract to the client for electronic signature via GHL's Documents & Contracts feature.

---

#### Step 2 — Add Contact Tag

**App:** GoHighLevel
**Action:** Add Contact Tag
**Type:** Action

**Purpose:**
Applies the `Contract Sent` tag to the contact for CRM tracking and pipeline visibility.

---

### Workflow Summary Table

| Step | App | Action | Role |
|---|---|---|---|
| Trigger | GHL | Trigger Link Clicked | Fires when client clicks "Shopping List Approved" |
| 1 | GHL | Send Contract | Dispatches the agreement document for signature |
| 2 | GHL | Add Contact Tag | Applies the `Contract Sent` tag |

---

## 6. Check Contract Status

**Purpose:** Closes the loop on the entire client journey. When the contract is signed, it updates the deal to Closed Won.

### Workflow Diagram

```
┌─────────────────────────────────────────────┐
│         ✍️ TRIGGER: Documents & Contracts   │
│   Template: Internal Audit Contract         │
│   Status: Signed / Accepted                 │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         🏷️ Action: Add Contact Tag          │
│   Step 1: Tag Contact                       │
│   Tag: 'Contract Signed'                    │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         👤 Action: Update Contact Field     │
│   Step 2: Update Status                     │
│   Status: 'Closed Won'                      │
└─────────────────────────────────────────────┘
```

### Step-by-Step Node Documentation

#### TRIGGER — Documents & Contracts

**App:** GoHighLevel
**Action:** Documents & Contracts
**Type:** Trigger

**Purpose:**
Fires automatically when the client signs and accepts the Internal Audit Contract via GHL's e-signature system. No manual action is required from the team.

**Configuration Notes:**
* Template: Internal Audit Contract
* Status filter: Signed / Accepted

---

#### Step 1 — Add Contact Tag

**App:** GoHighLevel
**Action:** Add Contact Tag
**Type:** Action

**Purpose:**
Applies the `Contract Signed` tag to the contact, marking the completion of the sales process in the CRM.

---

#### Step 2 — Update Contact Field

**App:** GoHighLevel
**Action:** Update Contact Field
**Type:** Action

**Purpose:**
Sets the Audit Status field to `Closed Won`, completing the full client journey from form submission to signed agreement.

---

### Workflow Summary Table

| Step | App | Action | Role |
|---|---|---|---|
| Trigger | GHL | Documents & Contracts | Fires when the Internal Audit Contract is signed |
| 1 | GHL | Add Contact Tag | Applies the `Contract Signed` tag |
| 2 | GHL | Update Contact Field | Sets Audit Status to `Closed Won` |

---

## Complete Client Journey — End to End

The six workflows above connect to form a single unbroken pipeline from form submission to closed deal.

```
Client Submits Deep Onboarding Form
             │
             ▼
   GHL: Send Form Data to n8n  ──────────────────────► n8n generates Shopping List PDF
                                                                    │
                                                                    ▼
                                                       GHL: Initial Shopping List
                                                       (Notifies team, tags contact)

             │   (Team runs Discovery Call)
             │
             ▼
   n8n: Transcript Revision Workflow  ────────────────► n8n generates Revised PDF
                                                                    │
                                              ┌─────────────────────┴──────────────────────┐
                                              ▼                                             ▼
                                 GHL: Revised Shopping List                  GHL: Send Email to Client
                                 (Notifies team, tags contact)               (Proposal + follow-up drip)

             │   (Client clicks "Shopping List Approved")
             │
             ▼
   GHL: Send Contract & Agreement
   (Dispatches contract for signature)

             │   (Client signs contract)
             │
             ▼
   GHL: Check Contract Status
   (Tags 'Contract Signed', sets 'Closed Won')
```

---

## Audit Status Field — All Possible Values

| Status Value | Set By | Meaning |
|---|---|---|
| `Pending` | Default | Client has not yet submitted the Deep Onboarding Form |
| `Shopping List Generated` | Workflow 2 | n8n has generated and synced the initial PDF |
| `Discovery Complete` | Workflow 3 | Revised PDF has been generated post-discovery call |
| `Proposal Sent` | Workflow 4 | Proposal email has been dispatched to the client |
| `Closed Won` | Workflow 6 | Contract has been signed — client is confirmed |
| `Closed Lost` | Manual | Deal did not proceed |
