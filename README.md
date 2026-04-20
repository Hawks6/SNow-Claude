<p align="center">
  <h1 align="center">❄️ SNow-Claude</h1>
  <p align="center">
    <strong>A lightweight, powerful meta-prompting & context engineering system<br>for ServiceNow development with Claude AI</strong>
  </p>
  <p align="center">
    <a href="#-installation">Installation</a> •
    <a href="#-quickstart">Quickstart</a> •
    <a href="#-how-it-works">How It Works</a> •
    <a href="#-file-reference">File Reference</a> •
    <a href="#-examples">Examples</a> •
    <a href="#-customization">Customization</a> •
    <a href="#-contributing">Contributing</a>
  </p>
  <p align="center">
    <img src="https://img.shields.io/badge/ServiceNow-Washington_DC_%2F_Xanadu-4DB33D?style=flat-square" alt="ServiceNow Version">
    <img src="https://img.shields.io/badge/Claude-Compatible-7C3AED?style=flat-square" alt="Claude Compatible">
    <img src="https://img.shields.io/badge/Files-36-blue?style=flat-square" alt="Files">
    <img src="https://img.shields.io/badge/Dependencies-Zero-brightgreen?style=flat-square" alt="No Dependencies">
    <img src="https://img.shields.io/badge/License-MIT-yellow?style=flat-square" alt="License">
  </p>
</p>

---

## The Problem

You ask Claude to write a ServiceNow Business Rule. You get back generic JavaScript that:

- ❌ Uses `let`/`const` (Rhino engine doesn't fully support ES6)
- ❌ Calls `GlideRecord` on the client side (synchronous, insecure)
- ❌ Hardcodes sys_ids that break across instances
- ❌ Skips input validation, error handling, and logging
- ❌ Ignores scoped application boundaries
- ❌ Has no idea what `current.setAbortAction(true)` does

**SNow-Claude fixes this.** Paste our context files into Claude and get production-grade ServiceNow code — every time.

---

## The Solution

SNow-Claude is a collection of **composable markdown files** — no dependencies, no tooling, no npm install. Just copy → paste → build.

```
┌─────────────────────────────────────────────────────────┐
│                    SYSTEM.md                             │
│            The Brain — Expert Persona,                   │
│         Coding Standards, Quality Gates                  │
├─────────────┬─────────────┬──────────────┬──────────────┤
│  contexts/  │   specs/    │  prompts/    │ knowledge/   │
│             │             │              │              │
│ 13 deep     │ 9 fill-in   │ 4 role       │ 5 reference  │
│ knowledge   │ spec        │ modes        │ documents    │
│ modules     │ templates   │              │              │
│             │             │              │              │
│ Load per    │ Spec →      │ /architect   │ API patterns │
│ task        │ production  │ /developer   │ Anti-patterns│
│             │ code        │ /reviewer    │ Naming       │
│             │             │ /debugger    │ Scoping      │
└─────────────┴─────────────┴──────────────┴──────────────┘
```

---

## ✨ What You Get

| Without SNow-Claude | With SNow-Claude |
|---------------------|------------------|
| Generic JavaScript | Platform-aware scoped app code |
| No naming conventions | `<Table> - <When> <Description>` everywhere |
| Hardcoded sys_ids | `gs.getProperty()` + system properties |
| Missing error handling | try/catch + structured logging with source IDs |
| No input validation | `gs.nil()` checks, type validation, GlideRecordSecure |
| No naming conventions | `<Table> - <Action> <Description>` everywhere |
| Silent about bad practices | 25-point quality gate with pass/fail report |

### Before & After

<details>
<summary><strong>❌ Without SNow-Claude</strong> — What Claude typically generates</summary>

```javascript
// "Create a business rule that sets priority based on VIP caller"

var gr = new GlideRecord('sys_user');
gr.addQuery('sys_id', current.caller_id);
gr.query();
if (gr.next()) {
    if (gr.vip == 'true') {
        current.priority = 1;
    }
}
```

**Problems:** No timing specified, no operation guard, uses dot-notation instead of `getValue()`, no logging, no error handling, no null checks, no naming convention, no scope awareness.

</details>

<details>
<summary><strong>✅ With SNow-Claude</strong> — Production-grade output</summary>

```javascript
/**
 * Business Rule: Incident - Before Set VIP Priority
 * Table: incident | When: before | Insert: true, Update: true
 * Condition: caller_id changes
 * 
 * @description Automatically elevates priority for VIP callers.
 *              Delegates lookup to avoid unnecessary queries on non-VIP callers.
 */
(function executeRule(current, previous) {

    var LOG_PREFIX = '[Incident - Before Set VIP Priority]';

    // Only process when caller changes
    if (!current.caller_id.changes()) {
        return;
    }

    var callerId = current.getValue('caller_id');
    if (gs.nil(callerId)) {
        return;
    }

    var grCaller = new GlideRecord('sys_user');
    if (grCaller.get(callerId)) {
        if (grCaller.getValue('vip') === 'true') {
            current.setValue('priority', '1');
            current.setValue('urgency', '1');
            gs.info(LOG_PREFIX + ' VIP caller detected, priority elevated for: ' + 
                    current.getValue('number'));
        }
    } else {
        gs.warn(LOG_PREFIX + ' Caller not found: ' + callerId);
    }

})(current, previous);
```

**Includes:** Proper naming, JSDoc, IIFE wrapper, change detection, null checks, `getValue()`, structured logging, graceful handling of missing caller.

</details>

---

## 📦 Installation

### Prerequisites

- A **Claude account** (free or Pro) at [claude.ai](https://claude.ai) — or access via Claude API / Claude Desktop
- That's it. No npm, no pip, no build tools. SNow-Claude is just markdown files.

### Option 1: Clone the Repository

```bash
git clone https://github.com/Hawks6/SNow-Claude.git
cd SNow-Claude
```

### Option 2: Download as ZIP

1. Click the green **`<> Code`** button at the top of this GitHub page
2. Select **Download ZIP**
3. Extract the ZIP to any folder on your machine

### What You Get

After cloning or extracting, you'll have this folder structure:

```
snow-claude/
├── SYSTEM.md                    ← 🧠 The brain — start here
├── QUICKSTART.md                ← ⚡ 5-minute guide
├── README.md                    ← 📖 You're reading it
├── LICENSE                      ← 📄 MIT License
│
├── contexts/                    ← 📚 Knowledge modules (load per task)
│   ├── _base.md                 ←    Always needed (core platform)
│   ├── business-rules.md        ←    Business Rule patterns
│   ├── script-includes.md       ←    Script Include patterns
│   ├── client-scripts.md        ←    Client Scripts & UI Policies
│   ├── gliderecord.md           ←    GlideRecord / GlideAggregate
│   ├── flow-designer.md         ←    Flow Designer & IntegrationHub
│   ├── playbooks.md             ←    Process Automation Designer & Playbooks ✨NEW
│   ├── service-portal.md        ←    Service Portal widgets
│   ├── now-experience.md        ←    UI Builder / Now Experience
│   ├── rest-api.md              ←    Scripted REST APIs
│   ├── atf.md                   ←    Automated Test Framework
│   ├── security.md              ←    ACLs, roles, data policies
│   ├── performance.md           ←    Query optimization
│   └── update-sets.md           ←    Update set management
│
├── specs/                       ← 📝 Fill-in-the-blank spec templates
│   ├── _spec-guide.md           ←    How specs work
│   ├── business-rule.spec.md
│   ├── script-include.spec.md
│   ├── client-script.spec.md
│   ├── rest-endpoint.spec.md
│   ├── widget.spec.md
│   ├── flow.spec.md
│   ├── playbook.spec.md         ←    PAD Playbook / Process Automation Designer ✨NEW
│   ├── integration.spec.md
│   └── scoped-app.spec.md
│
├── prompts/                     ← 🎭 Role-based meta-prompts
│   ├── architect.md             ←    Design & architecture mode
│   ├── developer.md             ←    Code generation mode
│   ├── reviewer.md              ←    Code review mode
│   └── debugger.md              ←    Debugging mode
│
└── knowledge/                   ← 📖 Reference material
    ├── api-patterns.md          ←    15 most-used API patterns
    ├── anti-patterns.md         ←    Common mistakes & fixes
    ├── naming-conventions.md    ←    Naming standards
    ├── scoping-rules.md         ←    Cross-scope access rules
    └── table-reference.md       ←    Key system tables
```

> **💡 Tip:** You don't need all 34 files for every task. Most of the time you'll use `SYSTEM.md` + 1-2 context modules. Think of it as a toolbox — grab what you need.

---

## 🚀 Quickstart

### Step 1: Get the Files
Clone or download the repo (see [Installation](#-installation) above).

### Step 2: Choose Your Setup

#### Option A: Claude Projects (Recommended — Set Up Once, Use Forever)

Claude Projects let you attach files as persistent knowledge. Every conversation in the project automatically has access to your SNow-Claude context.

1. Go to [claude.ai](https://claude.ai) and click **"Create Project"**
2. Name it something like `ServiceNow Development`
3. Click **"Add project knowledge"** and upload these files from your cloned repo:

   **Always upload:**
   - `SYSTEM.md`
   - `contexts/_base.md`

   **Upload based on your daily work** (pick 3-4 to start):
   - `contexts/business-rules.md`
   - `contexts/script-includes.md`
   - `contexts/gliderecord.md`
   - `contexts/client-scripts.md`

   **Highly recommended reference material:**
   - `knowledge/anti-patterns.md`
   - `knowledge/naming-conventions.md`

   **Optional (Based on your workflow):**
   - `prompts/story-analyzer.md` (If you frequently map agile stories to technical designs)
   - `specs/user-story.spec.md` (If you want Claude to know the exact format required for stories)

4. **Start a new conversation** inside the project
5. Ask Claude to build something — it already has full ServiceNow expertise loaded

> **💡 You can always add more files later.** Working on REST APIs next week? Upload `contexts/rest-api.md` to the project.

#### Option B: Per-Conversation (Quick & Flexible — No Setup Required)

For one-off tasks or if you don't have Claude Pro:

1. **Open** `SYSTEM.md` in a text editor
2. **Select all** and **copy** (Ctrl+A → Ctrl+C)
3. **Paste** into a new Claude conversation as your first message
4. **Open** the relevant context module (e.g., `contexts/business-rules.md`)
5. **Copy and paste** that too, right after
6. **Now ask Claude** to build your ServiceNow artifact

```
📋 Step 1 — Paste: SYSTEM.md
📋 Step 2 — Paste: contexts/business-rules.md
📋 Step 3 — Your request:

"Create a before Business Rule on incident that validates resolution 
requirements when state changes to Resolved:
- Resolution code is filled
- Close notes are at least 20 chars  
- P1 incidents must have a linked problem"
```

Claude will generate the complete Business Rule with naming conventions, quality gate results, and deployment notes.

### Step 3: Level Up with Specs (Optional)

For structured, repeatable code generation:

1. Open a spec template from `specs/` (e.g., `business-rule.spec.md`)
2. Fill in the `<placeholder>` fields with your requirements
3. Paste the filled spec into your Claude conversation (after SYSTEM.md + context)
4. Claude generates production-ready code matching your exact specification

### Step 4: Switch Roles (Optional)

Paste a role prompt from `prompts/` to change Claude's behavior:

| File to Paste | What Changes |
|---------------|-------------|
| `prompts/architect.md` | Claude evaluates design options before coding |
| `prompts/story-analyzer.md` | Claude acts as an architect to map user stories to technical designs |
| `prompts/developer.md` | Claude generates code from specs with full output format |
| `prompts/reviewer.md` | Claude reviews your existing code for issues |
| `prompts/debugger.md` | Claude systematically diagnoses your ServiceNow problems |

---

## 🔧 How It Works

SNow-Claude is built on three core ideas:

### 1. Composable Context Modules
Don't dump 50KB of context into every conversation. Load **only what you need**:

```
Building a Business Rule?     → Load business-rules.md
Need GlideRecord queries?     → Add gliderecord.md
Worried about performance?    → Add performance.md
```

Modules can be combined freely. They're designed to complement, not conflict.

### 2. Spec-Driven Development
Fill in a structured spec template → get production-ready code:

```yaml
artifact_type: Business Rule
table: incident
name: Incident - Before Validate Resolution
when: before
operation: update
condition: state changes to 6 (Resolved)
scope: x_acme_itsm_ext

description: |
  Validate that resolution requirements are met before allowing 
  an incident to move to Resolved state.

criteria:
  - Blocks if resolution_code is empty
  - Blocks if close_notes < 20 characters
  - Blocks P1 resolution without linked problem
  - Shows specific error messages per validation failure
  - Admin users bypass all checks
```

Claude reads the spec, applies all context modules, runs quality gates, and delivers deployable code.

### 3. Role-Based Meta-Prompts
Switch Claude's behavior mode for different stages of work:

| Command | Mode | Best For |
|---------|------|----------|
| `/architect` | 🏗️ Architect | "Should I use a Flow or a Business Rule?" |
| `/developer` | 💻 Developer | "Build this Script Include from spec" |
| `/reviewer` | 🔍 Reviewer | "Review this code for anti-patterns" |
| `/debugger` | 🐛 Debugger | "My Business Rule isn't firing" |

---

## 📁 File Reference

### Core Files

| File | What It Does |
|------|-------------|
| [`SYSTEM.md`](SYSTEM.md) | **The brain.** Expert persona, coding standards, 25-point quality gate, response format. Always load this first. |
| [`QUICKSTART.md`](QUICKSTART.md) | 5-minute setup guide with examples |

### Context Modules — `contexts/`

Composable knowledge modules. Load per task.

| Module | Lines | Load When You're Working On |
|--------|:-----:|---------------------------|
| [`_base.md`](contexts/_base.md) | ~350 | **Always** — core platform APIs, scoped app model, table hierarchy |
| [`business-rules.md`](contexts/business-rules.md) | ~300 | Business Rules — timing, patterns, anti-patterns, performance |
| [`script-includes.md`](contexts/script-includes.md) | ~160 | Script Includes — prototype pattern, GlideAjax, cross-scope |
| [`client-scripts.md`](contexts/client-scripts.md) | ~150 | Client Scripts, UI Policies, catalog scripts |
| [`gliderecord.md`](contexts/gliderecord.md) | ~180 | GlideRecord, GlideAggregate, GlideRecordSecure |
| [`flow-designer.md`](contexts/flow-designer.md) | ~100 | Flows, Subflows, Actions, IntegrationHub |
| [`playbooks.md`](contexts/playbooks.md) | ~300 | **PAD & Playbooks** — process design, lane/activity patterns, workspace embedding, S2P patterns |
| [`service-portal.md`](contexts/service-portal.md) | ~120 | Service Portal widgets (AngularJS) |
| [`now-experience.md`](contexts/now-experience.md) | ~100 | UI Builder / Now Experience components |
| [`rest-api.md`](contexts/rest-api.md) | ~130 | Scripted REST APIs, outbound integrations |
| [`atf.md`](contexts/atf.md) | ~90 | Automated Test Framework |
| [`security.md`](contexts/security.md) | ~100 | ACLs, roles, data policies, GlideRecordSecure |
| [`performance.md`](contexts/performance.md) | ~100 | Query optimization, caching, batch processing |
| [`update-sets.md`](contexts/update-sets.md) | ~70 | Update set management, deployment order |

### Spec Templates — `specs/`

Fill-in-the-blank YAML templates. Complete one → paste it with SYSTEM.md + context → get code.

| Template | Generates |
|----------|-----------|
| [`business-rule.spec.md`](specs/business-rule.spec.md) | Business Rule with full configuration |
| [`script-include.spec.md`](specs/script-include.spec.md) | Script Include class with methods |
| [`client-script.spec.md`](specs/client-script.spec.md) | Client Script with GlideAjax calls |
| [`user-story.spec.md`](specs/user-story.spec.md) | Agile user story definition for the `/story` skill |
| [`rest-endpoint.spec.md`](specs/rest-endpoint.spec.md) | Scripted REST API with resources |
| [`widget.spec.md`](specs/widget.spec.md) | Service Portal widget (server + client + HTML) |
| [`flow.spec.md`](specs/flow.spec.md) | Flow Designer flow with steps |
| [`playbook.spec.md`](specs/playbook.spec.md) | **PAD Playbook** — lanes, activities, workspace integration, S2P example |
| [`integration.spec.md`](specs/integration.spec.md) | External system integration |
| [`scoped-app.spec.md`](specs/scoped-app.spec.md) | Full scoped application architecture |
| [`_spec-guide.md`](specs/_spec-guide.md) | How spec-driven development works |

### Meta-Prompt Roles — `prompts/`

Paste one of these to change Claude's mode of operation.

| Role | File | Behavior |
|------|------|----------|
| 🏗️ Architect | [`architect.md`](prompts/architect.md) | Evaluates design alternatives, produces ADRs, plans app structure |
| 📖 Story Analyzer | [`story-analyzer.md`](prompts/story-analyzer.md) | Parses agile user stories into tech specs; **auto-detects cross-team handoffs and recommends PAD Playbooks** |
| 🎭 Playbook Designer | Load `contexts/playbooks.md` + `specs/playbook.spec.md` | PAD process design, lane breakdown, workspace integration guide |
| 💻 Developer | [`developer.md`](prompts/developer.md) | Spec-to-code pipeline, complete output with config + tests |
| 🔍 Reviewer | [`reviewer.md`](prompts/reviewer.md) | Systematic code review with severity ratings (🔴🟠🟡🟢) |
| 🐛 Debugger | [`debugger.md`](prompts/debugger.md) | Root cause analysis, platform diagnostic tools, resolution steps |


### Knowledge Base — `knowledge/`

Reference material. Upload to Claude Projects for always-on access.

| File | Contents |
|------|----------|
| [`api-patterns.md`](knowledge/api-patterns.md) | 15 most-used ServiceNow API patterns (copy-paste ready) |
| [`anti-patterns.md`](knowledge/anti-patterns.md) | 15+ common mistakes with severity ratings and fixes |
| [`naming-conventions.md`](knowledge/naming-conventions.md) | Naming standards for every artifact type |
| [`scoping-rules.md`](knowledge/scoping-rules.md) | Cross-scope access rules, Application Access matrix |
| [`table-reference.md`](knowledge/table-reference.md) | Key system tables, Task hierarchy, CMDB structure |

---

## 💡 Examples

### Example 1: Build a Script Include

```
📋 SYSTEM.md + contexts/script-includes.md + contexts/gliderecord.md

"Create a Script Include called IncidentEscalationUtils in scope x_acme_itsm 
that provides methods to:
1. Check if an incident has been stale for N days (configurable via property)
2. Escalate stale incidents by bumping priority and adding a work note
3. Get a count of all stale incidents per assignment group"
```

### Example 2: Review Existing Code

```
📋 SYSTEM.md + prompts/reviewer.md + contexts/business-rules.md

"Review this Business Rule for issues:

(function executeRule(current, previous) {
    var gr = new GlideRecord('sys_user');
    gr.addQuery('sys_id', current.caller_id);
    gr.query();
    if (gr.next()) {
        if (gr.vip == true) {
            current.priority = 1;
            current.update();
        }
    }
})(current, previous);"
```

Claude will return a severity-rated issue table with specific fixes for every problem found.

### Example 3: Debug an Issue

```
📋 SYSTEM.md + prompts/debugger.md + contexts/flow-designer.md

"My Flow isn't triggering when a Change Request moves to 'Implement' state.
The flow was working last week. We deployed an update set yesterday.
Flow name: ITSM - Change State Implement Approval Flow"
```

### Example 4: Architecture Decision

```
📋 SYSTEM.md + prompts/architect.md

"We need to automate the approval process for Change Requests over $10,000.
Should we use a Business Rule with Script Include, Flow Designer, 
or a combination? We're on the Washington DC release with IntegrationHub."
```

### Example 5: User Story Analysis (The `/story` skill)

```
📋 SYSTEM.md + prompts/story-analyzer.md

"As an ITIL Manager, I want to automatically close stale incident tasks 
after 30 days of inactivity, so that my metrics are accurate."
```

Claude will output a **Technical Solution Map** detailing the exact ServiceNow artifacts needed (e.g., Scheduled Job, Business Rule), required context files, and acceptance criteria. It will then hand off execution to the `/developer` role to generate the final code.


---

## 🛠️ Customization

### Add Your Team's Conventions

Edit `SYSTEM.md` Section 5 (Code Standards) to match your organization:

```markdown
// Change the vendor prefix
x_acme_  →  x_yourcompany_

// Add custom naming rules
// Modify JSDoc requirements
// Adjust quality gate thresholds
```

### Create Domain-Specific Context Modules

Create a new file in `contexts/` for your specific domain:

```markdown
# Context Module: HR Service Delivery

> **Module:** `hrsd.md`
> **Load when:** Building HRSD customizations
> **Depends on:** `_base.md`

---

## Key Tables
- sn_hr_core_case (HR Case)
- sn_hr_core_task (HR Task)
...

## Common Patterns
...

## Anti-Patterns
...
```

### Create Custom Spec Templates

Copy any template from `specs/` and add fields specific to your artifact type — catalog items, record producers, transform maps, etc.

---

## 🏗️ Architecture Principles

SNow-Claude is designed around these ideas:

1. **Composable, not monolithic** — Load 3KB of context, not 50KB. Your Claude context window stays efficient.

2. **Convention over configuration** — Sensible defaults for naming, logging, error handling. Override only what you need.

3. **Spec-driven, not prompt-dependent** — Structured specs produce consistent output regardless of how you phrase your request.

4. **Quality gates built-in** — Every code generation runs a 25-point mental checklist covering security, performance, maintainability, upgrade safety, and testability.

5. **Zero dependencies** — Pure markdown. Works with Claude.ai, Claude API, Claude Desktop, or any interface that accepts text.

---

## 🤝 Contributing

Contributions are welcome! Here's how you can help:

### Add a New Context Module
1. Create `contexts/your-module.md` following the existing format
2. Include: key concepts, patterns, anti-patterns, and decision guides
3. Reference `_base.md` as a dependency
4. Submit a PR with a usage example

### Improve Existing Modules
- Found a missing API pattern? Add it
- Discovered a new anti-pattern? Document it
- Have a better code example? Replace the old one

### Add Spec Templates
- Create templates for artifact types not yet covered (e.g., Transform Maps, Record Producers, Notifications)
- Follow the YAML structure in existing templates

### Report Issues
- If Claude generates incorrect code using SNow-Claude, open an issue with:
  - Which modules were loaded
  - What you asked for
  - What Claude generated (the bad part)
  - What the correct output should be

---

## 📋 Compatibility

| Platform | Supported |
|----------|:---------:|
| Claude.ai (Web) | ✅ |
| Claude Projects | ✅ (Recommended) |
| Claude Desktop App | ✅ |
| Claude API | ✅ |
| Other LLMs | ⚠️ Untested — likely works with adjustments |

| ServiceNow Release | Supported |
|-------------------|:---------:|
| Washington DC | ✅ |
| Xanadu | ✅ |
| Vancouver | ✅ (minor API differences) |
| Utah and earlier | ⚠️ Some APIs may differ |

---

## 📄 License

MIT License — use it, modify it, share it. See [LICENSE](LICENSE) for details.

---

<p align="center">
  <strong>Stop prompting. Start engineering.</strong><br>
  <sub>Built for ServiceNow developers who want Claude to write platform code the right way, every time.</sub>
</p>
