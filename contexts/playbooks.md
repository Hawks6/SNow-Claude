# Context Module: Process Automation Designer (PAD) & Playbooks

> **Module:** `playbooks.md`
> **Load when:** Designing or building Playbooks, Process Automation Designer processes, or workspace-embedded guided experiences
> **Depends on:** `_base.md`, `flow-designer.md` (recommended)

---

## 1. Core Architecture: PAD vs. Playbook Experience

Understanding the two-layer model is critical:

| Layer | Tool | Role |
|-------|------|------|
| **Design-time** | Process Automation Designer (PAD) | Author the process: triggers, lanes, activities, branching logic |
| **Runtime** | Playbook Experience (Workspace) | Agents see a step-by-step guided UI derived from the process definition |

> **Mental Model:** PAD is the blueprint; the Playbook is the rendered experience agents live in.

PAD lives inside **Workflow Studio** (Washington DC+), the unified hub alongside Flow Designer, Subflows, Actions, and Decision Tables.

---

## 2. Key Concepts & Taxonomy

### 2.1 Process Definition
The top-level container. Equivalent to a Flow in Flow Designer, but for multi-team, multi-step business processes.

| Property | Description |
|----------|-------------|
| **Name** | `<Scope> - <Noun> Process` e.g., `S2P - Supplier Onboarding Process` |
| **Application Scope** | Always create in a scoped app (never Global) |
| **Table** | The driving record table (e.g., `sn_ss_task`, `sn_hr_core_case`) |
| **Version** | PAD supports versioning — always use version control via source control integration |

### 2.2 Triggers
Determines when a process execution (playbook instance) is created.

| Trigger Type | When to Use |
|--------------|-------------|
| **Record-based** | Process starts when a record matching criteria is inserted or updated |
| **Application-based** | Fired explicitly from a Subflow or Script using the PAD API |
| **Manual** | Agent manually initiates the process from the workspace |

> **Washington DC Note:** Trigger license requirement removed — you can add multiple triggers per process without additional licensing.

```javascript
// Programmatically start a process from a Script Include or Business Rule
var processAPI = new sn_pad.ProcessAPI();
processAPI.startProcess({
    processName: 'x_vendor_app.supplier_onboarding_process',
    tableName: 'sn_ss_task',
    recordSysId: current.getUniqueValue()
});
```

### 2.3 Lanes (Stages)
Lanes organize the process into logical phases. They appear as progress stages in the Playbook UI.

| Lane Property | Guidance |
|---------------|----------|
| **Name** | Use business-language names visible to agents: `Intake`, `Validation`, `Fulfillment`, `Closure` |
| **Type** | Sequential (default) or Parallel |
| **Condition** | Optional — skip a lane based on record data (e.g., skip `Legal Review` lane if contract value < $10,000) |
| **Entry Criteria** | Define when the lane becomes active |
| **Exit Criteria** | Define when the lane is considered complete |

**Parallel Lanes Example (Source-to-Pay):**
```
[Intake Lane] → [Legal Review Lane] ──┬──→ [Approval Lane] → [Closure Lane]
                [Finance Review Lane] ──┘
```

### 2.4 Activities
The building blocks within lanes. Each activity maps to a Flow, Subflow, or Action.

| Activity Type | Purpose |
|---------------|---------|
| **Automated** | Executes a Subflow/Action without agent interaction |
| **Manual** | Requires the agent to complete a task card in the Playbook UI |
| **Approval** | Dedicated approval step, integrates with Approvals table |
| **Instructions** | Displays guidance text/links to the agent |
| **Ad-hoc** | Optional activities agents can add as exceptions |

**Activity Configuration:**
```
Activity: Validate Supplier Documents
├── Type: Manual
├── Subflow: x_s2p_app.validate_supplier_docs_subflow (auto-runs on entry)
├── Inputs:
│   ├── supplier_record (Reference → sn_ss_task)
│   └── required_docs_list (String — from System Property)
├── Outputs:
│   ├── docs_validated (Boolean)
│   └── missing_docs (String)
└── Completion Condition: docs_validated = true
```

### 2.5 Activity Definitions
Reusable activity templates stored in the Process Activity Definitions table (`sn_pad_activity_def`). Think of them as Subflow blueprints for PAD.

> **Best Practice:** Always create Activity Definitions in a scoped app. This enables reuse across multiple processes without copy-pasting logic.

---

## 3. Now Experience Workspace Integration

Playbooks surface inside **Configurable Workspaces** built with UI Builder. This is where the agent-facing experience lives.

### 3.1 How Rendering Works
When a record page loads in a workspace, the Playbook component checks for active process executions linked to that record. If found, it renders the Playbook panel.

```
Record Page (UI Builder) 
  └── Playbook Tab / Panel
        └── pb-renderer component
              └── Playbook Data Controller (fetches active execution)
                    └── PAD Process Execution (backend)
```

### 3.2 UI Builder: Adding the Playbook Component

Navigate to **UI Builder → [Your Workspace] → [Record Page]** and:

1. Add a new **Tab** or **Column** container to the record page layout.
2. Search the component library for **Playbook** — use the `Playbook` layout preset for the full experience or `pb-renderer` for embedded use.
3. Connect the **Playbook data resource** on the page — it reads `tableName` and `recordSysId` from page properties to auto-fetch the correct execution.
4. Bind the component's `execution-id` property to the data resource output.

**Key UI Builder Properties:**
| Property | Value |
|----------|-------|
| `table` | Page prop: `{{state.table}}` |
| `record-id` | Page prop: `{{state.recordId}}` |
| `show-activity-log` | `true` (recommended) |
| `collapsible` | `true` (for sidebar embedding) |

### 3.3 Source-to-Pay (S2P) Workspace Patterns

The S2P Workspace is a flagship Configurable Workspace with OOTB Playbooks. When customizing or extending it:

**Required Plugins:**
- `Process Automation Designer` (com.sn_process_automation)
- `Playbook Experience` (com.sn_playbook)
- `Sourcing and Procurement Operations` (com.sn_spo) — for OOTB S2P Playbooks

**OOTB Process Definitions to Reference (do not modify directly):**
| OOTB Process | Table | Scope |
|--------------|-------|-------|
| Supplier Onboarding | `sn_ss_task` | `sn_spo` |
| Purchase Requisition | `sn_spo_req` | `sn_spo` |
| Contract Creation | `ast_contract` | `sn_spo` |

> **⚠️ CRITICAL:** Never modify OOTB process definitions directly. Always **fork** them into your scoped app (`sn_padRecord.copyProcess()`) or create a brand-new process that extends the same table.

**Extending S2P — Correct Pattern:**
```javascript
// In a Script Include within your scoped app:
// 1. Copy the OOTB process to your scope via PAD UI (Actions → Copy)
// 2. Rename with your prefix: x_yourco_s2p.enhanced_supplier_onboarding
// 3. Modify only your copy
// 4. Deactivate the OOTB version using Process Priority rules
```

**Custom Lane for S2P Compliance Example:**
```
S2P - Supplier Onboarding Process (x_yourco_s2p)
  ├── Lane 1: Intake          [Sequential]
  ├── Lane 2: Document Review [Sequential]
  ├── Lane 3: Compliance Check [NEW - Custom Lane, runs parallel to Legal]
  │     └── Activity: Sanctions Screening (Automated → IntegrationHub Spoke)
  ├── Lane 4: Legal Review    [Sequential]  
  └── Lane 5: Closure         [Sequential]
```

### 3.4 Multi-Workspace Playbook Patterns

If the same process needs to surface in multiple workspaces (e.g., both S2P Workspace and CSM Workspace):

- Use a **single Process Definition** — the Playbook rendering is workspace-agnostic.
- Add the Playbook component to each workspace's record page independently.
- Use **workspace-specific Activity Definition display conditions** to control which activities are shown per workspace context.

---

## 4. Scripting & API Reference

### 4.1 Process API (Server-side)

```javascript
// Start a process programmatically
var sn_pad = sn_pad || {};
var processRunner = new sn_pad.ProcessAPI();
var response = processRunner.startProcess({
    processName: 'x_yourco_s2p.supplier_onboarding_process',
    tableName: 'sn_ss_task',
    recordSysId: grTask.getUniqueValue()
});

if (response.executionId) {
    gs.info('[SupplierOnboardingUtils] Process started. Execution: ' + response.executionId);
} else {
    gs.error('[SupplierOnboardingUtils] Failed to start process: ' + JSON.stringify(response));
}
```

### 4.2 Querying Process Executions

Key tables for querying PAD runtime data:

| Table | Contains |
|-------|---------|
| `sn_pad_process_execution` | Active and historical process runs |
| `sn_pad_lane_execution` | Lane-level execution state |
| `sn_pad_activity_execution` | Activity-level execution, status, outputs |
| `sn_pad_activity_def` | Reusable activity definitions |

```javascript
// Check if a process is currently active for a record
var grExec = new GlideRecord('sn_pad_process_execution');
grExec.addQuery('record', recordSysId);
grExec.addQuery('state', 'in_progress');
grExec.setLimit(1);
grExec.query();

if (grExec.next()) {
    gs.info('[PlaybookUtils] Active execution found: ' + grExec.getUniqueValue());
}
```

### 4.3 Completing Activities from Server-Side

```javascript
// Force-complete an activity (use sparingly — prefer organic completion)
var actExec = new GlideRecord('sn_pad_activity_execution');
if (actExec.get(activityExecutionSysId)) {
    actExec.setValue('state', 'complete');
    actExec.setValue('output_data', JSON.stringify({ result: 'auto_completed' }));
    actExec.update();
}
```

### 4.4 Client-Side: Refreshing the Playbook Panel

After a background operation completes, refresh the Playbook component from a Client Script or UI Builder client script:

```javascript
// In a UI Builder client script or Now Experience component
dispatch('PLAYBOOK_RELOAD', { recordSysId: record_sys_id });

// Or use the standard workspace event bus
window.dispatchEvent(new CustomEvent('sn:playbook:refresh', {
    detail: { recordSysId: props.recordId }
}));
```

---

## 5. Design Patterns & Best Practices

### 5.1 Process Decomposition Rules

```
When to use a LANE:          When to use an ACTIVITY:
─────────────────────        ───────────────────────
Distinct business phase      Single task or decision
Different team ownership     One responsible role
Clear entry/exit criteria    Discrete start/end
Sequential time blocks       5-30 minute effort
```

### 5.2 Activity → Subflow Architecture

Keep Activity Definitions thin. Push all business logic into Subflows:

```
Activity Definition: "Validate Invoice"
  └── Subflow: x_yourco_s2p.validate_invoice_subflow
        ├── Get Invoice Record
        ├── Check Required Fields (Script Action)
        ├── Validate against PO (Subflow: cross_reference_po)
        ├── Flag duplicates (GlideRecord in Script step)
        └── Output: validation_status, error_detail
```

> **Rule:** If an Activity Definition contains more than 3 scripted steps, extract into a Subflow.

### 5.3 Conditional Lane Routing

Use **lane conditions** based on record fields — NOT on activity/lane names (which break on rename):

```javascript
// ✅ CORRECT — condition based on record field
// Lane condition expression (evaluated on record):
current.getValue('contract_value') > 10000 && current.getValue('requires_legal') === 'true'

// ❌ WRONG — fragile, breaks on lane rename or reorder
current.lane_name === 'Legal Review'
```

### 5.4 Avoid Race Conditions

PAD processes often run alongside Business Rules and Flows. Risk areas:

| Risk | Mitigation |
|------|-----------|
| Business Rule fires during activity completion | Use `setWorkflow(false)` only for technical utilities, not business records |
| Two processes triggered simultaneously | Add duplicate-check in trigger condition using `sn_pad_process_execution` query |
| Activity auto-completes before agent reviews | Add mandatory manual step or approval as gate before automated exit |

### 5.5 Versioning & Deployment

```
Source Control Strategy:
  main          ← Stable, production-deployed processes
  feature/*     ← In-development process changes
  hotfix/*      ← Emergency fixes to active processes

Deployment Rules:
  1. Export Process via Workflow Studio → Source Control
  2. Test in sub-production with process version pinning
  3. Use "Activate Version" (not direct edits) to promote
  4. Never delete old versions — archive them (state = inactive)
```

### 5.6 Performance Considerations

| Risk | Pattern |
|------|---------|
| Slow playbook load (>3s) | Limit activities per lane to 10; use pagination |
| Heavy subflow blocking UI | Use `.inBackground()` execution for long activities |
| Repeated GlideRecord in activity scripts | Cache lookups in Subflow data outputs and pass forward |
| Lost context on refresh | Store key data as process execution output variables |

---

## 6. Security & Access Control

### 6.1 Process Definition Access
| Role | Permission |
|------|-----------|
| `sn_pad.admin` | Full PAD administration |
| `sn_pad.author` | Create/edit process definitions |
| `sn_pad.viewer` | Read-only access to PAD |
| *(custom)* `x_yourco_s2p.playbook_runner` | Execute playbooks in workspace |

### 6.2 Activity-Level Security
Control which roles see or can complete specific activities:

```javascript
// Activity display condition (set in PAD activity properties):
// Only show "Sanctions Screening" activity to compliance team
gs.hasRole('x_yourco_s2p.compliance_officer')

// Data isolation — use GlideRecordSecure in Subflows called by activities
var grSecure = new GlideRecordSecure('sn_ss_task');
grSecure.get(recordSysId); // Respects ACLs even in background context
```

---

## 7. Anti-Patterns

| Anti-Pattern | Problem | Fix |
|--------------|---------|-----|
| Logic inside Activity Definition script steps | Hard to test, duplicated across processes | Move to named Subflow |
| Hardcoded sys_ids in trigger conditions | Breaks between instances | Use encoded query with field values |
| Modifying OOTB process definitions | Breaks on upgrades | Fork and scope into custom app |
| Single mega-lane with 20+ activities | Unusable in Playbook UI | Split into 4-6 focused lanes |
| Skipping version control | Can't roll back bad process changes | Always use Workflow Studio Git integration |
| Business Rules conflicting with PAD automation | Race conditions, duplicate updates | Audit related automation before adding PAD to a table |
| Using display conditions based on lane/activity names | Fragile on rename | Use record field values for conditions |

---

## 8. Naming Conventions

| Artifact | Convention | Example |
|----------|-----------|---------|
| Process Definition | `<Scope Prefix> - <Noun> Process` | `x_yourco_s2p - Supplier Onboarding Process` |
| Activity Definition | `<Verb> <Noun>` (PascalCase) | `ValidateSupplierDocuments` |
| Lane | Business-language title | `Document Review`, `Legal Approval` |
| Subflow (PAD-backing) | `<Scope> - PAD <Noun> <Verb> Subflow` | `x_yourco_s2p - PAD Supplier Validate Subflow` |
| Process variable | `snake_case` | `contract_value`, `supplier_approved` |

---

## 9. Release Notes Highlights

| Release | PAD / Playbook Changes |
|---------|----------------------|
| **Xanadu** | Playbook versioning; AI-assisted process generation from natural language; default OOTB activities in Now Assist-generated Playbooks |
| **Washington DC** | PAD integrated into Workflow Studio; trigger license removed; dynamic inputs enhancement; improved playbook load performance |
| **Vancouver** | Parallel lanes GA; optional/ad-hoc activities; improved version management |

---

*Context module for Process Automation Designer & Playbooks — workspace-embedded guided experiences.*
