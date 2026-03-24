# Selr AI — AI Audit Automation System

This documentation covers the complete end-to-end automated pipeline for generating priced **Automation Shopping Lists** for Selr AI audit clients.

## Overview

The system automates the manual analysis of client onboarding data. It ingests form responses from GoHighLevel, processes them through a multi-agent AI pipeline, and outputs a professional PDF proposal within minutes of form submission. The high-level flow is:

1. **Trigger:** Client submits the Deep Onboarding Form in GoHighLevel (GHL).
2. **Orchestration:** n8n fetches the data and routes it through a 3-agent AI pipeline.
3. **Intelligence:** AI Agents identify opportunities, assess complexity, and apply pricing based on Google Sheets reference data.
4. **Budget Enforcement:** A custom code node hard-validates all pricing math and defers any over-budget items automatically.
5. **Delivery:** A professional PDF is generated via PDF.co and synced back to the GHL contact record.
6. **Revision:** Discovery call transcripts can be submitted via an internal form to automatically amend the proposal.

---

## Workflows

The system is composed of interconnected workflows. Detailed documentation for each component is linked below.

### 1. GHL Audit Trigger Workflow
The entry point that captures form submissions, updates the CRM, notifies the client and team, and fires the webhook handoff to n8n.
* [GHL Audit Trigger Documentation](./GHL_Audit_Trigger_Documentation.md)

### 2. n8n Initial Generation Pipeline
The core AI engine that processes form data through 3 sequential agents and generates the initial Shopping List PDF.
* [Initial Generation Documentation](./Initial_Generation_Documentation.md)

### 3. n8n Transcript Revision Workflow
The secondary flow that amends existing proposals based on discovery call insights — without starting from scratch.
* [Transcript Revision Documentation](./Transcript_Revision_Documentation.md)

### 4. Reference Data (Google Sheets)
The dynamic pricing database for tiers, adjustment factors, automation patterns, and tech stack integrations.
* [Reference Data Documentation](./Reference_Data_Documentation.md)

### 5. PDF Output Structure
The structure and sections of the generated client-facing PDF document.
* [PDF Structure Documentation](./PDF_Structure_Documentation.md)

---

## System Requirements

| Requirement | Value |
|---|---|
| Shopping list generation time | < 5 minutes from form submission |
| PDF generation time | < 30 seconds |
| Concurrent submissions supported | Up to 5 |
| Target uptime (business hours AEST) | 99% |
| API retry attempts on failure | 3 |
| Monthly audit capacity (no modification needed) | 50+ |

---

## Tech Stack

| Component | Tool |
|---|---|
| CRM & Trigger | GoHighLevel |
| Workflow Orchestration | n8n (hosted at selrai.app.n8n.cloud) |
| AI Agents | OpenAI GPT-5.2 |
| Reference Data | Google Sheets |
| PDF Generation | PDF.co (HTML to PDF) |
| Data Storage | GHL Custom Fields |

---

## Version History

| Version | Date | Author | Notes |
|---|---|---|---|
| 1.0 | 2024-12-23 | Harvey Shaw | Initial release — approved for MVP development |
