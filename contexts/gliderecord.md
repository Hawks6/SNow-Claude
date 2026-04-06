# Context Module: GlideRecord & GlideAggregate

> **Module:** `gliderecord.md`
> **Load when:** Any server-side data operations
> **Depends on:** `_base.md` (auto-loaded)

---

## 1. GlideRecord Essentials

### Basic CRUD

```javascript
// CREATE
var gr = new GlideRecord('incident');
gr.initialize();
gr.setValue('short_description', 'New incident');
gr.setValue('caller_id', callerSysId);
var newSysId = gr.insert();

// READ (single record by sys_id)
var gr = new GlideRecord('incident');
if (gr.get(sysId)) {
    var desc = gr.getValue('short_description');
}

// READ (query with conditions)
var gr = new GlideRecord('incident');
gr.addQuery('state', '1');                    // state = New
gr.addQuery('priority', '<=', '2');           // priority Critical or High
gr.orderByDesc('sys_created_on');
gr.setLimit(10);
gr.query();
while (gr.next()) {
    gs.info(gr.getValue('number') + ': ' + gr.getValue('short_description'));
}

// UPDATE
var gr = new GlideRecord('incident');
if (gr.get(sysId)) {
    gr.setValue('state', '2');  // In Progress
    gr.update();
}

// DELETE
var gr = new GlideRecord('incident');
if (gr.get(sysId)) {
    gr.deleteRecord();
}

// DELETE MULTIPLE (⚠️ dangerous — no BR triggers)
var gr = new GlideRecord('x_acme_app_temp_log');
gr.addQuery('sys_created_on', '<', cutoffDate);
gr.deleteMultiple();  // Bypasses Business Rules!
```

### Query Methods

```javascript
// Standard conditions
gr.addQuery('field', 'value');                    // field = value
gr.addQuery('field', '!=', 'value');              // field != value
gr.addQuery('field', '>', 'value');               // field > value
gr.addQuery('field', '>=', 'value');              // field >= value
gr.addQuery('field', '<', 'value');               // field < value
gr.addQuery('field', 'IN', 'val1,val2,val3');     // field IN list
gr.addQuery('field', 'NOT IN', 'val1,val2');      // field NOT IN list
gr.addQuery('field', 'STARTSWITH', 'prefix');     // field starts with
gr.addQuery('field', 'ENDSWITH', 'suffix');       // field ends with
gr.addQuery('field', 'CONTAINS', 'text');         // field contains
gr.addQuery('field', 'DOESNOTCONTAIN', 'text');   // field doesn't contain
gr.addQuery('field', 'ISEMPTY', '');              // field is empty
gr.addQuery('field', 'ISNOTEMPTY', '');            // field is not empty
gr.addNullQuery('field');                          // field is NULL
gr.addNotNullQuery('field');                       // field is NOT NULL
gr.addActiveQuery();                               // active = true

// OR conditions
var qc = gr.addQuery('state', '1');
qc.addOrCondition('state', '2');                  // state = 1 OR state = 2

// Encoded queries (from list filter URL)
gr.addEncodedQuery('active=true^stateIN1,2,3^priority<=2');

// Join queries (avoid N+1)
var join = gr.addJoinQuery('sys_user_grmember', 'assignment_group', 'group');
join.addCondition('user', gs.getUserID());

// Ordering
gr.orderBy('priority');                            // ASC
gr.orderByDesc('sys_created_on');                  // DESC

// Limiting
gr.setLimit(1);                                    // Only 1 result
gr.chooseWindow(0, 20);                            // Pagination (start, end)

// Field control
gr.setWorkflow(false);                             // Skip BRs on update/insert
gr.autoSysFields(false);                           // Don't update sys_updated_*
```

### Reading Values

```javascript
// PREFERRED — returns raw string value
gr.getValue('field_name');

// Display value (formatted, resolved references)
gr.getDisplayValue('field_name');

// Sys_id
gr.getUniqueValue();

// Check emptiness
gr.field_name.nil();     // true if empty
gs.nil(gr.getValue('field'));  // also works

// Row count (✅ use GlideAggregate instead for large tables)
gr.getRowCount();  // ⚠️ Loads ALL rows into memory!

// Dot-walking (caution — extra DB queries)
gr.getValue('assigned_to.manager');  // 1 extra query
gr.assigned_to.manager.department;   // 2 extra queries!
```

---

## 2. GlideAggregate

Use for **counts, sums, averages** — never loop a GlideRecord to count.

```javascript
// COUNT
var ga = new GlideAggregate('incident');
ga.addQuery('state', 'IN', '1,2,3');
ga.addAggregate('COUNT');
ga.query();
if (ga.next()) {
    var count = parseInt(ga.getAggregate('COUNT'));
}

// COUNT with GROUP BY
var ga = new GlideAggregate('incident');
ga.addQuery('active', true);
ga.addAggregate('COUNT');
ga.groupBy('priority');
ga.query();
while (ga.next()) {
    gs.info('Priority ' + ga.getValue('priority') + ': ' + ga.getAggregate('COUNT'));
}

// SUM
var ga = new GlideAggregate('sc_req_item');
ga.addQuery('request', requestSysId);
ga.addAggregate('SUM', 'u_cost');
ga.query();
if (ga.next()) {
    var totalCost = parseFloat(ga.getAggregate('SUM', 'u_cost'));
}

// AVG / MIN / MAX
ga.addAggregate('AVG', 'u_resolution_time');
ga.addAggregate('MIN', 'sys_created_on');
ga.addAggregate('MAX', 'priority');
```

---

## 3. GlideRecordSecure

**Always use in user-facing contexts** (REST APIs, Service Portal):

```javascript
// Enforces ACLs — returns only records the user can access
var grs = new GlideRecordSecure('incident');
grs.addQuery('active', true);
grs.query();
// Only records passing ACL checks are returned
```

---

## 4. Performance Rules

| Rule | Why |
|------|-----|
| Always use `addQuery()` | Prevents full-table scans |
| Use indexed fields in queries | Orders of magnitude faster |
| Use `setLimit()` when possible | Reduces memory and DB load |
| Use `getValue()` over `gr.field` | Avoids GlideElement overhead |
| Use `GlideAggregate` for counts | Database-level aggregation |
| Avoid nested GlideRecord loops | N+1 query problem |
| Use `addJoinQuery()` for related data | Single query instead of loop |
| Minimize dot-walking in loops | Each dot-walk = extra query |
| Use `chooseWindow()` for pagination | Memory-safe batch processing |
| Use `addEncodedQuery()` for complex filters | Often more efficient |

### ❌ Anti-Pattern: Nested GlideRecord (N+1)
```javascript
// BAD — 1 query + N queries
var grInc = new GlideRecord('incident');
grInc.addQuery('active', true);
grInc.query();
while (grInc.next()) {
    var grUser = new GlideRecord('sys_user');  // ❌ Query per row!
    grUser.get(grInc.getValue('assigned_to'));
}

// GOOD — collect IDs, single IN query
var userIds = [];
var grInc = new GlideRecord('incident');
grInc.addQuery('active', true);
grInc.query();
while (grInc.next()) {
    var userId = grInc.getValue('assigned_to');
    if (!gs.nil(userId) && userIds.indexOf(userId) === -1) {
        userIds.push(userId);
    }
}
// Single query for all users
var grUsers = new GlideRecord('sys_user');
grUsers.addQuery('sys_id', 'IN', userIds.join(','));
grUsers.query();
```

### ❌ Anti-Pattern: Client-Side GlideRecord
```javascript
// ❌ NEVER do this in Client Scripts
var gr = new GlideRecord('incident');
gr.addQuery('caller_id', g_form.getValue('caller_id'));
gr.query();  // Synchronous! Blocks the browser!

// ✅ Use GlideAjax instead (see script-includes.md)
```

---

*Context module for GlideRecord & GlideAggregate operations.*
