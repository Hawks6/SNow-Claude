# Spec-Driven Development Guide

> **File:** `_spec-guide.md`
> **Purpose:** How to use spec templates for code generation

---

## How It Works

1. **Pick a spec template** from `specs/` matching your artifact type
2. **Fill in the fields** — replace `<placeholders>` with your requirements
3. **Paste into Claude** with: `SYSTEM.md` + relevant `contexts/*.md` + your filled spec
4. **Claude generates** production-ready code following all conventions
5. **Claude runs quality gates** and flags any issues

## Workflow Diagram

```
┌─────────────┐     ┌──────────────┐     ┌─────────────────┐
│ Pick         │ ──→ │ Fill in       │ ──→ │ Paste into       │
│ spec/*.md    │     │ requirements  │     │ Claude + context │
└─────────────┘     └──────────────┘     └────────┬────────┘
                                                   │
                    ┌──────────────┐     ┌─────────▼────────┐
                    │ Ready to      │ ←── │ Claude generates  │
                    │ implement     │     │ + quality gates   │
                    └──────────────┘     └──────────────────┘
```

## Tips

- **Be specific** in descriptions — Claude uses them for naming, comments, and logic
- **List acceptance criteria** — they become test cases
- **Mention dependencies** — Claude will generate correct cross-scope calls
- **Leave fields blank** if unsure — Claude will ask

## Example: Filling a Business Rule Spec

```yaml
artifact_type: Business Rule
table: incident
name: Incident - Before Validate Resolution
when: before
operation: update
order: 100
condition: state changes to 6 (Resolved)
scope: x_acme_itsm_ext

description: |
  When an incident is resolved, validate that:
  1. Resolution code is filled
  2. Resolution notes are at least 20 characters
  3. If incident is P1, require a problem link

criteria:
  - Blocks resolution if resolution_code is empty
  - Blocks resolution if close_notes < 20 chars
  - Blocks P1 resolution if no problem_id
  - Shows specific error messages for each validation failure
  - Does NOT block admin users

dependencies:
  - None (self-contained validation)
```

---

*Guide to using spec-driven development in SNow-Claude.*
