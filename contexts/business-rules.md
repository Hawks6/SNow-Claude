# Context Module: Business Rules

> **Module:** `business-rules.md`
> **Load when:** Creating, modifying, or reviewing Business Rules
> **Depends on:** `_base.md` (auto-loaded)

---

## 1. Business Rule Fundamentals

A Business Rule is a **server-side script** that executes when a record is displayed, inserted, updated, deleted, or queried.

### Execution Timing

| When | Fires | Use Case | Has `previous`? |
|------|-------|----------|:---------------:|
| **before** | Before DB write | Validation, field manipulation, abort | ✅ (on update) |
| **after** | After DB write | Notifications, related record updates, events | ✅ |
| **async** | After, in background | Heavy processing, integrations, long-running ops | ✅ |
| **display** | When form loads | Populate `g_scratchpad`, set display values | ❌ |

### Execution Order
- Business Rules execute in **order** value (ascending)
- Default order: **100**
- Lower numbers run first
- Use order to control sequencing: `100` (normal), `50` (early), `200` (late)
- Within the same order, execution is **not guaranteed** — don't depend on it

### Operation Types

| Operation | Trigger |
|-----------|---------|
| `insert` | New record created |
| `update` | Existing record modified |
| `delete` | Record deleted |
| `query` | Record list queried (careful — fires on EVERY query!) |

---

## 2. Key Objects

### `current` — The Active Record
```javascript
current.getValue('state');               // Get raw field value
current.setValue('priority', '1');        // Set field value (before BR)
current.short_description = 'New value'; // Also works, but getValue/setValue preferred
current.getDisplayValue('state');         // Display value ('New', 'In Progress')
current.getUniqueValue();                // sys_id
current.isNewRecord();                   // true on insert
current.operation();                     // 'insert', 'update', 'delete'
current.getTableName();                  // Table name
current.isValidRecord();                 // Has the record been loaded?
```

### `previous` — The Record Before Changes
```javascript
// Only available in before/after/async on UPDATE operations
previous.getValue('state');              // Value before this update
previous.priority;                       // Previous priority

// Change detection (preferred approach)
current.state.changes();                 // Did state change?
current.state.changesFrom('1');          // Changed from 'New'?
current.state.changesTo('6');            // Changed to 'Resolved'?
```

### Aborting an Operation (before rules only)
```javascript
// Prevent the insert/update/delete from occurring
current.setAbortAction(true);
gs.addErrorMessage('Cannot update: ' + reason);
```

---

## 3. Patterns

### Pattern: Field Auto-Population (Before Insert)
```javascript
// BR: Incident - Before Set Default Assignment
// Table: incident | When: before | Insert: true
// Condition: assignment_group is empty

(function executeRule(current, previous) {

    if (gs.nil(current.getValue('assignment_group'))) {
        var defaultGroup = gs.getProperty('x_acme_app.incident.default_group');
        if (!gs.nil(defaultGroup)) {
            current.setValue('assignment_group', defaultGroup);
            gs.info('[Incident - Before Set Default Assignment] ' +
                    'Auto-assigned to default group for: ' + current.getValue('number'));
        }
    }

})(current, previous);
```

### Pattern: Validation with Abort (Before Update)
```javascript
// BR: Change Request - Before Validate Approval
// Table: change_request | When: before | Update: true
// Condition: state changes to Implement (3)

(function executeRule(current, previous) {

    if (current.state.changesTo('3')) {
        var approvalStatus = current.getValue('approval');
        if (approvalStatus !== 'approved') {
            current.setAbortAction(true);
            gs.addErrorMessage('Change Request must be approved before implementation.');
            gs.warn('[Change Request - Before Validate Approval] ' +
                    'Blocked unapproved implementation attempt: ' + current.getValue('number'));
        }
    }

})(current, previous);
```

### Pattern: Related Record Update (After Insert/Update)
```javascript
// BR: Incident - After Update Parent Problem
// Table: incident | When: after | Update: true
// Condition: state changes to Resolved (6) AND problem_id is not empty

(function executeRule(current, previous) {

    if (current.state.changesTo('6') && !current.problem_id.nil()) {
        // Delegate heavy logic to Script Include
        var problemUtils = new x_acme_app.ProblemUtils();
        problemUtils.checkAllIncidentsResolved(current.getValue('problem_id'));
    }

})(current, previous);
```

### Pattern: Async Processing (Heavy Operations)
```javascript
// BR: Incident - Async Sync to External System
// Table: incident | When: async | Insert: true, Update: true

(function executeRule(current, previous) {

    var integrationUtils = new x_acme_app.ExternalSystemSync();
    var result = integrationUtils.syncIncident(current.getUniqueValue());

    if (!result.success) {
        gs.error('[Incident - Async Sync] Failed to sync ' + 
                 current.getValue('number') + ': ' + result.error);
    }

})(current, previous);
```

### Pattern: Display Business Rule (Scratchpad)
```javascript
// BR: Incident - Display Load Config
// Table: incident | When: display

(function executeRule(current, previous) {

    // Pass server data to client scripts via scratchpad
    g_scratchpad.canEscalate = gs.hasRole('itil_admin');
    g_scratchpad.maxAttachmentSize = gs.getProperty('x_acme_app.max_attachment_mb', '10');
    
    // Pass computed values
    var relatedCount = new GlideAggregate('incident');
    relatedCount.addQuery('caller_id', current.getValue('caller_id'));
    relatedCount.addQuery('state', 'IN', '1,2,3');
    relatedCount.addAggregate('COUNT');
    relatedCount.query();
    if (relatedCount.next()) {
        g_scratchpad.callerOpenIncidents = parseInt(relatedCount.getAggregate('COUNT'));
    }

})(current, previous);
```

### Pattern: Query Business Rule (Restrict Results)
```javascript
// BR: Incident - Query Restrict by Group
// Table: incident | When: before | Query: true
// ⚠️ Use sparingly — fires on EVERY query to this table

(function executeRule(current, previous) {

    // Only apply for non-admin users
    if (!gs.hasRole('admin')) {
        // Restrict to user's assignment groups
        var groups = new x_acme_app.UserGroupUtils().getUserGroupSysIds(gs.getUserID());
        if (groups.length > 0) {
            current.addQuery('assignment_group', 'IN', groups.join(','));
        }
    }

})(current, previous);
```

---

## 4. Anti-Patterns

### ❌ Heavy Logic in Business Rules
```javascript
// BAD — Business Rule doing too much
(function executeRule(current, previous) {
    var gr = new GlideRecord('sys_user_group');
    gr.addQuery('active', true);
    gr.query();
    while (gr.next()) {
        // 50 lines of complex routing logic...
    }
})(current, previous);

// ✅ GOOD — Delegate to Script Include
(function executeRule(current, previous) {
    var router = new x_acme_app.IncidentRouter();
    router.routeByCategory(current);
})(current, previous);
```

### ❌ Unnecessary current.update() in Before Rules
```javascript
// BAD — current.update() in a before BR causes double-save
(function executeRule(current, previous) {
    current.setValue('priority', '1');
    current.update();  // ❌ NEVER do this in a before rule!
})(current, previous);

// ✅ GOOD — just set the value; the platform handles the save
(function executeRule(current, previous) {
    current.setValue('priority', '1');
    // No update() needed — the platform saves automatically after all before BRs run
})(current, previous);
```

### ❌ No Condition Filtering
```javascript
// BAD — runs on EVERY update, wastes cycles
// Condition: (empty)
(function executeRule(current, previous) {
    if (current.state.changesTo('6')) {
        // only needed when state changes to Resolved
    }
})(current, previous);

// ✅ GOOD — use the Condition field
// Condition: state changes to 6
// OR use condition builder: stateVALCHANGES^stateSTARTSWITH6
```

### ❌ Recursive Updates
```javascript
// BAD — updating current in an after BR triggers another BR cycle!
(function executeRule(current, previous) {
    current.setValue('work_notes', 'Auto-note');
    current.update();  // Triggers all after BRs again! Infinite loop risk!
})(current, previous);

// ✅ GOOD — use update with autoSysFields and workflow control
(function executeRule(current, previous) {
    var gr = new GlideRecord('incident');
    if (gr.get(current.getUniqueValue())) {
        gr.autoSysFields(false);
        gr.setWorkflow(false);  // Prevent re-triggering BRs
        gr.setValue('work_notes', 'Auto-note');
        gr.update();
    }
})(current, previous);
```

### ❌ Query Business Rule Without Safeguards
```javascript
// BAD — this fires on EVERY query including list views, reports, 
// scheduled jobs, REST queries, etc.
// No role check, no escape hatch
(function executeRule(current, previous) {
    current.addQuery('active', true);
})(current, previous);

// ✅ GOOD — guard with role check and source detection
(function executeRule(current, previous) {
    if (!gs.hasRole('admin') && !gs.hasRole('x_acme_app.data_admin')) {
        current.addQuery('active', true);
    }
})(current, previous);
```

---

## 5. Decision Guide: When to Use What

```
Need to modify field values before save?
  → Before Business Rule

Need to create/update related records after save?
  → After Business Rule

Need heavy processing (API calls, large queries)?
  → Async Business Rule

Need to pass data to client scripts?
  → Display Business Rule

Need simple field visibility/mandatory rules?
  → UI Policy (no code needed!)

Need to restrict query results?
  → Query Business Rule (use very sparingly)
  → Consider ACLs or domain separation first

Need form button action?
  → UI Action (not a Business Rule)

Need to trigger complex multi-step process?
  → Flow Designer (not a Business Rule)
```

---

## 6. Performance Guidelines

| Guideline | Reason |
|-----------|--------|
| Keep BRs under 20 lines of inline code | Readability, maintainability |
| Use condition field instead of script conditions | Avoids loading the Rhino engine |
| Limit queries inside BRs (max 2-3) | Each query = DB round trip |
| Never query inside query BRs | Fires on every list/report load |
| Use async for anything >100ms | Don't block the user transaction |
| Profile with "Business Rule Execution Order" | Debug plugin for BR ordering |

---

## 7. Configuration Checklist

When creating a Business Rule, verify:

- [ ] **Name** follows convention: `<Table> - <When> <Description>`
- [ ] **Table** is the most specific table (not `task` if you mean `incident`)
- [ ] **When** is correct (`before` for validation/field-set, `after` for side-effects)
- [ ] **Order** is appropriate (100 default, adjust if sequencing matters)
- [ ] **Operations** are minimal (don't check all four if you only need update)
- [ ] **Condition** is set (use condition builder or encoded query if possible)
- [ ] **Scope** is correct application
- [ ] **Active** is checked
- [ ] **Role** requirements considered (if applicable)
- [ ] Logic is delegated to Script Include for anything complex

---

*Context module for Business Rule development in ServiceNow.*
