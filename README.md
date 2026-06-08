# Client Lead Intake Qualification System

## Overview
n8n portfolio workflow for receiving, validating, scoring, routing, logging, and responding to website lead submissions.

## Business Problem
Website leads often arrive with inconsistent quality and urgency. This workflow demonstrates how to triage leads automatically while preserving review paths for incomplete or urgent submissions.

## Workflow Architecture
Webhook -> Normalize/Validate -> Score -> Determine Route -> Switch -> Placeholder Handler -> Prepare Log -> Respond to Webhook

## Tech Stack
- n8n
- Webhook API
- JavaScript Code nodes
- Switch routing
- Edit Fields placeholder handlers
- JSON response handling

## Input Payload Schema
Required: full_name, email, service_need, message.
Optional: phone, company, budget, urgency, source.

## Validation, Scoring, and Routing
Explain the validation rules, scoring weights, lead tiers, and routing priority.

## Placeholder Integration Strategy
Placeholder nodes represent future CRM, notification, queue, and nurture integrations without using real credentials.

## Logging Behavior
The workflow creates lead_log_record for future storage in Data Tables, Sheets, Airtable, SmartSuite, CRM, or a database.

## Test Results
Hot, urgent, invalid, and nurture test cases all passed using synthetic data.

## Screenshots
List the seven screenshot files.

## Known Limitations
No real integrations, no authentication yet, inactive workflow, synthetic data only.

## Production Upgrade Path
Add authentication, real storage, CRM/notification integrations, error workflow, retries, monitoring, and data redaction.

## Skills Demonstrated
n8n workflow design, webhooks/APIs, JavaScript, validation, scoring logic, routing, troubleshooting, documentation, and portfolio-ready testing.