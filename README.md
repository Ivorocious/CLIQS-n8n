# Client Lead Intake Qualification System

Portfolio n8n workflow for receiving, validating, scoring, routing, logging, and responding to website lead submissions.

This project was built as a job-application artifact to demonstrate practical n8n workflow design, webhook/API handling, JavaScript business logic, conditional routing, troubleshooting, structured documentation, and production-aware thinking without exposing real credentials or client data.

## Business Problem

Website lead forms often produce inconsistent submissions. Some leads are urgent, some are high-value, some are incomplete, and some need nurturing rather than immediate sales action.

This workflow turns raw website form submissions into a structured qualification result. It validates the payload, scores the lead, routes the lead to the right queue, prepares a log record, and returns a clean JSON response to the caller.

## Workflow Architecture

```text
Webhook - Receive Lead Intake
-> Code - Normalize and Validate Lead
-> Code - Score Lead
-> Code - Determine Lead Route
-> Switch - Route Lead
   -> Placeholder - Manual Review Queue
   -> Placeholder - Urgent Human Review Alert
   -> Placeholder - Notify Sales for Hot Lead
   -> Placeholder - Add to Qualified Lead Queue
   -> Placeholder - Add to Nurture Queue
-> Code - Prepare Lead Intake Log
-> Respond - Return Lead Qualification Result
```

The five route branches converge into one shared logging-preparation node before returning the webhook response.

## Package Contents

```text
client-lead-intake-qualification-system.workflow.json
screenshots/
sample-payloads/
docs/
```

The workflow export is included for review/import. Screenshots show the workflow canvas, test executions, routing behavior, validation errors, and final webhook response. Sample payloads provide reproducible synthetic test cases.

## Sample Payloads

Synthetic request payloads are included for reproducible testing:

| File | Scenario |
| --- | --- |
| `sample-payloads/hot-lead.json` | High-value lead that routes to immediate sales follow-up |
| `sample-payloads/urgent-lead.json` | Urgent lead that routes to human review even when the score is hot |
| `sample-payloads/invalid-lead.json` | Invalid submission with missing required fields and bad email format |
| `sample-payloads/nurture-lead.json` | Lower-priority valid lead that routes to nurture |

## Tech Stack

- n8n
- Webhook trigger
- Respond to Webhook node
- JavaScript Code nodes
- Switch node routing
- Edit Fields placeholder handlers
- JSON payloads and API-style responses
- Synthetic test data

## Input Payload Schema

Expected POST payload fields:

| Field | Required | Description |
| --- | --- | --- |
| `full_name` | Yes | Lead name |
| `email` | Yes | Lead email address |
| `phone` | No | Phone number |
| `company` | No | Company or organization |
| `service_need` | Yes | Requested service or problem statement |
| `budget` | No | Budget as a number or numeric string |
| `urgency` | No | Lead urgency, such as `low`, `high`, or `urgent` |
| `message` | Yes | Lead message |
| `source` | No | Source form or channel |

Webhook path:

```text
lead-intake
```

The workflow is designed for synthetic/test data in this portfolio package.

## Validation Rules

The normalization and validation step:

- Reads the webhook request body.
- Trims string fields.
- Lowercases email.
- Normalizes urgency.
- Converts budget to a number when possible.
- Requires `full_name`, `email`, `service_need`, and `message`.
- Validates email format.
- Outputs `validation_errors`, `validation_status`, `is_valid`, and `received_at`.

The normalized lead object is stored under:

```text
lead
```

Invalid or incomplete leads route to manual review.

## Scoring Rules

| Rule | Points |
| --- | ---: |
| Valid email present | +10 |
| Phone present | +10 |
| Company present | +10 |
| Budget >= 5000 | +30 |
| Budget 1000 to 4999 | +20 |
| Budget below 1000 | +5 |
| Urgency high | +15 |
| Urgency urgent | +25 |
| Service need contains `automation`, `ai`, `workflow`, `integration`, or `api` | +20 |
| Message length >= 80 characters | +10 |

Lead tiers:

| Tier | Rule |
| --- | --- |
| `hot` | Valid and score >= 70 |
| `qualified` | Valid and score >= 45 |
| `nurture` | Valid and score >= 25 |
| `incomplete` | Invalid or score below 25 |

## Routing Rules

Routing priority:

1. Invalid or incomplete leads -> `Manual Review`
2. Urgent leads -> `Urgent Human Review`
3. Hot leads -> `Immediate Sales Follow-Up`
4. Qualified leads -> `Qualified Lead Queue`
5. Nurture leads -> `Nurture Queue`
6. Fallback -> `Manual Review`

The urgent routing rule intentionally overrides lead tier. A lead can score as `hot` and still route to `Urgent Human Review` if the urgency field is `urgent`.

## Placeholder Integration Strategy

This project intentionally uses placeholder route handlers instead of real production integrations.

Each placeholder preserves the incoming lead data and adds:

- `action_type`
- `action_status`
- `action_description`
- `assigned_queue`

These placeholders represent future integrations such as:

- CRM task creation
- Slack or Discord notification
- Sales pipeline insertion
- Nurture campaign enrollment
- Manual review queue creation

This keeps the workflow safe to publish as a portfolio artifact while still showing how production integrations would attach.

## Logging Behavior

All route branches converge into `Code - Prepare Lead Intake Log`.

That node creates a structured `lead_log_record` containing:

- normalized lead fields
- validation status and errors
- score and tier
- scoring breakdown
- route and route reason
- action metadata
- received and logged timestamps

The log record is prepared for future insertion into:

- n8n Data Tables
- Google Sheets
- Airtable
- SmartSuite
- CRM
- SQL/NoSQL database

No real storage integration is used in this portfolio version.

## Webhook Response

The final node returns a JSON response through `Respond - Return Lead Qualification Result`.

Response fields:

```json
{
  "status": "success",
  "validation_status": "valid",
  "lead_score": 105,
  "lead_tier": "hot",
  "route": "Immediate Sales Follow-Up",
  "route_reason": "Lead score qualifies as hot and should receive immediate sales follow-up.",
  "assigned_queue": "Immediate Sales Follow-Up",
  "logging_status": "prepared",
  "message": "Lead received, qualified, routed, and prepared for logging."
}
```

Leads routed to manual or urgent human review return:

```json
{
  "status": "needs_review",
  "message": "Lead received and queued for human review."
}
```

## Test Cases and Results

Manual synthetic testing was completed through the n8n test webhook URL. The workflow remained inactive and no real credentials were used.

| Test case | Execution ID | Expected route | Result |
| --- | ---: | --- | --- |
| Hot lead | 9 | Immediate Sales Follow-Up | Passed |
| Urgent lead | 10 | Urgent Human Review | Passed |
| Invalid lead | 11 | Manual Review | Passed |
| Nurture lead | 12 | Nurture Queue | Passed |

Observed results:

- Hot lead scored `105`, tiered as `hot`, and routed to `Immediate Sales Follow-Up`.
- Urgent lead scored `105`, tiered as `hot`, and correctly routed to `Urgent Human Review` because urgent priority overrides hot scoring.
- Invalid lead produced validation errors, tiered as `incomplete`, and routed to `Manual Review`.
- Nurture lead scored `25`, tiered as `nurture`, and routed to `Nurture Queue`.

Detailed testing notes are available in [docs/testing-notes.md](docs/testing-notes.md).

## Screenshots

| File | Purpose |
| --- | --- |
| `screenshots/01-full-workflow-canvas.png` | Full workflow architecture |
| `screenshots/02-hot-lead-score-and-route.png` | Hot lead scoring/routing proof |
| `screenshots/03-urgent-priority-routing.png` | Urgent priority routing proof |
| `screenshots/04-invalid-lead-validation-errors.png` | Validation error proof |
| `screenshots/05-nurture-lead-routing.png` | Nurture route proof |
| `screenshots/06-final-webhook-response-json.png` | Final webhook JSON response proof |
| `screenshots/07-workflow-documentation-sticky-notes-top.png` | Top-row workflow documentation notes |
| `screenshots/08-workflow-documentation-sticky-notes-bottom.png` | Bottom-row workflow documentation notes |

## Known Limitations

- The workflow is inactive in the portfolio export.
- No real CRM, notification, queue, or database integrations are connected.
- Webhook authentication is not enabled in the demo version.
- Test data is synthetic and intentionally non-sensitive.
- Placeholder nodes document future integration points but do not perform external actions.
- This workflow demonstrates lead intake automation, not the separate AI voice-agent workflow planned for another project.

## Production Upgrade Path

Recommended production upgrades:

- Add webhook authentication, signed request validation, or IP allowlisting.
- Store log records in n8n Data Tables, SmartSuite, Airtable, CRM, or a database.
- Replace placeholder nodes with real CRM, notification, and queue integrations.
- Add an Error Trigger workflow for failed executions.
- Add retry logic and failure notifications.
- Add execution monitoring and incident documentation.
- Redact or secure sensitive fields.
- Use dedicated credentials managed through n8n credential storage.

More detail is available in:

- [docs/production-upgrade-notes.md](docs/production-upgrade-notes.md)
- [docs/incident-readiness-notes.md](docs/incident-readiness-notes.md)

## Skills Demonstrated

- n8n workflow architecture
- Webhook/API intake design
- JavaScript data validation and scoring
- Conditional routing and route priority handling
- Branch convergence
- JSON response design
- Placeholder integration planning
- Logging/audit-record preparation
- Manual synthetic testing
- Troubleshooting pinned data and response expression issues
- Portfolio-grade documentation and evidence packaging

## Safety Notes

This repository should not include real client data, secrets, production webhook URLs, or credential values. Screenshots and payloads should use synthetic data only.
