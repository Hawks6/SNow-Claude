# Spec Template: Process Automation Designer (PAD) Playbook

> **Template:** `playbook.spec.md`
> **Load with:** `SYSTEM.md` + `contexts/playbooks.md` + `contexts/flow-designer.md`
> **Purpose:** Define a complete Process Automation Designer process before generation

---

```yaml
# ============================================================
#  SECTION 1: PROCESS IDENTITY
# ============================================================

artifact_type: PAD Process Definition

# Full process name — follows convention: <Scope Prefix> - <Noun> Process
name: "<x_yourco_app - [Process Name] Process>"

# Short description of what this process achieves
description: |
  <What business process does this Playbook guide agents through?>
  <What problem does it solve? What outcome does it produce?>

# Application scope for this process (never Global)
scope: "x_<vendor>_<app>"

# The primary driving table for this process
# Examples: sn_ss_task, sn_hr_core_case, sn_si_incident, ast_contract, sc_req_item
table: "<table_name>"

# ServiceNow release target
target_release: "Xanadu | Washington DC | Vancouver"

# ============================================================
#  SECTION 2: TRIGGER CONFIGURATION
# ============================================================

trigger:
  # Options: record_insert, record_update, record_insert_or_update, manual, application
  type: "<trigger_type>"

  # Encoded query or field conditions that activate this process
  # MUST use field values, not lane/activity names
  condition: |
    <e.g., state=1^category=procurement^approval=not requested>

  # Allow agents to manually start a new process instance?
  allow_manual_start: <true | false>

  # Allow multiple concurrent executions for the same record?
  allow_concurrent_executions: <true | false>

# ============================================================
#  SECTION 3: WORKSPACE INTEGRATION
# ============================================================

workspace:
  # The target Now Experience workspace where this Playbook will render
  # Examples: Source-to-Pay Workspace, CSM Workspace, HR Agent Workspace, Custom Workspace
  target_workspace: "<Workspace Name>"

  # Is this a custom workspace (built in UI Builder) or OOTB?
  workspace_type: "<custom | ootb>"

  # If custom: what record page and tab does the Playbook embed in?
  ui_builder:
    page: "<e.g., sn_ss_task record page>"
    container: "<e.g., Tab: Playbook, or right sidebar panel>"
    show_activity_log: <true | false>
    collapsible: <true | false>

  # If extending an OOTB workspace (e.g., S2P):
  ootb_base_process: "<Name of OOTB process being forked, or 'none'>"

# ============================================================
#  SECTION 4: LANE DEFINITIONS
# ============================================================

# Define each lane in execution order (or parallel grouping)
lanes:
  - name: "<Lane 1 Name>"             # Human-readable, visible to agents in Playbook
    execution_type: "sequential"       # sequential | parallel
    condition: |                        # Leave blank if this lane always runs
      <Field-based condition e.g., contract_value > 10000>
    entry_criteria: "<When does this lane become active?>"
    exit_criteria: "<When is this lane considered complete?>"
    responsible_role: "<e.g., Procurement Agent, Supplier Manager>"
    activities:
      - name: "<Activity 1 Name>"
        type: "<automated | manual | approval | instructions | ad_hoc>"
        description: "<What does this activity do?>"
        backing_subflow: "<x_scope.subflow_name or 'new'>"
        inputs:
          - name: "<input_var_name>"
            type: "<Reference | String | Boolean | Integer>"
            source: "<record field, previous activity output, or system property>"
        outputs:
          - name: "<output_var_name>"
            type: "<Boolean | String>"
            description: "<What does this output represent?>"
        completion_condition: "<When is this activity considered done?>"
        required: <true | false>

  - name: "<Lane 2 Name>"
    execution_type: "sequential"
    condition: ""
    activities:
      - name: "<Activity Name>"
        type: "<automated | manual | approval>"
        description: "<Description>"
        backing_subflow: "<subflow name or 'new'>"
        inputs: []
        outputs: []
        completion_condition: "<condition>"
        required: true

  # Add more lanes as needed...

# ============================================================
#  SECTION 5: PROCESS VARIABLES
# ============================================================

# Shared variables available across all lanes/activities
process_variables:
  - name: "<variable_name>"           # snake_case
    type: "<String | Boolean | Integer | Reference | JSON>"
    description: "<What does this variable store?>"
    initial_value: "<optional default>"

# ============================================================
#  SECTION 6: SECURITY & ACCESS
# ============================================================

security:
  # Roles that can VIEW this process in PAD
  author_roles:
    - "sn_pad.author"
    - "<x_yourco_app.custom_role>"

  # Roles that can EXECUTE / interact with the Playbook in workspace
  executor_roles:
    - "<e.g., x_yourco_s2p.procurement_agent>"
    - "<e.g., sn_sow_read>"

  # Are there activity-level role restrictions? Describe them.
  activity_restrictions: |
    <e.g., "Sanctions Screening" activity only visible to x_yourco_s2p.compliance_officer>

# ============================================================
#  SECTION 7: INTEGRATION POINTS
# ============================================================

integrations:
  # External systems or ServiceNow modules this process connects to
  - system: "<e.g., SAP Ariba, DocuSign, ServiceNow ITSM, Email>"
    purpose: "<What data is exchanged?>"
    integration_type: "<REST | IntegrationHub Spoke | GlideRecord | Email>"
    credential_alias: "<Connection & Credential Alias name>"

# ============================================================
#  SECTION 8: CROSS-TEAM HANDOFFS
# ============================================================

handoffs:
  # Document every moment when ownership transfers between teams/roles
  - from_lane: "<Lane Name>"
    to_lane: "<Lane Name>"
    handoff_trigger: "<What causes the handoff? e.g., Activity X completed>"
    notification_method: "<Email notification, Assignment, Workspace alert>"
    sla_target: "<Time SLA for the receiving team, e.g., 4 business hours>"

# ============================================================
#  SECTION 9: ACCEPTANCE CRITERIA
# ============================================================

acceptance_criteria:
  - "<criterion 1: e.g., Playbook launches automatically when a new sn_ss_task record with category='procurement' is created>"
  - "<criterion 2: e.g., Agents cannot advance past Lane 3 without completing all required activities in Lane 2>"
  - "<criterion 3: e.g., Compliance lane is only visible for contracts > $50,000>"
  - "<criterion 4: e.g., Process execution is logged in sn_pad_process_execution with correct record linkage>"
  - "<criterion 5: e.g., Manual override activity available to Procurement Managers as ad-hoc option>"

# ============================================================
#  SECTION 10: ADDITIONAL NOTES
# ============================================================

notes: |
  <Any additional context: known constraints, upstream dependencies,
  related processes, or platform version-specific considerations.>

# ============================================================
#  GENERATION INSTRUCTIONS (for Claude)
# ============================================================
# When all fields above are filled, Claude will generate:
#   1. Process Definition configuration summary (PAD setup guide)
#   2. Lane-by-lane activity breakdown with Subflow stubs
#   3. UI Builder integration steps (if workspace section is filled)
#   4. Activity Definition templates for manual creation in PAD
#   5. Backing Subflow structure for each automated activity
#   6. Quality Gate assessment
#   7. Deployment notes & plugin dependencies
#   8. Naming convention validation
```

---

## How to Use This Spec

1. **Copy** this template and fill in all `<placeholder>` fields.
2. **Load into Claude:** `SYSTEM.md` + `contexts/playbooks.md` + `contexts/flow-designer.md` + this filled spec.
3. **Command:** `"Generate a PAD Playbook configuration based on this spec using /developer mode."`
4. Claude will output: process setup guide, lane/activity breakdown, Subflow stubs, UI Builder steps, and deployment notes.

---

## Quick-Start Example: Source-to-Pay Supplier Onboarding

```yaml
artifact_type: PAD Process Definition
name: "x_yourco_s2p - Supplier Onboarding Process"
description: |
  Guides procurement agents through the end-to-end onboarding of a new supplier,
  including document collection, compliance screening, legal review, and final activation.
scope: "x_yourco_s2p"
table: "sn_ss_task"
target_release: "Xanadu"

trigger:
  type: record_insert
  condition: "category=supplier_onboarding^state=1"
  allow_manual_start: true
  allow_concurrent_executions: false

workspace:
  target_workspace: "Source-to-Pay Workspace"
  workspace_type: ootb
  ui_builder:
    page: "sn_ss_task record page"
    container: "Playbook Tab"
    show_activity_log: true
    collapsible: false
  ootb_base_process: "Supplier Onboarding (sn_spo)"

lanes:
  - name: "Intake"
    execution_type: sequential
    condition: ""
    activities:
      - name: "Collect Supplier Information"
        type: manual
        description: "Agent completes the supplier profile form"
        backing_subflow: "x_yourco_s2p.collect_supplier_info_subflow"

  - name: "Compliance Screening"
    execution_type: parallel
    condition: "contract_value > 50000"
    activities:
      - name: "Sanctions Screening"
        type: automated
        description: "Calls IntegrationHub spoke to run supplier against sanctions list"
        backing_subflow: "x_yourco_s2p.sanctions_check_subflow"

  - name: "Legal Review"
    execution_type: sequential
    activities:
      - name: "Legal Sign-Off"
        type: approval
        description: "Legal team approves supplier terms"
        backing_subflow: new

  - name: "Closure"
    execution_type: sequential
    activities:
      - name: "Activate Supplier"
        type: automated
        description: "Updates supplier record to Active and notifies procurement"
        backing_subflow: "x_yourco_s2p.activate_supplier_subflow"
```

---

*Spec template for Process Automation Designer (PAD) Playbooks — define first, build right.*
