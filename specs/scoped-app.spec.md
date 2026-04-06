# Spec Template: Scoped Application

> Fill in with SYSTEM.md — comprehensive application spec

---

```yaml
# ═══════════════════════════════════════════
# SCOPED APPLICATION SPECIFICATION
# ═══════════════════════════════════════════

artifact_type: Scoped Application
name: <Application Display Name>
scope: <x_vendor_appname>
version: <1.0.0>
description: |
  <What this application does, business problem it solves>

# ───────────────────────────────────────────
# DATA MODEL
# ───────────────────────────────────────────

tables:
  - name: <x_vendor_app_tablename>
    label: <Display Label>
    extends: <task | cmdb_ci | none>
    description: <Purpose of this table>
    fields:
      - name: <u_field_name>
        type: <string | integer | reference | choice | boolean | date | journal>
        label: <Display Label>
        max_length: <if string>
        reference_table: <if reference>
        mandatory: <true | false>
        choices:
          - value: <value>
            label: <Display Label>

# ───────────────────────────────────────────
# BUSINESS LOGIC
# ───────────────────────────────────────────

business_rules:
  - <Brief description — will generate individual BR specs>

script_includes:
  - <Brief description — will generate individual SI specs>

flows:
  - <Brief description of automated workflows>

# ───────────────────────────────────────────
# USER INTERFACE
# ───────────────────────────────────────────

forms:
  - table: <table_name>
    sections: <section layout description>

lists:
  - table: <table_name>
    columns: <visible columns>

# ───────────────────────────────────────────
# SECURITY MODEL
# ───────────────────────────────────────────

roles:
  - name: <x_vendor_app.role_name>
    description: <What this role can do>
    contains_roles: <inherited roles>

acls:
  - table: <table_name>
    operation: <read | write | create | delete>
    role: <role required>
    conditions: <additional script conditions if any>

application_access:
  - table: <table_name>
    can_read: <true | false>
    can_create: <true | false>
    can_update: <true | false>
    can_delete: <true | false>

# ───────────────────────────────────────────
# INTEGRATIONS
# ───────────────────────────────────────────

rest_apis:
  - <Inbound/outbound API requirements>

# ───────────────────────────────────────────
# ACCEPTANCE CRITERIA
# ───────────────────────────────────────────

criteria:
  - <Application-level acceptance criteria>
```
