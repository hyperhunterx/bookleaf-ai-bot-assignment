# BookLeaf AI Customer Query Bot - Technical Assignment

This repository contains the n8n workflow and supporting documentation for the AI Automation Specialist technical assignment. The project is a live, automated, multi-path query responder designed to handle author inquiries for BookLeaf Publishing.

---

## Tech Stack

*   **Automation Engine:** n8n (Self-Hosted)
*   **Language Model (LLM):** Google Gemini
*   **Database:** Supabase (PostgreSQL)
*   **Testing Interface:** Postman

---

## Features

*   **Natural Language Understanding:** Interprets user queries to classify intent and determine the required data source.
*   **Dual-Path Logic:**
    *   **Database Path:** Fetches specific author and book data (e.g., live dates, royalty status) from a Supabase database.
    *   **Knowledge Base Path:** Answers general policy and process questions using an embedded knowledge base.
*   **Confidence-Based Escalation:** If the AI's confidence in understanding the query is below 80%, it provides a fallback message and escalates to a human agent.
*   **Comprehensive Logging:** Every query, its classified intent, the AI's confidence, and the final response are logged to a `query_logs` table in Supabase for monitoring and analysis.
*   **Multi-Channel Ready:** Built with a webhook trigger, making it easy to integrate with Email, WhatsApp, Instagram DMs, etc.

---

## Live Demo Endpoint

The workflow is active and can be tested directly using the following production URL:

**URL:** `http://129.159.18.115:49169/webhook/aca61cab-b12e-4145-94b9-096722cc7520`

**Method:** `POST`

**Body (raw, JSON):**
```json
{
  "email": "sara.j@example.com",
  "query": "Is my book live yet?"
}
Example curl command:
code
Bash
curl --location 'http://129.159.18.115:49169/webhook/aca61cab-b12e-4145-94b9-096722cc7520' \
--header 'Content-Type: application/json' \
--data '{
    "email": "sara.j@example.com",
    "query": "Is my book live yet?"
}'
Project Files
workflow.json: The complete, importable n8n workflow.
data/authors.csv: The sample data used in the Supabase authors table.
data/query_logs_sample.csv: A sample of the data being captured in the query_logs table.
design/IdentityUnificationFlowchart.md: The flowchart and explanation for the intermediate task.
Setup Instructions (For Replication)
Supabase:
Create a new Supabase project.
Create an authors table and a query_logs table. You can use the provided CSV files as a schema reference.
Disable Row Level Security (RLS) on both tables for this MVP.
Obtain your Project URL and anon public key.
n8n:
Import the workflow.json file.
Create and assign your "Google Gemini API" and "Supabase" credentials.
Activate the workflow.```
