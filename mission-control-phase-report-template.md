# Mission Control Phase Completion Report Template

Use this reminder after each manually completed n8n build phase.

Before sending the report:

- Inspect the workflow read-only through MCP.
- Confirm the saved nodes match what the user built in the n8n UI.
- Confirm key node settings for the phase.
- Confirm the connection path.
- Mention any expected warning or unfinished dependency.
- End with the next phase readiness line.

Do not say the workflow is complete unless the full workflow has been finished and verified.

## Message Format

```text
Phase [PHASE] completed manually.

Workflow: Client Lead Intake Qualification System
Workflow ID: Ge5w6oBYstyCWWCs
Status: inactive

Current saved nodes:
1. [Node 1 name]
2. [Node 2 name]
3. [Node 3 name]

Connection:
[Node 1 name] -> [Node 2 name] -> [Node 3 name]

[New/changed node name]:
- Mode: [mode, if relevant]
- Language: [language, if relevant]
- [Key behavior confirmed]
- [Key output confirmed]
- [Other portfolio-relevant detail]

Known expected issue:
[Mention any expected warning, such as the missing Respond to Webhook node, if still relevant.]

Ready for Phase [NEXT PHASE]: [short description].
```

## Phase 1B Example

```text
Phase 1B completed manually.

Workflow: Client Lead Intake Qualification System
Workflow ID: Ge5w6oBYstyCWWCs
Status: inactive

Current saved nodes:
1. Webhook - Receive Lead Intake
2. Code - Normalize and Validate Lead
3. Code - Score Lead

Connection:
Webhook - Receive Lead Intake -> Code - Normalize and Validate Lead -> Code - Score Lead

Code - Score Lead:
- Mode: Run Once for Each Item
- Language: JavaScript
- Calculates lead_score
- Calculates lead_tier
- Outputs scoring_breakdown

Known expected issue:
The workflow may still warn that a Respond to Webhook node is required. This is expected until the final response node is added.

Ready for Phase 1C: routing.
```

