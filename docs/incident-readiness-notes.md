# Incident Readiness Notes

This document describes how the Client Lead Intake Qualification System should be operated and diagnosed if it is promoted from portfolio workflow to production workflow.

## Common Failure Modes

Likely issues:

- Webhook receives malformed JSON.
- Required fields are missing.
- Email format validation fails.
- Source form sends changed field names.
- Routing strings no longer match Switch cases.
- A downstream CRM or notification integration fails.
- Logging destination is unavailable.
- Respond to Webhook expression is misconfigured.
- Pinned test data is accidentally used during manual testing.

## First Checks

When an incident is reported:

1. Confirm whether the workflow is active.
2. Confirm whether the request hit the production or test webhook URL.
3. Inspect the latest execution status.
4. Identify the last executed node.
5. Check the node error message.
6. Compare the incoming payload against the expected schema.
7. Confirm route output and assigned queue.
8. Confirm response body was returned to the caller.

## Useful Execution Fields

Fields to inspect during troubleshooting:

- `raw_payload`
- `lead`
- `validation_status`
- `validation_errors`
- `missing_required_fields`
- `lead_score`
- `lead_tier`
- `scoring_breakdown`
- `matched_service_keywords`
- `route`
- `route_reason`
- `response_status`
- `assigned_queue`
- `lead_log_record`
- `logging_status`

## Expected Recovery Actions

Malformed payload:

- Confirm the source form still sends the expected JSON shape.
- Update normalization logic only after confirming the new schema is intentional.

Validation failures:

- Check `validation_errors`.
- Confirm required field mapping from the form.
- Route to manual review if the lead might still be valuable.

Routing failures:

- Confirm `route` exactly matches one Switch output label.
- Check case sensitivity and spelling.
- Add a fallback path if production risk requires it.

Logging failures:

- Keep the webhook response separate from logging failures when possible.
- Send failed log records to a retry queue or error workflow.
- Preserve execution ID for replay.

Notification or CRM failures:

- Retry transient errors.
- Notify the owner if retries fail.
- Avoid dropping the lead silently.

## Alerting Recommendations

Recommended alerts:

- Any execution failure.
- Any urgent lead.
- Repeated invalid payloads from the same source.
- Logging destination unavailable.
- CRM creation failure.
- Notification delivery failure.

## Incident Notes Template

```text
Incident title:
Date/time:
Workflow:
Execution ID:
Trigger source:
Affected route:
Last successful node:
Failed node:
Error message:
Payload summary:
Business impact:
Immediate action taken:
Follow-up fix:
Owner:
Status:
```

## Portfolio Relevance

Including incident readiness notes demonstrates that the workflow was designed beyond the happy path. It shows attention to troubleshooting, operational ownership, handoff quality, and production support discipline.

