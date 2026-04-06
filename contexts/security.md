# Context Module: Security — ACLs, Roles & Data Policies

> **Module:** `security.md`
> **Load when:** Designing security models, ACLs, roles, or data policies
> **Depends on:** `_base.md` (auto-loaded)

---

## 1. ACL (Access Control List) Hierarchy

```
Table-level ACL:    incident.*          → Can user access the table?
Field-level ACL:    incident.priority   → Can user access this field?
Row-level ACL:      Script condition    → Can user access THIS record?
```

ACLs evaluate **most specific first**: Field ACL → Table ACL → Wildcard.

## 2. ACL Structure

```
ACL Record:
├── Type:        record (table/field access)
├── Operation:   read | write | create | delete
├── Name:        table_name.field_name (or table_name.* for table-level)
├── Requires role: role_name(s)
├── Condition:   Encoded query filter
├── Script:      Advanced condition (return true/false)
└── Active:      true/false
```

### ACL Script Pattern
```javascript
// ACL Script — return true to GRANT access, false to DENY
// Available objects: current (the record), gs

// Example: Only allow access to records in user's assignment group
var userGroups = gs.getUser().getMyGroups();
answer = userGroups.indexOf(current.getValue('assignment_group')) >= 0;
```

## 3. Role Design

```yaml
x_acme_app.admin:       # Full application access
  contains: x_acme_app.user
  
x_acme_app.user:        # Standard user access
  contains: x_acme_app.viewer
  
x_acme_app.viewer:      # Read-only access

x_acme_app.api_user:    # API-only access (no UI)
```

**Rules:**
- Scope-prefix all custom roles: `x_vendor_app.role_name`
- Use role **hierarchy** (contains) to avoid redundant assignments
- Create **functional** roles, not per-person roles
- Never assign `admin` to service accounts

## 4. GlideRecordSecure vs GlideRecord

```javascript
// GlideRecord — bypasses ACLs (internal/trusted context ONLY)
var gr = new GlideRecord('incident');

// GlideRecordSecure — enforces ACLs (user-facing context)
var grs = new GlideRecordSecure('incident');
// Use in: REST APIs, Service Portal, any user-triggered operation
```

## 5. Data Policies

| Feature | Data Policy | UI Policy |
|---------|------------|-----------|
| Enforced server-side | ✅ | ❌ |
| Works on API/imports | ✅ | ❌ |
| Set mandatory/read-only | ✅ | ✅ |
| Complex logic | ❌ | ⚠️ (via script) |
| Bypass with role | ✅ | N/A |

**Rule:** Use **Data Policies** when the rule must apply regardless of how data enters the system (UI, API, import, workflow).

## 6. Security Checklist

- [ ] Custom roles created with scope prefix
- [ ] ACLs defined for all custom tables (read/write/create/delete)
- [ ] Field-level ACLs for sensitive fields
- [ ] GlideRecordSecure in REST APIs and Service Portal
- [ ] Input validation on all user-provided data
- [ ] No sensitive data in client-accessible scripts
- [ ] Service accounts use least-privilege roles
- [ ] API endpoints require authentication
- [ ] Cross-scope access explicitly configured

---

*Context module for ServiceNow security design.*
