# Spec Template: Business Rule

> Fill in this template and paste it with SYSTEM.md + contexts/business-rules.md

---

```yaml
# ═══════════════════════════════════════════
# BUSINESS RULE SPECIFICATION
# ═══════════════════════════════════════════

artifact_type: Business Rule
table: <target_table>
name: <Table - When Description>
when: <before | after | async | display>
operation: <insert, update, delete, query>
order: <100>
active: true
condition: <encoded query or plain description>
scope: <x_vendor_app>

# ───────────────────────────────────────────
# REQUIREMENTS
# ───────────────────────────────────────────

description: |
  <What should this Business Rule do? Be specific.
   Include the business context and user story if available.>

# ───────────────────────────────────────────
# ACCEPTANCE CRITERIA
# ───────────────────────────────────────────

criteria:
  - <Criterion 1: specific testable outcome>
  - <Criterion 2: specific testable outcome>
  - <Criterion 3: edge case handling>

# ───────────────────────────────────────────
# DEPENDENCIES & CONTEXT
# ───────────────────────────────────────────

uses_script_includes:
  - <ScriptIncludeName — describe what it provides>

related_business_rules:
  - <Other BRs on same table to be aware of>

system_properties:
  - <property.name — what it controls>

# ───────────────────────────────────────────
# SECURITY & PERMISSIONS
# ───────────────────────────────────────────

role_requirements: <roles that should/shouldn't trigger this>
data_sensitivity: <low | medium | high>

# ───────────────────────────────────────────
# CONSTRAINTS
# ───────────────────────────────────────────

performance_notes: <any volume/frequency concerns>
mobile_compatible: <yes | no | not applicable>
agent_workspace_compatible: <yes | no | not applicable>
```

---

## 📘 Field Guide

| Field | Required | Notes |
|-------|:--------:|-------|
| `table` | ✅ | Use most specific table, not parent |
| `name` | ✅ | Follow `<Table> - <When> <Description>` |
| `when` | ✅ | See decision guide in context module |
| `operation` | ✅ | Only check operations you need |
| `description` | ✅ | Detailed plain-language requirements |
| `criteria` | ✅ | Become ATF test cases |
| `order` | ⬜ | Default 100, adjust for sequencing |
| `condition` | ⬜ | Use condition builder when possible |
| `uses_script_includes` | ⬜ | Claude will generate calls |
| `role_requirements` | ⬜ | For security gate validation |
