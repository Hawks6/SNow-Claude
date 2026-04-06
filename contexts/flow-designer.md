# Context Module: Flow Designer & IntegrationHub

> **Module:** `flow-designer.md`
> **Load when:** Creating Flows, Subflows, Actions, or integrations via IntegrationHub
> **Depends on:** `_base.md` (auto-loaded)

---

## 1. Flow Designer Components

| Component | Purpose | Trigger |
|-----------|---------|---------|
| **Flow** | End-to-end automation | Record, schedule, application, or inbound email trigger |
| **Subflow** | Reusable logic block | Called from Flows or scripts |
| **Action** | Single operation | Called from Flows/Subflows |
| **Spoke** | Integration package | Groups actions for a specific integration |

## 2. Flow Design Patterns

### Keep Flows Under 25 Actions
Break complex flows into subflows for readability and reuse.

### Use Subflows For:
- Logic reused across multiple flows
- Complex approval sequences
- Data transformation pipelines
- Integration call + error handling bundles

### Input/Output Design
```
Subflow: Route Approval
├── Inputs
│   ├── record (Reference) — the record needing approval
│   └── approval_type (String) — 'standard' | 'emergency'
└── Outputs
    ├── approved (Boolean)
    └── approver_name (String)
```

**Rule:** Pass **record references** not individual fields — more flexible if data needs change.

## 3. Action Design (IntegrationHub)

### Single Integration Call Per Action
```
Action: Create JIRA Issue
├── Input: title, description, project_key, priority
├── Step 1: Build request payload (Script)
├── Step 2: REST Step (single HTTP call)
├── Step 3: Parse response (Script)
└── Output: jira_key, status, error_message
```

### Connection & Credential Aliases
```
NEVER hardcode endpoints or credentials in actions:
  ✅ Use Connection Alias → maps to different endpoints per environment
  ✅ Use Credential Alias → maps to different credentials per environment
  ❌ Never put URLs or passwords in script steps
```

### Error Handling
```
Every integration action should:
  1. Check HTTP status code
  2. Parse error response body
  3. Set meaningful error output
  4. Log the failure with gs.error()
  5. Use Flow error handlers for retry logic
```

## 4. Triggering Flows from Scripts

```javascript
// Trigger a flow
sn_fd.FlowAPI.getRunner()
    .flow('x_acme_app.my_flow_name')
    .inBackground()
    .withInputs({ 'record': current })
    .run();

// Trigger a subflow
sn_fd.FlowAPI.getRunner()
    .subflow('x_acme_app.my_subflow_name')
    .inForeground()
    .withInputs({ 'record_sys_id': current.getUniqueValue() })
    .run();
```

## 5. Best Practices

| Practice | Why |
|----------|-----|
| Minimize inline scripts | Maintainability; use Script Includes |
| Use Flow annotations | Document complex branching |
| Test subflows independently | Isolate failures |
| Use Connection Aliases | Environment portability |
| Set error handlers | Graceful failure |
| Name flows descriptively | `<Scope> - <Trigger> <Description>` |
| Version your spokes | Backward compatibility |

---

*Context module for Flow Designer & IntegrationHub.*
