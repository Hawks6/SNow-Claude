# Architecture Research: SNow-Claude User Story Skill

## Domain
Integrating a user story analysis skill into the existing Composable Context Injection architecture.

## Existing Architecture (From Codebase Map)
```
SYSTEM.md (Brain — always active)
├── prompts/ (Behavioral Roles)
│   ├── architect.md   → /architect
│   ├── developer.md   → /developer
│   ├── reviewer.md    → /reviewer
│   └── debugger.md    → /debugger
├── contexts/ (Knowledge Modules)
│   ├── _base.md       → always active
│   └── <domain>.md    → loaded per task
├── specs/ (Spec Templates)
│   ├── _spec-guide.md
│   └── <artifact>.spec.md
└── knowledge/ (Reference Docs)
    └── <topic>.md
```

## Proposed Skill Integration

### Component: `prompts/story-analyzer.md` (New Behavioral Role)
**What it does:** Activates `/story` mode. Contains the complete chain-of-thought pipeline:
1. **Parse** — Extract actor, goal, motivation, acceptance criteria from raw story
2. **Analyze** — Map requirements to ServiceNow artifact types using SYSTEM.md knowledge
3. **Select** — Identify which `contexts/*.md` modules are relevant
4. **Generate** — Produce filled specs and production code for each artifact
5. **Validate** — Run quality gates (SYSTEM.md §6) against all output
6. **Deliver** — Output in structured format with dependency map, ATF tests, deployment notes

### Component: `specs/user-story.spec.md` (New Spec Template)
**What it does:** Optional structured input format for formal story submission. Provides YAML fields for actor, goal, motivation, acceptance criteria, table context, scope, and constraints.

### Data Flow
```
User Story Input (chat or file)
        │
        ▼
┌─────────────────────────┐
│  story-analyzer.md       │  ← New behavioral role
│  (Chain-of-Thought)      │
│                          │
│  1. Parse story          │
│  2. Identify artifacts   │──→ Maps to: Business Rule, Script Include,
│  3. Select contexts      │    Client Script, Flow, REST API, etc.
│  4. Generate specs+code  │
│  5. Run quality gates    │──→ Uses: SYSTEM.md §6 (25-point checklist)
│  6. Format output        │
└─────────────────────────┘
        │
        ▼
Structured Output:
  ├── Story Analysis Summary
  ├── Artifact 1: [Type] + Code + Config + Tests
  ├── Artifact 2: [Type] + Code + Config + Tests
  ├── Dependency Map
  └── Deployment Notes
```

### Integration Points
| Existing Component | How Skill Uses It |
|-------------------|------------------|
| `SYSTEM.md` §2 (Core Principles) | Applied to all generated code |
| `SYSTEM.md` §3 (Context Protocol) | Drives context module selection |
| `SYSTEM.md` §4 (Spec Protocol) | Governs spec generation |
| `SYSTEM.md` §6 (Quality Gates) | Validates all output |
| `SYSTEM.md` §7 (Response Format) | Structures code generation output |
| `prompts/developer.md` | Reuses code generation patterns internally |
| `prompts/architect.md` | Reuses architecture decision patterns |
| `contexts/_base.md` | Always loaded during generation |
| `contexts/<domain>.md` | Loaded based on identified artifacts |
| `specs/<artifact>.spec.md` | Templates filled during generation |

### Build Order
1. `specs/user-story.spec.md` — Input template (no dependencies)
2. `prompts/story-analyzer.md` — Core skill prompt (depends on understanding all existing modules)
3. `SYSTEM.md` update — Register `/story` command in §8 Meta-Prompt Roles table
4. `README.md` update — Document the new skill

---
*Research: Architecture — SNow-Claude User Story Skill*
