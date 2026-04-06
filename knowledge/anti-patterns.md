# Knowledge Base: Anti-Patterns

> **File:** `anti-patterns.md`
> **Purpose:** The "Hall of Shame" — common ServiceNow mistakes and their fixes

---

## 🔴 Critical Anti-Patterns

### 1. Client-Side GlideRecord
```javascript
// ❌ Synchronous, blocks browser, exposes data model
var gr = new GlideRecord('sys_user');
gr.addQuery('user_name', username);
gr.query();

// ✅ Use GlideAjax
var ga = new GlideAjax('MyAjaxUtils');
ga.addParam('sysparm_name', 'getUserInfo');
ga.addParam('sysparm_username', username);
ga.getXMLAnswer(callback);
```

### 2. Hardcoded sys_ids
```javascript
// ❌ Breaks across instances
current.setValue('assignment_group', '27d2c2e80a0a0b7900d4b5f57108b848');

// ✅ Use system property or named query
var groupSysId = gs.getProperty('x_acme_app.default.assignment_group');
current.setValue('assignment_group', groupSysId);
```

### 3. current.update() in Before Business Rules
```javascript
// ❌ Causes double-save and recursive loops
current.setValue('state', '2');
current.update();  // NEVER in before BR!

// ✅ Just set value — platform auto-saves after all before BRs
current.setValue('state', '2');
```

### 4. No Input Validation in REST APIs
```javascript
// ❌ Trust client input
var userId = request.queryParams.user_id;
var gr = new GlideRecord('sys_user');
gr.get(userId);  // What if null? Malicious?

// ✅ Validate + GlideRecordSecure
var userId = request.queryParams.user_id;
if (gs.nil(userId) || userId.length !== 32) {
    response.setStatus(400);
    response.setBody({ error: 'Invalid user_id' });
    return;
}
var grs = new GlideRecordSecure('sys_user');
if (!grs.get(userId)) {
    response.setStatus(404);
    return;
}
```

---

## 🟠 High Anti-Patterns

### 5. Nested GlideRecord Queries (N+1)
```javascript
// ❌ Quadratic query explosion
while (grInc.next()) {
    var grUser = new GlideRecord('sys_user');
    grUser.get(grInc.getValue('assigned_to'));  // 1 query per row!
}

// ✅ Collect IDs → single IN query
```

### 6. Looping to Count Records
```javascript
// ❌ Loads every record into memory
var count = 0;
var gr = new GlideRecord('incident');
gr.addQuery('active', true);
gr.query();
while (gr.next()) count++;

// ✅ GlideAggregate — database-level COUNT
var ga = new GlideAggregate('incident');
ga.addQuery('active', true);
ga.addAggregate('COUNT');
ga.query();
ga.next();
var count = parseInt(ga.getAggregate('COUNT'));
```

### 7. Modifying OOB Records
```
❌ Editing the OOB "Incident - Set Priority" Business Rule
  → Breaks on upgrade, overwrites your changes

✅ Deactivate OOB, create custom clone with your changes
  → Clone naming: "Custom - Incident - Set Priority"
```

### 8. Using gs.log()
```javascript
// ❌ No severity level, writes to syslog_app only
gs.log('Something happened');

// ✅ Use leveled logging
gs.info('[MyClass] Informational message');
gs.warn('[MyClass] Warning condition');
gs.error('[MyClass] Error occurred');
```

---

## 🟡 Medium Anti-Patterns

### 9. alert() in Client Scripts
```javascript
// ❌ Blocks UI, breaks mobile
alert('Error!');

// ✅ ServiceNow messaging
g_form.addErrorMessage('Error description');
g_form.showFieldMsg('field_name', 'Field-level error', 'error');
```

### 10. Missing isLoading Check in onChange
```javascript
// ❌ Fires GlideAjax on every form load
function onChange(control, oldValue, newValue, isLoading) {
    var ga = new GlideAjax('...');  // Runs on load!
}

// ✅ Guard against load
function onChange(control, oldValue, newValue, isLoading) {
    if (isLoading || newValue === '') return;
    // Now safe to call server
}
```

### 11. Storing GlideRecord Objects in Arrays
```javascript
// ❌ GlideRecord is a pointer — all entries become the last record
while (gr.next()) { results.push(gr); }

// ✅ Extract primitive values
while (gr.next()) {
    results.push({ sys_id: gr.getUniqueValue(), name: gr.getValue('name') });
}
```

### 12. Query Business Rules Without Guards
```javascript
// ❌ Fires on EVERY query — lists, reports, REST, integrations
// No escape hatch for admin

// ✅ Guard with role check
if (!gs.hasRole('admin')) {
    current.addQuery('active', true);
}
```

### 13. Excessive Dot-Walking in Loops
```javascript
// ❌ Each dot-walk = extra DB query per row
while (gr.next()) {
    var dept = gr.assigned_to.department.name;  // 2 queries per row!
}

// ✅ Use addJoinQuery or collect + batch lookup
```

### 14. Not Using setLimit()
```javascript
// ❌ Returns ALL matching records
var gr = new GlideRecord('incident');
gr.addQuery('active', true);
gr.query();  // Could return 100,000 records!
if (gr.next()) { /* only need first */ }

// ✅
gr.setLimit(1);
gr.query();
```

### 15. Using eval() or Dynamic Script Construction
```javascript
// ❌ Security risk — code injection
eval('current.' + fieldName + ' = "value"');

// ✅ Use safe APIs
current.setValue(fieldName, 'value');
```

---

*Knowledge base of ServiceNow anti-patterns — reference during code review.*
