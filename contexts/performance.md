# Context Module: Performance Optimization

> **Module:** `performance.md`
> **Load when:** Optimizing queries, troubleshooting slowness, or reviewing performance
> **Depends on:** `_base.md` (auto-loaded), pairs well with `gliderecord.md`

---

## 1. Query Performance

### Index Awareness
```
Always query on INDEXED fields:
  ✅ sys_id, number, state, active, assignment_group, assigned_to
  ✅ sys_created_on, sys_updated_on, opened_at, closed_at
  ✅ Most reference fields are indexed by default
  
  ❌ short_description, description (text fields — not indexed)
  ❌ Custom fields (check if index exists!)
```

### Query Optimization Checklist
- [ ] Queries have conditions (`addQuery`) — never query without filters on large tables
- [ ] `setLimit()` used when only N results needed
- [ ] `GlideAggregate` for counts/sums — never loop to count
- [ ] No nested GlideRecord loops — use `addJoinQuery` or collect + IN
- [ ] `getValue()` used over dot-notation for performance
- [ ] `chooseWindow()` for batch processing large datasets
- [ ] Minimize dot-walking inside loops (each = extra query)
- [ ] `addEncodedQuery()` for complex filters (pre-optimized by platform)

## 2. Business Rule Performance

| Timing | Impact | Guideline |
|--------|--------|-----------|
| `before` | Blocks save | Max 2-3 queries, <50ms |
| `after` | Blocks response | Keep fast, delegate heavy work |
| `async` | Background | Use for integrations, bulk ops |
| `query` | Every list load! | Absolute minimum logic |
| `display` | Every form load | Cache where possible |

### Use Condition Field Over Script
```
Condition field (no script): Platform optimized, no Rhino engine load
Script condition: Loads Rhino, evaluates JS — measurably slower
```

## 3. Client-Side Performance

- **Never** use client-side `GlideRecord` (synchronous, blocks browser)
- Minimize `GlideAjax` calls — batch data needs in Display BR scratchpad
- Use `g_form.getReference()` callback (async) not synchronous version
- Reduce client scripts per form — each one loads and executes
- Prefer UI Policies for simple show/hide (no script engine)

## 4. Large-Scale Processing

```javascript
// Batch processing with chooseWindow
var batchSize = 500;
var start = 0;
var hasMore = true;

while (hasMore) {
    var gr = new GlideRecord('x_acme_app_log');
    gr.addQuery('processed', false);
    gr.chooseWindow(start, start + batchSize);
    gr.query();
    
    if (!gr.hasNext()) {
        hasMore = false;
        break;
    }
    
    while (gr.next()) {
        // Process record
        gr.setValue('processed', true);
        gr.setWorkflow(false);
        gr.autoSysFields(false);
        gr.update();
    }
    
    start += batchSize;
}
```

## 5. Diagnostic Tools

| Tool | Access | Use |
|------|--------|-----|
| Slow Query Log | System Diagnostics | Find expensive queries |
| SQL Debug | Session Debug | See actual SQL statements |
| Transaction Log | System Diagnostics | Find slow transactions |
| Script Execution Tracker | System Diagnostics | Find expensive scripts |
| Instance Scan | Instance Scan | Automated pattern detection |

---

*Context module for ServiceNow performance optimization.*
