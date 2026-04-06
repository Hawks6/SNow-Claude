# Spec Template: Script Include

> Fill in this template and paste it with SYSTEM.md + contexts/script-includes.md

---

```yaml
# ═══════════════════════════════════════════
# SCRIPT INCLUDE SPECIFICATION
# ═══════════════════════════════════════════

artifact_type: Script Include
name: <PascalCaseClassName>
scope: <x_vendor_app>
client_callable: <true | false>
accessible_from: <this_scope | all_scopes>

# ───────────────────────────────────────────
# CLASS PURPOSE
# ───────────────────────────────────────────

description: |
  <What does this class do? What problem does it solve?
   Include the business domain and key responsibilities.>

# ───────────────────────────────────────────
# METHODS
# ───────────────────────────────────────────

methods:
  - name: <methodName>
    description: <What this method does>
    params:
      - name: <paramName>
        type: <string | number | boolean | GlideRecord | Object>
        description: <What this parameter represents>
        required: <true | false>
    returns:
      type: <string | boolean | Object | Array | void>
      description: <What is returned and its structure>

  - name: <anotherMethod>
    description: <Description>
    params: []
    returns:
      type: <type>

# ───────────────────────────────────────────
# ACCEPTANCE CRITERIA
# ───────────────────────────────────────────

criteria:
  - <Criterion 1>
  - <Criterion 2>

# ───────────────────────────────────────────
# DEPENDENCIES
# ───────────────────────────────────────────

depends_on:
  - <Other Script Includes this calls>
tables_accessed:
  - <Tables queried or modified>
system_properties:
  - <Properties read>

# ───────────────────────────────────────────
# SECURITY
# ───────────────────────────────────────────

data_sensitivity: <low | medium | high>
input_validation_notes: <Special validation needs>
```

---

## 📘 Field Guide

| Field | Required | Notes |
|-------|:--------:|-------|
| `name` | ✅ | PascalCase, must match class name |
| `description` | ✅ | Purpose and responsibilities |
| `methods` | ✅ | At least one method defined |
| `client_callable` | ✅ | If true, generates AbstractAjaxProcessor pattern |
| `accessible_from` | ⬜ | Default: this_scope |
| `criteria` | ✅ | Become ATF test cases |
| `tables_accessed` | ⬜ | Helps Claude pick correct APIs |
