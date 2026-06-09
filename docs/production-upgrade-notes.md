# Production Upgrade Notes

This portfolio workflow intentionally avoids real production integrations. The notes below describe how it should be hardened before real deployment.

## Webhook Security

Recommended upgrades:

- Add webhook authentication.
- Validate signed requests from the source website or form provider.
- Consider IP allowlisting when the source platform has stable egress IPs.
- Reject unsupported HTTP methods.
- Validate `Content-Type: application/json`.
- Add rate limiting at the platform or gateway layer if available.

## Data Validation

Recommended upgrades:

- Add stricter schema validation.
- Normalize phone numbers to a consistent format.
- Add an allowlist for accepted urgency values.
- Reject excessively large request bodies.
- Add spam/bot filtering.
- Add consent and privacy fields if the workflow handles regulated lead data.

## Persistence

The current workflow prepares `lead_log_record` but does not store it.

Production storage options:

- n8n Data Tables
- SmartSuite
- Airtable
- Google Sheets
- CRM records
- SQL database
- NoSQL database

Storage should include:

- submission timestamp
- normalized lead fields
- validation status and errors
- score and tier
- route and route reason
- assigned queue
- execution ID
- error status, if any

## Integration Replacements

Replace placeholder route handlers with real actions:

- Manual review route -> task or queue item.
- Urgent review route -> Slack/Discord/SMS/email notification and escalation.
- Hot lead route -> CRM lead creation and sales assignment.
- Qualified route -> CRM pipeline stage or sales queue.
- Nurture route -> marketing automation or email list enrollment.

Each integration should have clear failure handling and retry behavior.

## Error Handling

Recommended upgrades:

- Add an n8n Error Trigger workflow.
- Send failure notifications to an operations channel.
- Capture failed payload, execution ID, node name, error message, and timestamp.
- Add retry logic for transient failures.
- Add manual replay instructions for failed lead intake events.

## Observability

Recommended upgrades:

- Track success/failure counts.
- Track route distribution.
- Track average lead score.
- Track urgent lead volume.
- Track validation failure reasons.
- Review execution history regularly.

## Privacy and Compliance

Recommended upgrades:

- Redact sensitive data from public logs and screenshots.
- Limit stored fields to business-required data.
- Use n8n credential storage for all secrets.
- Define retention policy for lead data.
- Avoid storing raw payloads if not required.
- Document data handling assumptions.

## Deployment Readiness

Before activation:

- Confirm webhook URL and source form configuration.
- Confirm credentials are scoped and tested.
- Confirm error workflow exists.
- Confirm logging target is reliable.
- Confirm notifications reach the right human owner.
- Run test cases in staging.
- Document rollback steps.

