# ⚡ SNow-Claude Quickstart

**Get productive in 5 minutes.**

---

## Step 1: Copy SYSTEM.md

Copy the contents of `SYSTEM.md` and paste it as the **first message** in a new Claude conversation. Or add it to a **Claude Project** as a system prompt.

This gives Claude the ServiceNow expert persona, coding standards, quality gates, and the composable context system.

## Step 2: Load Context for Your Task

Depending on what you're building, paste the relevant context module **after** SYSTEM.md:

| Building... | Paste these context files |
|-------------|--------------------------|
| Business Rule | `contexts/business-rules.md` |
| Script Include | `contexts/script-includes.md` |
| Client Script | `contexts/client-scripts.md` |
| GlideRecord queries | `contexts/gliderecord.md` |
| Flow / Subflow | `contexts/flow-designer.md` |
| Service Portal widget | `contexts/service-portal.md` |
| UI Builder component | `contexts/now-experience.md` |
| REST API | `contexts/rest-api.md` |
| ATF tests | `contexts/atf.md` |
| Security / ACLs | `contexts/security.md` |
| Performance tuning | `contexts/performance.md` |

**Tip:** You can load multiple context modules at once for complex tasks.

## Step 3: Use a Spec Template (Optional)

For structured code generation:

1. Open the spec template from `specs/` that matches your artifact
2. Fill in the `<placeholder>` fields with your requirements
3. Paste the filled spec into the conversation

Claude will generate production-ready code that passes all quality gates.

## Step 4: Activate a Role (Optional)

Use slash commands to change Claude's behavior mode:

| Command | Mode | Use When |
|---------|------|----------|
| `/architect` | Architecture mode | Designing solutions, evaluating options |
| `/developer` | Developer mode | Generating code from specs |
| `/reviewer` | Review mode | Reviewing existing code |
| `/debugger` | Debug mode | Troubleshooting issues |

Or paste the role prompt from `prompts/` for the full role context.

---

## Example: Quick Business Rule

```
[Paste SYSTEM.md]

[Paste contexts/business-rules.md]

Create a before Business Rule on the incident table that validates 
resolution requirements when state changes to Resolved:
- Resolution code must be filled
- Close notes must be at least 20 characters
- P1 incidents must have a linked problem
```

Claude will generate the complete BR with proper naming, JSDoc, logging, error handling, quality gate results, ATF recommendations, and deployment notes.

---

## Claude Projects Setup (Recommended)

For persistent context without re-pasting:

1. Create a new **Claude Project**
2. Add `SYSTEM.md` + `contexts/_base.md` to the project knowledge
3. Add your most-used context modules
4. Add relevant knowledge files (`anti-patterns.md`, `naming-conventions.md`)
5. Every conversation in this project automatically has full SNow-Claude context

---

*That's it. Start building.* 🚀
