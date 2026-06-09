# Testing Notes

This document summarizes the safe synthetic testing performed for the Client Lead Intake Qualification System.

## Testing Approach

The workflow was tested manually through n8n's test webhook flow while the workflow remained inactive.

No real client data, production credentials, or external integrations were used.

Manual test sequence:

1. Open the workflow in n8n.
2. Click `Execute workflow` to make the test webhook listen for one request.
3. Send one synthetic POST request to the test webhook URL.
4. Inspect node outputs from left to right.
5. Repeat for each test payload.

## Test Webhook Path

The webhook node uses:

```text
POST /webhook-test/lead-intake
```

The production webhook path exists only when the workflow is active, which this portfolio workflow is not.

## Test Payloads

Synthetic payload files are stored in:

```text
sample-payloads/
```

Files:

- `hot-lead.json`
- `urgent-lead.json`
- `invalid-lead.json`
- `nurture-lead.json`

## Execution Results

| Scenario | Execution ID | Key expected behavior | Result |
| --- | ---: | --- | --- |
| Hot lead | 9 | Score as hot and route to sales follow-up | Passed |
| Urgent lead | 10 | Route to urgent human review even though score is hot | Passed |
| Invalid lead | 11 | Fail validation and route to manual review | Passed |
| Nurture lead | 12 | Score as nurture and route to nurture queue | Passed |

## Hot Lead Result

Observed:

- `validation_status`: `valid`
- `lead_score`: `105`
- `lead_tier`: `hot`
- `route`: `Immediate Sales Follow-Up`
- `assigned_queue`: `Immediate Sales Follow-Up`
- `response_status`: `success`
- `logging_status`: `prepared`

## Urgent Lead Result

Observed:

- `validation_status`: `valid`
- `lead_score`: `105`
- `lead_tier`: `hot`
- `route`: `Urgent Human Review`
- `assigned_queue`: `Urgent Human Review`
- `response_status`: `needs_review`
- `logging_status`: `prepared`

This confirms the routing priority rule: urgent leads override normal hot-lead routing.

## Invalid Lead Result

Observed:

- `validation_status`: `invalid`
- `validation_errors`: missing required fields and invalid email format
- `lead_tier`: `incomplete`
- `route`: `Manual Review`
- `assigned_queue`: `Manual Review Queue`
- `response_status`: `needs_review`
- `logging_status`: `prepared`

## Nurture Lead Result

Observed:

- `validation_status`: `valid`
- `lead_score`: `25`
- `lead_tier`: `nurture`
- `route`: `Nurture Queue`
- `assigned_queue`: `Nurture Queue`
- `response_status`: `success`
- `logging_status`: `prepared`

## Node-Level Checks

During testing, these node outputs were checked:

- `Webhook - Receive Lead Intake`: request body arrived as expected.
- `Code - Normalize and Validate Lead`: lead object, validation status, and validation errors were correct.
- `Code - Score Lead`: lead score, lead tier, and scoring breakdown were correct.
- `Code - Determine Lead Route`: route, route reason, and response status were correct.
- `Switch - Route Lead`: only the expected route output received the item.
- Placeholder route node: `action_type`, `action_status`, `action_description`, and `assigned_queue` were added.
- `Code - Prepare Lead Intake Log`: `lead_log_record` and logging fields were prepared.
- `Respond - Return Lead Qualification Result`: final response data was available for the webhook response.

## Issues Watched For

- Pinned webhook data causing repeated execution of the same payload.
- Markdown `mailto` email strings causing validation failure.
- Route text mismatch between Code and Switch nodes.
- Respond to Webhook body saved as literal text instead of an expression.
- `lead_score` being returned as a string instead of a number.

## Evidence

Execution proof is captured in the `screenshots/` folder. The workflow export and test payloads are included so a reviewer can inspect and reproduce the test cases.

