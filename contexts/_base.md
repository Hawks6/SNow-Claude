# Context Module: ServiceNow Platform Foundations

> **Module:** `_base.md`
> **Auto-loaded:** Yes — this module is always active with SYSTEM.md
> **Purpose:** Core platform knowledge that applies to ALL ServiceNow development

---

## 1. Scoped Application Model

### Application Scope
Every ServiceNow application operates within a **scope** — an isolated namespace that controls access to data and APIs.

```
Global Scope (global)
├── System tables, OOB Business Rules, Script Includes
├── Full API access (GlideRecord, GlideSystem, etc.)
└── Avoid modifying — impacts upgradeability

Custom Scoped App (x_vendor_appname)
├── Your custom tables, scripts, flows, UI
├── Restricted API access (Scoped API subset)
├── Isolated from other apps by default
└── Deployable via App Repository or Source Control
```

### Scope Naming Convention
```
x_<vendor_prefix>_<app_name>

vendor_prefix: 2-5 lowercase chars identifying your organization
app_name:      lowercase, underscores, descriptive

Examples:
  x_acme_itsm_ext       (Acme's ITSM Extension)
  x_myco_asset_mgmt     (MyCo Asset Management)
  x_demo_hr_onboard     (Demo HR Onboarding)
```

### Application Access Settings
Configure on each table via **Application Access** tab:

| Setting | Description | Default |
|---------|-------------|---------|
| Can read | Other scopes can read records | ✅ |
| Can create | Other scopes can create records | ❌ |
| Can update | Other scopes can update records | ❌ |
| Can delete | Other scopes can delete records | ❌ |
| Allow access to this table via web services | REST/SOAP access | ❌ |
| Allow configuration | Other scopes can create configs | ❌ |

**Rule:** Start with everything **off**, open only what's explicitly needed.

### Cross-Scope Access
When calling Script Includes across scopes:
```javascript
// From scope x_acme_app_b, calling x_acme_app_a.MyUtils
var utils = new x_acme_app_a.MyUtils();
var result = utils.doSomething(param);
```
- The target Script Include must have **Accessible from: All application scopes**
- The calling scope needs appropriate **Application Cross-Scope Access** records

---

## 2. Execution Boundaries

### Server-Side Context
Scripts that run on the ServiceNow server (Java/Rhino engine):

| Artifact | Execution Context | Key Objects |
|----------|------------------|-------------|
| Business Rule | Record operation (CRUD) | `current`, `previous`, `gs` |
| Script Include | Called by other scripts | `this`, constructor params |
| Scheduled Job | Time-based trigger | `gs` |
| Fix Script | One-time execution | `gs` |
| Transform Map Script | Data import | `source`, `target`, `map` |
| Scripted REST API | HTTP request handler | `request`, `response` |
| Flow Designer Script | Within Flow actions | `fd_data`, action inputs |
| Event Script | Event queue processing | `event`, `current` |
| UI Action (server) | Form button click | `current`, `action` |

### Client-Side Context
Scripts that run in the user's browser:

| Artifact | Execution Context | Key Objects |
|----------|------------------|-------------|
| Client Script | Form interactions | `g_form`, `g_user`, `g_scratchpad` |
| UI Policy Script | Form field rules | `g_form` |
| Catalog Client Script | Catalog item forms | `g_form`, `g_user` |
| Service Portal Widget | AngularJS controller | `$scope`, `c.data`, `c.server` |
| Now Experience | Web component | Component state, `dispatch` |

### Critical Rule: NEVER cross the boundary incorrectly
```
❌ Client-side GlideRecord (synchronous, insecure, slow)
✅ GlideAjax → Client-callable Script Include

❌ Direct DOM manipulation in ServiceNow forms
✅ g_form API methods (setValue, setVisible, etc.)

❌ alert() / console.log() in production client scripts
✅ g_form.addInfoMessage() / g_form.addErrorMessage()
```

---

## 3. Key Server-Side APIs (Scoped)

### GlideSystem (gs)
```javascript
gs.getProperty('property.name');           // System property value
gs.getUser().getID();                      // Current user sys_id
gs.getUser().hasRole('role_name');          // Role check
gs.getUserID();                            // Current user sys_id (shorthand)
gs.getUserName();                          // Current user username
gs.nil(value);                             // Null/empty/undefined check
gs.info('message');                        // Info log
gs.warn('message');                        // Warning log
gs.error('message');                       // Error log
gs.debug('message');                       // Debug log (requires debug enabled)
gs.addInfoMessage('msg');                  // UI info banner (server-side)
gs.addErrorMessage('msg');                 // UI error banner (server-side)
gs.now();                                  // Current date-time (UTC)
gs.nowDateTime();                          // Current date-time string
gs.generateGUID();                         // Generate a unique ID
gs.include('ScriptIncludeName');           // Load a non-class Script Include
gs.tableExists('table_name');              // Check if table exists
gs.sleep(milliseconds);                    // Pause execution (use sparingly!)
gs.eventQueue('event.name', current, p1, p2); // Queue a system event
gs.getSession().getLanguage();             // Current session language
```

### GlideDateTime
```javascript
var gdt = new GlideDateTime();             // Current date-time
var gdt = new GlideDateTime('2024-01-15 08:00:00'); // Specific date-time
gdt.addDaysUTC(5);                         // Add days
gdt.addSeconds(3600);                      // Add seconds
gdt.getNumericValue();                     // Epoch milliseconds
gdt.getDisplayValue();                     // User-timezone formatted
gdt.getValue();                            // Internal value (UTC)
gdt.before(otherGdt);                      // Comparison
gdt.after(otherGdt);                       // Comparison

// Duration between two dates
var dur = GlideDateTime.subtract(start, end);  // GlideDuration
dur.getDisplayValue();                     // "X Days HH:MM:SS"
```

### GlideElement (field operations on current/gr)
```javascript
current.getValue('field_name');            // Raw value (string)
current.getDisplayValue('field_name');     // Display value
current.field_name.nil();                  // Is field empty?
current.field_name.canRead();              // ACL read check
current.field_name.canWrite();             // ACL write check
current.field_name.changes();              // Did field change? (BR only)
current.field_name.changesFrom(value);     // Changed from specific value?
current.field_name.changesTo(value);       // Changed to specific value?
current.field_name.toString();             // String representation
current.field_name.getRefRecord();         // Dereference (loads full record)
current.field_name.getReferenceTable();    // Get reference table name
```

### Workflow / System Operations
```javascript
current.setAbortAction(true);              // Abort current operation (before BR)
current.setWorkflow(false);                // Skip Business Rules on this update
current.autoSysFields(false);              // Don't update sys_updated_on/by
current.update();                          // Save record (triggers BRs!)
current.insert();                          // Insert new record
current.deleteRecord();                    // Delete record
current.isNewRecord();                     // Insert operation?
current.isValidRecord();                   // Has record been loaded?
current.operation();                       // 'insert', 'update', 'delete'
current.getTableName();                    // Table name
current.getUniqueValue();                  // sys_id
current.getClassDisplayValue();            // Table label
```

---

## 4. Key Client-Side APIs

### g_form (GlideForm)
```javascript
g_form.getValue('field_name');             // Get field value
g_form.setValue('field_name', value, displayValue); // Set field value
g_form.getReference('field_name', callback);       // Get referenced record (async!)
g_form.addOption('field', value, label);   // Add choice option
g_form.removeOption('field', value);       // Remove choice option
g_form.clearOptions('field');              // Clear all options
g_form.setVisible('field', true/false);    // Show/hide field
g_form.setMandatory('field', true/false);  // Set required
g_form.setReadOnly('field', true/false);   // Set read-only
g_form.setDisabled('field', true/false);   // Disable field
g_form.addInfoMessage('text');             // Show info message
g_form.addErrorMessage('text');            // Show error message
g_form.clearMessages();                    // Clear all messages
g_form.showFieldMsg('field', 'msg', 'error'); // Field-level message
g_form.hideFieldMsg('field');              // Hide field message
g_form.flash('field', 'color', count);     // Flash field highlight
g_form.submit();                           // Submit form
g_form.save();                             // Save without redirect
g_form.getTableName();                     // Current table
g_form.getSysId();                         // Current record sys_id
g_form.isNewRecord();                      // New record?
g_form.setScope('scope_name');             // Set scope context
```

### g_user (GlideUser — Client)
```javascript
g_user.userID;                             // Current user sys_id
g_user.userName;                           // Username
g_user.firstName;                          // First name
g_user.lastName;                           // Last name
g_user.hasRole('role_name');               // Role check
g_user.hasRoles();                         // Has any role?
g_user.getClientData('key');               // Get client data set by scratchpad
```

### g_scratchpad
```javascript
// Set in Display Business Rule (server-side):
g_scratchpad.myKey = 'value';

// Read in Client Script (client-side):
var val = g_scratchpad.myKey;
```
**Use case:** Pass server data to client scripts without additional queries.

---

## 5. Task Table Hierarchy

ServiceNow's core data model extends from the `task` table:

```
task [task]
├── incident [incident]
├── problem [problem]  
├── change_request [change_request]
│   ├── change_request_imac [change_request_imac]
│   └── ... (change models)
├── sc_request [sc_request]
│   └── sc_req_item [sc_req_item]
│       └── sc_task [sc_task]
├── kb_submission [kb_submission]
├── hr_case [sn_hr_core_case]
├── csm_case [sn_customerservice_case] 
├── sn_si_incident [sn_si_incident] (Security)
└── ... (200+ extensions)
```

**Key inherited fields (from task):**
- `number`, `short_description`, `description`
- `state`, `priority`, `urgency`, `impact`
- `assignment_group`, `assigned_to`
- `opened_by`, `opened_at`, `closed_by`, `closed_at`
- `work_notes`, `comments` (journal fields)
- `sys_id`, `sys_created_on`, `sys_updated_on`

**Rule:** When querying, use the **most specific table** possible for performance.

---

## 6. CMDB Fundamentals

```
cmdb [cmdb]
└── cmdb_ci [cmdb_ci]              (Base CI class)
    ├── cmdb_ci_computer [cmdb_ci_computer]
    │   ├── cmdb_ci_server [cmdb_ci_server]
    │   ├── cmdb_ci_vm [cmdb_ci_vm]
    │   └── cmdb_ci_pc_hardware [cmdb_ci_pc_hardware]
    ├── cmdb_ci_service [cmdb_ci_service]
    │   ├── cmdb_ci_service_auto [cmdb_ci_service_auto]
    │   └── cmdb_ci_service_manual [cmdb_ci_service_manual]
    ├── cmdb_ci_appl [cmdb_ci_appl]
    ├── cmdb_ci_database [cmdb_ci_database]
    ├── cmdb_ci_network_gear [cmdb_ci_network_gear]
    └── ... (hundreds of CI types)

cmdb_rel_ci [cmdb_rel_ci]          (Relationship table)
  - parent → cmdb_ci
  - child → cmdb_ci
  - type → cmdb_rel_type
```

---

## 7. Logging Standards

### Log Levels
```javascript
gs.debug('[ClassName.method] Debug detail');    // Verbose, debug-only
gs.info('[ClassName.method] Informational');    // Normal operations
gs.warn('[ClassName.method] Warning condition'); // Potential issues
gs.error('[ClassName.method] Error occurred');  // Errors requiring attention
```

### Log Format
```
[SourceIdentifier.methodName] <Action/Status>: <Details> | <Context>

Examples:
gs.info('[ApprovalUtils.routeApproval] Routing approval for INC0012345 to group: Service Desk');
gs.error('[RESTHandler.processRequest] Invalid payload: missing required field "caller_id" | Request ID: ' + requestId);
gs.warn('[ScheduledSync.execute] Retry attempt 2/3 for external API call | Endpoint: ' + endpoint);
```

### Rules
- **Always** include the source identifier (class/script name)
- **Never** log sensitive data (passwords, SSNs, tokens, PII)
- **Never** use `gs.log()` — it writes to `syslog_app` with no level control
- Use `gs.debug()` for verbose output that should be off by default
- Include **record identifiers** (number, sys_id) for traceability

---

## 8. Update Set Awareness

### Rules
- All changes happen within an **Update Set** — know which one is current
- Before coding, verify you're in the **correct application scope and update set**
- **Never** commit changes to the "Default" update set in production
- Group related changes in a **single update set** for atomic deployment
- Name update sets descriptively: `[SCOPE] - [JIRA/RITM#] - [Description]`

### Deployment Order Matters
```
1. Tables and Fields
2. System Properties
3. Roles
4. ACLs
5. Script Includes (dependencies first)
6. Business Rules
7. Client Scripts / UI Policies
8. Flows / Workflows
9. Service Portal Widgets / UI Builder Pages
10. Scheduled Jobs
```

---

*Auto-loaded with SYSTEM.md — provides foundational ServiceNow platform knowledge.*
