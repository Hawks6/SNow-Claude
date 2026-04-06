# Spec Template: External Integration

> Fill in with SYSTEM.md + contexts/rest-api.md + contexts/flow-designer.md

---

```yaml
# ═══════════════════════════════════════════
# INTEGRATION SPECIFICATION
# ═══════════════════════════════════════════

artifact_type: Integration
name: <Integration Display Name>
scope: <x_vendor_app>
direction: <inbound | outbound | bidirectional>
protocol: <REST | SOAP | JDBC | Email | File>
external_system: <Name of the external system>

# ───────────────────────────────────────────
# CONNECTION
# ───────────────────────────────────────────

connection:
  endpoint: <Base URL or connection description>
  auth_method: <basic | oauth2 | api_key | certificate>
  connection_alias: <Alias name to create>
  credential_alias: <Credential alias name>
  mid_server_required: <true | false>

# ───────────────────────────────────────────
# DATA MAPPING
# ───────────────────────────────────────────

data_mapping:
  servicenow_table: <Table being synced>
  external_entity: <External system entity>
  field_mapping:
    - sn_field: <ServiceNow field>
      external_field: <External field>
      direction: <in | out | both>
      transform: <Any transformation needed>

# ───────────────────────────────────────────
# TRIGGER & FREQUENCY
# ───────────────────────────────────────────

trigger:
  type: <event | schedule | real_time | on_demand>
  frequency: <If scheduled — cron or interval>
  batch_size: <Records per execution>

# ───────────────────────────────────────────
# ERROR HANDLING
# ───────────────────────────────────────────

error_handling:
  retry_count: <number>
  retry_interval: <seconds>
  on_failure: <log | notify | queue_retry | skip>
  error_table: <If custom error logging table>

# ───────────────────────────────────────────
# ACCEPTANCE CRITERIA
# ───────────────────────────────────────────

criteria:
  - <Criterion 1>
  - <Criterion 2>
  - Handles auth token expiration gracefully
  - Handles external system downtime
  - Logs all integration attempts with status
```
