# Knowledge Base: API Quick-Reference Patterns

> **File:** `api-patterns.md`
> **Purpose:** Copy-paste patterns for the 20 most-used ServiceNow APIs

---

## 1. GlideRecord — Query + Loop
```javascript
var gr = new GlideRecord('incident');
gr.addQuery('active', true);
gr.addQuery('priority', '<=', '2');
gr.orderByDesc('sys_created_on');
gr.setLimit(25);
gr.query();
while (gr.next()) {
    var num = gr.getValue('number');
}
```

## 2. GlideRecord — Get by sys_id
```javascript
var gr = new GlideRecord('incident');
if (gr.get(sysId)) {
    // Record found
}
```

## 3. GlideRecord — Insert
```javascript
var gr = new GlideRecord('incident');
gr.initialize();
gr.setValue('short_description', 'New incident');
gr.setValue('caller_id', callerSysId);
var newSysId = gr.insert();
```

## 4. GlideAggregate — Count
```javascript
var ga = new GlideAggregate('incident');
ga.addQuery('active', true);
ga.addAggregate('COUNT');
ga.query();
if (ga.next()) {
    var count = parseInt(ga.getAggregate('COUNT'));
}
```

## 5. GlideAjax — Client-to-Server Call
```javascript
// CLIENT
var ga = new GlideAjax('x_acme_app.MyAjaxUtils');
ga.addParam('sysparm_name', 'myMethod');
ga.addParam('sysparm_my_param', value);
ga.getXMLAnswer(function(answer) {
    var result = JSON.parse(answer);
});

// SERVER (extends AbstractAjaxProcessor)
myMethod: function() {
    var param = this.getParameter('sysparm_my_param');
    return JSON.stringify({ success: true, data: param });
}
```

## 6. GlideDateTime — Operations
```javascript
var now = new GlideDateTime();
var future = new GlideDateTime();
future.addDaysUTC(7);
var isOverdue = now.after(dueDate);
var dur = GlideDateTime.subtract(start, end);
```

## 7. System Property — Read/Write
```javascript
var val = gs.getProperty('x_acme_app.my.property', 'default');
gs.setProperty('x_acme_app.my.property', 'new_value');
```

## 8. Event Queue
```javascript
// Queue event (server-side)
gs.eventQueue('x_acme_app.my_event', current, parm1, parm2);
```

## 9. REST Message (Outbound)
```javascript
var rm = new sn_ws.RESTMessageV2('x_acme_app.MyEndpoint', 'POST');
rm.setRequestBody(JSON.stringify(payload));
var resp = rm.execute();
var status = resp.getStatusCode();
var body = JSON.parse(resp.getBody());
```

## 10. Scheduled Job Script
```javascript
// Runs as: scheduled job script
var gr = new GlideRecord('incident');
gr.addQuery('state', '1');
gr.addQuery('sys_created_on', '<', gs.daysAgoStart(7));
gr.query();
while (gr.next()) {
    gr.setValue('state', '2');
    gr.work_notes = 'Auto-escalated: stale for 7+ days';
    gr.update();
}
```

## 11. g_form — Common Client Operations
```javascript
g_form.getValue('field');
g_form.setValue('field', value, displayValue);
g_form.setVisible('field', true);
g_form.setMandatory('field', true);
g_form.setReadOnly('field', true);
g_form.addInfoMessage('message');
g_form.addErrorMessage('message');
g_form.showFieldMsg('field', 'msg', 'error');
g_form.isNewRecord();
g_form.save();
g_form.submit();
```

## 12. Flow API — Trigger from Script
```javascript
sn_fd.FlowAPI.getRunner()
    .flow('x_acme_app.my_flow')
    .inBackground()
    .withInputs({ record: current })
    .run();
```

## 13. Email — gs.eventQueue for Notifications
```javascript
gs.eventQueue('x_acme_app.notify_approver', current, 
    current.getValue('assigned_to'), 
    current.getValue('number'));
```

## 14. User / Role Checks
```javascript
// Server-side
gs.getUserID();
gs.getUser().hasRole('admin');
gs.getUser().getMyGroups();
gs.getUser().isMemberOf(groupSysId);

// Client-side
g_user.userID;
g_user.hasRole('itil');
```

## 15. Encoded Query from URL
```javascript
// Copy from list filter URL → paste as encoded query
gr.addEncodedQuery('active=true^state!=7^assignment_groupDYNAMIC90d1921e5f510100a9ad2572f2b477fe');
```

---

*API patterns quick-reference for ServiceNow development.*
