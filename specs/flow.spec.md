# Spec Template: Flow Designer Flow

> Fill in with SYSTEM.md + contexts/flow-designer.md

---

```yaml
# ═══════════════════════════════════════════
# FLOW DESIGNER FLOW SPECIFICATION
# ═══════════════════════════════════════════

artifact_type: Flow
name: <Scope - Trigger Description>
scope: <x_vendor_app>

# ───────────────────────────────────────────
# TRIGGER
# ───────────────────────────────────────────

trigger:
  type: <record | schedule | application | email>
  table: <if record trigger>
  condition: <when to fire — encoded query or description>
  run_as: <system | triggering_user>

# ───────────────────────────────────────────
# FLOW LOGIC
# ───────────────────────────────────────────

description: |
  <Step-by-step description of what the flow should do.
   Include decision points and branches.>

steps:
  - step: 1
    type: <action | decision | subflow | script | wait>
    description: <What this step does>
  - step: 2
    type: <type>
    description: <Description>
    condition: <If decision, what's the branch condition>

# ───────────────────────────────────────────
# SUBFLOWS USED
# ───────────────────────────────────────────

subflows:
  - name: <Subflow name>
    purpose: <What it does>
    inputs: <Key inputs passed>

# ───────────────────────────────────────────
# ERROR HANDLING
# ───────────────────────────────────────────

error_handling:
  - on_error: <What happens on failure>
  - retry: <true | false>
  - notification: <Who gets notified on failure>

# ───────────────────────────────────────────
# ACCEPTANCE CRITERIA
# ───────────────────────────────────────────

criteria:
  - <Criterion 1>
  - <Criterion 2>
```
