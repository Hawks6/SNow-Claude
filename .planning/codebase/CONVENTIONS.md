# Coding Conventions

This document outlines the coding standards and conventions enforced by SNow-Claude for ServiceNow development.

## 1. Naming Standards

| Artifact | Pattern | Example |
|---|---|---|
| Scoped App | `x_<vendor>_<app>` | `x_acme_itsm_ext` |
| Table | `x_<vendor>_<app>_<name>` | `x_acme_itsm_ext_approval_matrix` |
| Field | `u_<descriptive_name>` (custom) | `u_escalation_tier` |
| Business Rule | `<Table> - <Action> <Description>` | `Incident - Before Set Priority` |
| Script Include | `PascalCase` class name | `ApprovalMatrixUtils` |
| Client Script | `<Table> - <Type> <Description>` | `Incident - onChange Set Category Fields` |
| REST API | `<App> API` -> `/<version>/<resource>` | `ITSM Extension API` -> `/v1/approvals` |
| Flow | `<Scope> - <Trigger> <Description>` | `ITSM Ext - Record Created Approval Flow` |
| System Property | `x_<vendor>_<app>.<category>.<name>` | `x_acme_itsm_ext.approval.timeout_days` |
| Role | `x_<vendor>_<app>.<role_name>` | `x_acme_itsm_ext.approver` |

## 2. JavaScript Standards (Rhino Engine)
- **Variables**: Use `var` only. `let` and `const` have inconsistent support in older ServiceNow versions (Rhino engine).
- **Comparison**: Always use strict equality (`===` and `!==`).
- **Structure**:
    - Wrap server scripts in an **IIFE** `(function(){ ... })();` where appropriate.
    - Use **early returns** to avoid deep nesting.
    - Keep functions under ~50 lines.
    - Use **JSDoc** for all public methods.
- **Formatting**: Always use semicolons and 2 or 4 space indentation.

## 3. Logging & Error Handling
- **Structured Logging**: Always include the **source identifier** in brackets.
    - `gs.info('[ClassName.methodName] Message');`
- **Error Handling**: Use `try/catch` blocks for complex logic, integrations, and database operations.
    - Log exceptions with `ex.getMessage()` and record identifiers.
    - Pattern: `gs.error('[ClassName.methodName] Error: ' + ex.getMessage() + ' | Record: ' + current.getUniqueValue());`

## 4. Security & Performance
- **No Hardcoding**: Never hardcode `sys_id`, URLs, or credentials. Use System Properties or Aliases.
- **Data Access**: 
    - Use `GlideRecordSecure` for user-facing or REST contexts.
    - Use `getValue('field')` over `gr.field` for performance and type safety.
- **Queries**: 
    - Ensure queries use indexed fields.
    - Always use `setLimit()` for single/fixed result queries.
    - Use `GlideAggregate` for all counting/grouping operations.

