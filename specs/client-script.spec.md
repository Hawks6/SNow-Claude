# Spec Template: Client Script

> Fill in with SYSTEM.md + contexts/client-scripts.md

---

```yaml
# ═══════════════════════════════════════════
# CLIENT SCRIPT SPECIFICATION
# ═══════════════════════════════════════════

artifact_type: Client Script
table: <target_table>
name: <Table - Type Description>
type: <onLoad | onChange | onSubmit | onCellEdit>
field_name: <field triggering onChange — leave blank for onLoad/onSubmit>
scope: <x_vendor_app>
ui_type: <desktop | mobile | both>
is_catalog: <true | false>

# ───────────────────────────────────────────
# REQUIREMENTS
# ───────────────────────────────────────────

description: |
  <What should this client script do?>

criteria:
  - <Criterion 1>
  - <Criterion 2>

# ───────────────────────────────────────────
# SERVER DATA NEEDED
# ───────────────────────────────────────────

glideajax_calls:
  - script_include: <ClassName>
    method: <methodName>
    purpose: <What data is fetched>

scratchpad_data:
  - key: <scratchpad key>
    set_by: <Display Business Rule name>
    purpose: <What the data is for>

# ───────────────────────────────────────────
# FIELDS AFFECTED
# ───────────────────────────────────────────

fields_modified:
  - field: <field_name>
    action: <setValue | setVisible | setMandatory | setReadOnly>
    condition: <when this action applies>
```
