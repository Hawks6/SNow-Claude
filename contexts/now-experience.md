# Context Module: Now Experience / UI Builder

> **Module:** `now-experience.md`
> **Load when:** Building custom components or pages in UI Builder
> **Depends on:** `_base.md` (auto-loaded)

---

## 1. Architecture Overview

```
Now Experience Stack:
├── UI Builder          → Visual page/workspace builder
├── Components          → Web components (Seismic framework)
├── Data Resources      → Server-side data fetching
├── Events & Handlers   → Component communication
├── Themes              → Design tokens (Polaris/custom)
└── ServiceNow CLI (snc) → Local development toolchain
```

## 2. Key Principles

| Principle | Details |
|-----------|---------|
| Use OOB components first | 150+ baseline components available |
| No DOM manipulation | Web components are encapsulated |
| Data Resources for queries | Not inline script queries |
| Event-driven communication | Components emit events, pages handle |
| Theme-based styling | Use design tokens, not hardcoded colors |
| `snc` CLI for custom components | Don't hand-code component structure |

## 3. Custom Component Development

### Setup
```bash
# Install ServiceNow CLI
npm install -g @servicenow/cli

# Create component project
snc ui-component create --name x-acme-my-component
cd x-acme-my-component

# Development
snc ui-component develop    # Local dev server
snc ui-component deploy     # Deploy to instance
```

### Component Structure
```javascript
import { createCustomElement } from '@servicenow/ui-core';
import snabbdom from '@servicenow/ui-renderer-snabbdom';
import styles from './styles.scss';

const view = (state, { updateState, dispatch }) => {
    const { items, loading } = state;
    return (
        <div className="container">
            {loading && <now-loader />}
            {!loading && items.map(item => (
                <div className="item" key={item.sys_id}>
                    <span>{item.number}</span>
                    <span>{item.short_description}</span>
                </div>
            ))}
        </div>
    );
};

createCustomElement('x-acme-my-component', {
    renderer: { type: snabbdom },
    view,
    styles,
    properties: {
        tableName: { default: 'incident' },
        limit: { default: 10 }
    },
    initialState: {
        items: [],
        loading: true
    }
});
```

## 4. Data Resources

Use Data Resources in UI Builder (not inline scripts) for data fetching:

| Type | Use Case |
|------|----------|
| **Record** | Single record by sys_id |
| **Record List** | Query multiple records |
| **Indicator Source** | Snapshot-based metrics (performance) |
| **Transform** | Reshape data from other resources |
| **Script** | Custom server-side logic |

## 5. When to Use UI Builder vs Service Portal

| Criteria | UI Builder | Service Portal |
|----------|-----------|----------------|
| Strategic direction | ✅ Current | ⚠️ Legacy |
| Mobile support | ✅ Native | ⚠️ Responsive |
| Agent Workspace | ✅ Native | ❌ |
| AngularJS dependency | ❌ None | ✅ Required |
| Component ecosystem | Growing | Mature |
| Custom theming | ✅ Polaris | ✅ SASS |
| New projects | ✅ Recommended | Only if SP expertise exists |

---

*Context module for Now Experience / UI Builder development.*
