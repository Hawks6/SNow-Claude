# Spec Template: Service Portal Widget

> Fill in with SYSTEM.md + contexts/service-portal.md

---

```yaml
# ═══════════════════════════════════════════
# SERVICE PORTAL WIDGET SPECIFICATION
# ═══════════════════════════════════════════

artifact_type: Service Portal Widget
name: <Widget Display Name>
id: <widget-id-lowercase-dashes>
scope: <x_vendor_app>

# ───────────────────────────────────────────
# REQUIREMENTS
# ───────────────────────────────────────────

description: |
  <What this widget displays/does>

data_source:
  table: <primary table queried>
  filter: <encoded query or description>
  fields_needed:
    - <field1>
    - <field2>
  max_records: <number>

# ───────────────────────────────────────────
# USER INTERACTIONS
# ───────────────────────────────────────────

actions:
  - action: <click/submit/filter>
    description: <What happens>
    server_method: <server.get or server.update>

# ───────────────────────────────────────────
# INSTANCE OPTIONS
# ───────────────────────────────────────────

options:
  - name: <option_name>
    type: <string | boolean | integer | reference>
    label: <Display Label>
    default: <default value>

# ───────────────────────────────────────────
# ACCEPTANCE CRITERIA
# ───────────────────────────────────────────

criteria:
  - <Criterion 1>
  - <Criterion 2>
  - Handles empty data state gracefully
  - Respects user's ACL permissions
```
