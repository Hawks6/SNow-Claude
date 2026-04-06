# Knowledge Base: Scoping Rules & Cross-Scope Access

> **File:** `scoping-rules.md`
> **Purpose:** Rules for cross-scope access, Application Access, and runtime scope

---

## 1. Application Access Matrix

Configure on each custom table via **Application Access** tab:

| Setting | Description | Recommendation |
|---------|-------------|----------------|
| Can read | Other scopes query this table | Start OFF, open if needed |
| Can create | Other scopes insert records | Start OFF |
| Can update | Other scopes update records | Start OFF |
| Can delete | Other scopes delete records | Always OFF unless required |
| Allow web services | REST/SOAP can access | OFF unless API table |
| Allow configuration | Other scopes add BRs/ACLs | Usually OFF |

## 2. Script Include Accessibility

| Setting | Behavior |
|---------|----------|
| **This application scope only** | Only scripts in the same scope can call it |
| **All application scopes** | Any scope can call it — creates a public API |

**Rules:**
- Default to **This scope only**
- Only expose to **All scopes** when explicitly needed
- Document the public API contract for cross-scope SIs
- Consider a thin wrapper SI that validates cross-scope callers

## 3. Cross-Scope Calling

```javascript
// Calling Script Include in another scope
var utils = new x_other_scope.TheirUtilityClass();
var result = utils.publicMethod(params);

// Rules:
// 1. Target SI must be "Accessible from: All application scopes"
// 2. Calling user must have appropriate roles
// 3. ACLs are enforced based on the CALLING user's context
// 4. GlideRecord queries in the called SI respect the CALLING scope's table access
```

## 4. Runtime Scope Context

```
When Code Executes:
├── The script runs in its OWN scope
├── But GlideRecord ACLs evaluate against the USER's roles
├── Cross-scope SI calls: callee runs in callee's scope
├── But table access checks use the CALLING scope's permissions
└── System properties are scope-prefixed: gs.getProperty() is scope-aware
```

## 5. Common Cross-Scope Gotchas

| Gotcha | Explanation | Fix |
|--------|-------------|-----|
| "Cannot call SI from other scope" | Accessibility not set to All | Change to "All application scopes" |
| "Security constraint violation" | Table doesn't allow cross-scope | Update Application Access settings |
| SI returns undefined | Method exists but isn't public | Check `isPublic()` for Ajax; check scope |
| gs.getProperty() returns null | Property is in different scope | Use fully qualified name with scope prefix |
| ACL blocks cross-scope query | Calling scope lacks table access | Add Application Cross-Scope Access record |

## 6. Application Cross-Scope Access Records

When Scope A needs to access Scope B's table/script:

```
System Definition > Application Cross-Scope Access
├── Source Scope: x_acme_scope_a
├── Target Scope: x_acme_scope_b
├── Target Name: (table name or *)
├── Target Type: Table / Script Include
├── Operation: read / create / update / delete / execute
├── Status: Allowed
└── Active: true
```

---

*Scoping rules reference for cross-scope ServiceNow development.*
