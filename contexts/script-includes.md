# Context Module: Script Includes

> **Module:** `script-includes.md`
> **Load when:** Creating, modifying, or reviewing Script Includes
> **Depends on:** `_base.md` (auto-loaded)

---

## 1. Fundamentals

A Script Include is a **reusable server-side JavaScript class** callable from Business Rules, other Script Includes, Flows, REST APIs, and (via GlideAjax) Client Scripts.

**Key principle:** Script Includes are the **primary location for business logic**. Everything else should delegate to them.

### Configuration

| Property | Options | Notes |
|----------|---------|-------|
| **Name** | PascalCase | Must match class name in script |
| **Client callable** | true/false | Enable for GlideAjax |
| **Accessible from** | This scope / All scopes | Cross-scope visibility |

---

## 2. The Prototype Pattern

```javascript
var ApprovalMatrixUtils = Class.create();
ApprovalMatrixUtils.prototype = {

    initialize: function() {
        this.LOG_PREFIX = '[ApprovalMatrixUtils]';
        this.approvalTable = 'x_acme_app_approval_matrix';
    },

    /**
     * @description Get approvers for a record
     * @param {GlideRecord} grRecord - Record requiring approval
     * @param {string} approvalType - 'standard'|'emergency'|'expedited'
     * @returns {Object} { success, approvers[], message }
     */
    getApprovers: function(grRecord, approvalType) {
        if (!grRecord || !grRecord.isValidRecord()) {
            gs.error(this.LOG_PREFIX + ' getApprovers: Invalid record');
            return { success: false, approvers: [], message: 'Invalid record' };
        }
        approvalType = approvalType || 'standard';

        var approvers = [];
        var gr = new GlideRecord(this.approvalTable);
        gr.addQuery('type', approvalType);
        gr.addQuery('category', grRecord.getValue('category'));
        gr.addQuery('active', true);
        gr.orderBy('order');
        gr.query();

        while (gr.next()) {
            approvers.push(gr.getValue('approver'));
        }

        if (approvers.length === 0) {
            gs.warn(this.LOG_PREFIX + ' No approvers for type=' + approvalType);
            return { success: false, approvers: [], message: 'No approvers configured' };
        }

        return { success: true, approvers: approvers, message: approvers.length + ' found' };
    },

    type: 'ApprovalMatrixUtils'
};
```

### Structure Rules
1. **`Class.create()`** — always use this
2. **`initialize`** — set constants, log prefixes, table names
3. **Methods** — JSDoc, input validation, structured returns
4. **`type`** — last property, matches class name exactly
5. **Name** — class name must match Script Include record name

---

## 3. Client-Callable (GlideAjax)

### Server-Side
```javascript
var IncidentAjaxUtils = Class.create();
IncidentAjaxUtils.prototype = Object.extendsObject(AbstractAjaxProcessor, {

    getCallerInfo: function() {
        var callerId = this.getParameter('sysparm_caller_id');
        if (gs.nil(callerId)) {
            return JSON.stringify({ success: false, message: 'Missing caller_id' });
        }

        var result = { success: true, is_vip: false, open_incidents: 0 };

        var grUser = new GlideRecord('sys_user');
        if (grUser.get(callerId)) {
            result.is_vip = grUser.getValue('vip') === 'true';
        }

        var ga = new GlideAggregate('incident');
        ga.addQuery('caller_id', callerId);
        ga.addQuery('state', 'IN', '1,2,3');
        ga.addAggregate('COUNT');
        ga.query();
        if (ga.next()) {
            result.open_incidents = parseInt(ga.getAggregate('COUNT'));
        }

        return JSON.stringify(result);
    },

    isPublic: function(methodName) {
        return ['getCallerInfo'].indexOf(methodName) >= 0;
    },

    type: 'IncidentAjaxUtils'
});
```

### Client-Side Call
```javascript
var ga = new GlideAjax('x_acme_app.IncidentAjaxUtils');
ga.addParam('sysparm_name', 'getCallerInfo');
ga.addParam('sysparm_caller_id', newValue);
ga.getXMLAnswer(function(response) {
    var result = JSON.parse(response);
    if (result.success && result.is_vip) {
        g_form.setValue('priority', '1');
    }
});
```

---

## 4. Anti-Patterns

### ❌ Class Name Mismatch
```javascript
// Record name: "MyUtilities" but script says:
var MyUtils = Class.create();  // ❌ Won't load!
```

### ❌ No Input Validation
```javascript
// BAD
getUser: function(userId) {
    var gr = new GlideRecord('sys_user');
    gr.get(userId);  // ❌ What if null?
    return gr;
}

// GOOD
getUser: function(userId) {
    if (gs.nil(userId)) return null;
    var gr = new GlideRecord('sys_user');
    return gr.get(userId) ? gr : null;
}
```

### ❌ Returning GlideRecord from Loop
```javascript
// BAD — GlideRecord is a pointer, changes on .next()!
users.push(gr);  // ❌ All point to last record

// GOOD — extract values
users.push({ sys_id: gr.getUniqueValue(), name: gr.getDisplayValue('name') });
```

### ❌ Missing type Property
Always end with `type: 'ClassName'` — required for platform introspection.

---

## 5. Cross-Scope Access

```javascript
// Calling from another scope:
var utils = new x_acme_app.ApprovalMatrixUtils();
var result = utils.getApprovers(current, 'standard');
```

Requirements:
- Target SI: **Accessible from → All application scopes**
- Cross-scope calls run in the **caller's** scope context

---

## 6. Testing

### ATF Test Structure
```
Test Suite: ApprovalMatrixUtils
├── Happy Path - returns expected approvers
├── No Approvers - returns success=false
├── Invalid Input - handles null gracefully
└── Edge Cases - empty string, missing fields
```

---

*Context module for Script Include development.*
