# Context Module: Client Scripts & UI Policies

> **Module:** `client-scripts.md`
> **Load when:** Creating Client Scripts, UI Policies, UI Actions, or Catalog Client Scripts
> **Depends on:** `_base.md` (auto-loaded)

---

## 1. Client Script Types

| Type | Fires When | Signature | Use Case |
|------|-----------|-----------|----------|
| **onLoad** | Form loads | `function onLoad() {}` | Initialize form, hide fields, set defaults |
| **onChange** | Field value changes | `function onChange(control, oldValue, newValue, isLoading) {}` | React to user input |
| **onSubmit** | Form submit/save | `function onSubmit() { return true; }` | Validate before save |
| **onCellEdit** | List cell edit | `function onCellEdit(sysIDs, table, oldValues, newValue, callback) {}` | Validate list edits |

---

## 2. Patterns

### onLoad — Initialize Form
```javascript
function onLoad() {
    // Hide fields for non-admin users
    if (!g_user.hasRole('itil_admin')) {
        g_form.setVisible('u_internal_notes', false);
        g_form.setReadOnly('priority', true);
    }

    // Set defaults on new records
    if (g_form.isNewRecord()) {
        g_form.setValue('contact_type', 'self-service');
    }

    // Use scratchpad data (set in Display Business Rule)
    if (g_scratchpad.callerOpenIncidents > 3) {
        g_form.addInfoMessage('Caller has ' + g_scratchpad.callerOpenIncidents + ' open incidents');
    }
}
```

### onChange — React to Field Changes
```javascript
function onChange(control, oldValue, newValue, isLoading) {
    // ALWAYS skip processing on form load
    if (isLoading || newValue === '') {
        return;
    }

    // Use GlideAjax for server lookups (NEVER client-side GlideRecord)
    var ga = new GlideAjax('x_acme_app.IncidentAjaxUtils');
    ga.addParam('sysparm_name', 'getCallerInfo');
    ga.addParam('sysparm_caller_id', newValue);
    ga.getXMLAnswer(function(response) {
        try {
            var result = JSON.parse(response);
            if (result.success && result.is_vip) {
                g_form.setValue('priority', '1');
                g_form.addInfoMessage('VIP caller — priority escalated');
            }
        } catch (e) {
            // Graceful degradation
        }
    });
}
```

### onSubmit — Validate Before Save
```javascript
function onSubmit() {
    var desc = g_form.getValue('short_description');
    if (desc.length < 10) {
        g_form.addErrorMessage('Short description must be at least 10 characters.');
        return false;  // Prevent submission
    }

    // Check mandatory with custom message
    if (g_form.getValue('assignment_group') === '') {
        g_form.showFieldMsg('assignment_group', 'Please select an assignment group', 'error');
        return false;
    }

    return true;  // Allow submission
}
```

---

## 3. UI Policies vs Client Scripts

| Feature | UI Policy | Client Script |
|---------|-----------|---------------|
| No code needed | ✅ | ❌ |
| Show/hide fields | ✅ | ✅ |
| Set mandatory | ✅ | ✅ |
| Set read-only | ✅ | ✅ |
| Complex logic | ❌ (limited) | ✅ |
| GlideAjax calls | ❌ | ✅ |
| Form validation | ❌ | ✅ (onSubmit) |
| Mobile/Agent WS | ⚠️ varies | ⚠️ varies |

**Rule:** Use **UI Policy** for simple field visibility/mandatory/read-only. Use **Client Script** only when logic is needed.

---

## 4. Catalog Client Scripts

Same types but run on **Service Catalog item forms**:

```javascript
// Catalog onChange — uses variable names (not field names)
function onChange(control, oldValue, newValue, isLoading, isTemplate) {
    if (isLoading || newValue === '') return;

    // Catalog variables use their name directly
    var requestType = g_form.getValue('request_type');  // Variable name
    
    if (requestType === 'hardware') {
        g_form.setVisible('hardware_model', true);
        g_form.setMandatory('hardware_model', true);
    } else {
        g_form.setVisible('hardware_model', false);
        g_form.setMandatory('hardware_model', false);
    }
}
```

---

## 5. Anti-Patterns

### ❌ Client-Side GlideRecord
```javascript
// NEVER — synchronous, slow, insecure
var gr = new GlideRecord('incident');
gr.query();  // ❌ Blocks browser!

// ✅ Use GlideAjax
```

### ❌ Direct DOM Manipulation
```javascript
// BAD
document.getElementById('element').style.display = 'none';  // ❌

// GOOD
g_form.setVisible('field_name', false);  // ✅
```

### ❌ Using alert()
```javascript
alert('Error!');  // ❌ Blocks UI, unprofessional

g_form.addErrorMessage('Error description');  // ✅
```

### ❌ Not Checking isLoading
```javascript
// BAD — fires during form load, causes unnecessary server calls
function onChange(control, oldValue, newValue, isLoading) {
    // Missing isLoading check!  ❌
    var ga = new GlideAjax('...');
    // ...
}
```

---

## 6. Agent Workspace / Mobile Considerations

Not all client-side APIs work in Agent Workspace or Mobile:
- `g_form` works (mostly) in Agent Workspace
- Direct DOM manipulation **never** works
- `window` and `document` objects not available
- GlideAjax works but test thoroughly
- UI Policies are generally safer for cross-platform compatibility

---

*Context module for Client Script and UI Policy development.*
