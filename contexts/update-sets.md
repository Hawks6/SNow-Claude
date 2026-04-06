# Context Module: Update Sets & Deployment

> **Module:** `update-sets.md`
> **Load when:** Managing update sets, deployments, or CI/CD pipelines
> **Depends on:** `_base.md` (auto-loaded)

---

## 1. Update Set Strategy

### Naming Convention
```
[SCOPE] - [TICKET#] - [Description]

Examples:
  x_acme_app - RITM0012345 - Add approval matrix table
  x_acme_app - STRY0001234 - VIP caller auto-escalation
  Global - CHG0098765 - Add custom field to incident
```

### Grouping Rules
- One **logical change** = one update set
- Related changes (table + ACL + BR) go **together**
- Don't mix unrelated changes
- Never commit to the "Default" update set

## 2. Deployment Order

```
1. Tables and Fields         ← Schema first
2. System Properties         ← Configuration
3. Roles                     ← Security model
4. ACLs                      ← Access control
5. Script Includes           ← Dependencies used by others
6. Business Rules            ← Server logic
7. Client Scripts / UI Pol   ← Client logic
8. Flows / Workflows         ← Automation
9. Service Portal / UI Bldr  ← UI
10. Scheduled Jobs           ← Background tasks
11. Notifications            ← Alerting
```

## 3. Collision Resolution

| Collision Type | Action |
|---------------|--------|
| Local update is newer | Accept remote, cherry-pick local changes |
| Remote update is newer | Accept remote (usually correct) |
| Both modified same record | Manual merge — understand both changes |
| OOB record modified | Clone OOB → customize clone instead |

## 4. Source Control Integration

```
App Repo / Source Control:
├── Link scoped app to Git repo
├── Commit changes from Studio
├── Branch strategy: feature branches → main
├── Deploy: pull from repo to target instance
└── CI/CD: ATF tests on deploy
```

### Best Practice
- Use **Application Repository** or **Git** for scoped apps
- Use **Update Sets** for Global scope changes
- Never modify OOB records — clone first
- Tag releases with version numbers

---

*Context module for update set management and deployment.*
