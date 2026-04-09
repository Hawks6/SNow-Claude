# Features Research: SNow-Claude User Story Skill

## Domain
User story analysis and ServiceNow artifact generation via LLM skill.

## Feature Categories

### Table Stakes (Must Have)
| Feature | Complexity | Dependencies |
|---------|-----------|-------------|
| **User story parsing** — Accept raw "As a…, I want…, so that…" format and extract actors, goals, and motivations | Low | None |
| **SYSTEM.md integration** — Apply all core principles (scoping, no hardcoding, security, performance) to generated output | Low | SYSTEM.md |
| **Artifact identification** — Determine which ServiceNow artifacts are needed (Business Rule, Client Script, Script Include, etc.) | Medium | contexts/_base.md |
| **Context auto-selection** — Automatically load relevant context modules based on identified artifacts | Medium | contexts/*.md |
| **Spec auto-filling** — Generate filled spec templates for each identified artifact | Medium | specs/*.md |
| **Code generation** — Produce production-ready ServiceNow code for each artifact | High | developer.md role |
| **Quality gate pass** — Run the 25-point mental checklist from SYSTEM.md §6 against all output | Medium | SYSTEM.md §6 |
| **Dual input mode** — Accept stories via inline chat text OR `.md` file reference | Low | None |

### Differentiators (Competitive Advantage)
| Feature | Complexity | Dependencies |
|---------|-----------|-------------|
| **Dependency mapping** — Show how generated artifacts connect (e.g., Script Include → Business Rule → Client Script) | Medium | architect.md patterns |
| **ATF test generation** — Produce ATF test recommendations for every generated artifact | Medium | contexts/atf.md |
| **Acceptance criteria → test cases** — Transform story acceptance criteria into verifiable ATF assertions | Medium | specs/_spec-guide.md |
| **Multi-artifact orchestration** — Generate complete artifact sets in dependency order with cross-references | High | developer.md §5 |
| **Gap detection** — Flag when a user story is too vague or missing critical ServiceNow-specific details | Low | architect.md questioning |

### Anti-Features (Do NOT Build)
| Anti-Feature | Why |
|-------------|-----|
| Direct ServiceNow instance deployment | Out of scope; framework is offline/spec-driven |
| Story estimation/sizing | Not a project management tool |
| Story prioritization/backlog management | Out of scope |
| Multi-story epic decomposition | Keep skill focused; one story at a time |

## Feature Dependencies
```
User Story Parsing
  └── Artifact Identification
       ├── Context Auto-Selection
       │    └── Code Generation
       │         └── Quality Gate Pass
       └── Spec Auto-Filling
            └── Code Generation
```

---
*Research: Features — SNow-Claude User Story Skill*
