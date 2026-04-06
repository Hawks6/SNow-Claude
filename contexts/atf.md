# Context Module: Automated Test Framework (ATF)

> **Module:** `atf.md`
> **Load when:** Writing or designing automated tests
> **Depends on:** `_base.md` (auto-loaded)

---

## 1. ATF Structure

```
Test Suite
├── Test 1: Happy Path
│   ├── Step 1: Create test record (Record Insert)
│   ├── Step 2: Set field values (Field Values)
│   ├── Step 3: Run Script Include (Server Side Script)
│   └── Step 4: Assert results (Record Validation)
├── Test 2: Edge Case
│   └── ...
└── Teardown: Clean up test data
```

## 2. Test Step Types

| Step Type | Use |
|-----------|-----|
| **Record Insert** | Create test data |
| **Record Update** | Modify existing record |
| **Record Query** | Find records |
| **Field Values** | Set specific field values |
| **Record Validation** | Assert field values match expected |
| **Server Side Script** | Run custom validation logic |
| **REST Step** | Test REST API endpoints |
| **Flow Test** | Trigger and validate flows |
| **Impersonate** | Test as different user/role |
| **Set Values** | Set variables for later steps |

## 3. Server-Side Script Step Pattern

```javascript
// ATF Server Script — test a Script Include method
(function(outputs, steps, stepResult, assertEqual) {

    // Arrange
    var gr = new GlideRecord('incident');
    gr.initialize();
    gr.setValue('short_description', 'ATF Test Incident');
    gr.setValue('caller_id', gs.getUserID());
    gr.setValue('category', 'software');
    var sysId = gr.insert();

    // Act
    var utils = new x_acme_app.ApprovalMatrixUtils();
    var result = utils.getApprovers(gr, 'standard');

    // Assert
    assertEqual({
        name: 'Operation succeeded',
        shouldbe: true,
        value: result.success
    });

    assertEqual({
        name: 'At least one approver returned',
        shouldbe: true,
        value: result.approvers.length > 0
    });

    // Cleanup
    gr = new GlideRecord('incident');
    if (gr.get(sysId)) {
        gr.deleteRecord();
    }

})(outputs, steps, stepResult, assertEqual);
```

## 4. Test Design Rules

| Rule | Why |
|------|-----|
| Each test is independent | No dependencies between tests |
| Create your own test data | Don't rely on existing records |
| Clean up after tests | Remove test data in teardown |
| Use Impersonate step | Test with correct roles |
| Name tests descriptively | `ATF - <Target> - <Scenario>` |
| Test happy path + edge cases | Cover validation failures |
| One assertion focus per test | Easier to diagnose failures |

## 5. Naming Convention

```
Test Suite: ATF - ApprovalMatrixUtils
├── ATF - ApprovalMatrixUtils - Standard Approval Returns Approvers
├── ATF - ApprovalMatrixUtils - No Approvers Returns Empty
├── ATF - ApprovalMatrixUtils - Null Input Returns Error
├── ATF - ApprovalMatrixUtils - Multi Level Over Threshold
└── ATF - ApprovalMatrixUtils - Multi Level Under Threshold
```

---

*Context module for Automated Test Framework.*
