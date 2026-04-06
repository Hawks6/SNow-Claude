# Context Module: Service Portal Widgets

> **Module:** `service-portal.md`
> **Load when:** Creating or modifying Service Portal widgets
> **Depends on:** `_base.md` (auto-loaded)

---

## 1. Widget Architecture

```
Widget
├── HTML Template       (AngularJS, rendered client-side)
├── CSS / SCSS          (scoped to widget automatically)
├── Client Script       (AngularJS controller — $scope, c.data)
├── Server Script       (Runs on server — data.*, input.*)
└── Instance Options    (Configurable per widget instance)
```

## 2. Server Script Pattern

```javascript
(function() {
    // Initialize data on page load
    data.items = [];
    data.canCreate = gs.hasRole('x_acme_app.user');

    var gr = new GlideRecord('x_acme_app_request');
    gr.addQuery('requested_for', gs.getUserID());
    gr.addActiveQuery();
    gr.orderByDesc('sys_created_on');
    gr.setLimit(20);
    gr.query();
    while (gr.next()) {
        data.items.push({
            sys_id: gr.getUniqueValue(),
            number: gr.getValue('number'),
            short_description: gr.getValue('short_description'),
            state: gr.getDisplayValue('state')
        });
    }

    // Handle client requests (server.update / server.get)
    if (input) {
        if (input.action === 'delete') {
            var grDel = new GlideRecord('x_acme_app_request');
            if (grDel.get(input.sys_id)) {
                grDel.deleteRecord();
                data.message = 'Record deleted';
            }
        }
    }
})();
```

## 3. Client Script Pattern

```javascript
function($scope, $http, spUtil) {
    var c = this;

    // c.data is populated by server script
    c.deleteItem = function(sysId) {
        c.server.get({
            action: 'delete',
            sys_id: sysId
        }).then(function(response) {
            c.data = response.data;
            spUtil.addInfoMessage(c.data.message);
        });
    };

    // Use spUtil for navigation
    c.openRecord = function(table, sysId) {
        spUtil.recordWatch($scope, table, 'sys_idIN' + sysId);
    };
}
```

## 4. HTML Template Pattern

```html
<div class="panel panel-default">
    <div class="panel-heading">
        <h3 class="panel-title">{{::c.data.title}}</h3>
    </div>
    <div class="panel-body">
        <div ng-if="c.data.items.length === 0" class="text-muted">
            No items found
        </div>
        <table ng-if="c.data.items.length > 0" class="table table-hover">
            <thead>
                <tr>
                    <th>Number</th>
                    <th>Description</th>
                    <th>State</th>
                </tr>
            </thead>
            <tbody>
                <tr ng-repeat="item in c.data.items track by item.sys_id">
                    <td>{{item.number}}</td>
                    <td>{{item.short_description}}</td>
                    <td>{{item.state}}</td>
                </tr>
            </tbody>
        </table>
    </div>
</div>
```

## 5. Key Rules

| Rule | Why |
|------|-----|
| Never modify OOB widgets | Clone and customize instead |
| Keep logic in server script | Minimize client-side data processing |
| Use Script Includes as APIs | Reusable across widgets |
| Only return needed fields | Minimize payload size |
| Use `server.get()` for data fetch | `server.update()` for mutations |
| Use SASS variables from theme | Consistent styling |
| Always handle empty states | Widget on page without data |
| Scope CSS is automatic | But beware global theme overrides |
| Use `spUtil` for navigation/messages | Not `window.location` |

## 6. Communication Between Widgets

```javascript
// Widget A — broadcast event
$scope.$emit('my_custom_event', { data: 'value' });

// Widget B — listen for event
$scope.$on('my_custom_event', function(event, data) {
    c.handleEvent(data);
});
```

---

*Context module for Service Portal widget development.*
