# Context Module: Scripted REST APIs & Integrations

> **Module:** `rest-api.md`
> **Load when:** Building Scripted REST APIs or outbound integrations
> **Depends on:** `_base.md` (auto-loaded)

---

## 1. Scripted REST API Structure

```
Scripted REST API (container)
├── API Name: "ITSM Extension API"
├── API ID: x_acme_app_itsm_api
├── Base Path: /api/x_acme_app/itsm_api
└── Resources (endpoints):
    ├── GET    /v1/incidents
    ├── GET    /v1/incidents/{id}
    ├── POST   /v1/incidents
    ├── PUT    /v1/incidents/{id}
    └── DELETE /v1/incidents/{id}
```

## 2. Resource Script Pattern

```javascript
(function process(/*RESTAPIRequest*/ request, /*RESTAPIResponse*/ response) {

    var LOG_PREFIX = '[ITSM API - GET Incidents]';

    try {
        // 1. Parse parameters
        var limit = parseInt(request.queryParams.limit) || 10;
        var offset = parseInt(request.queryParams.offset) || 0;
        var state = request.queryParams.state;

        // 2. Validate
        if (limit > 100) limit = 100;  // Cap max results

        // 3. Query with GlideRecordSecure (enforces ACLs!)
        var grs = new GlideRecordSecure('incident');
        grs.addActiveQuery();
        if (state) {
            grs.addQuery('state', state);
        }
        grs.orderByDesc('sys_created_on');
        grs.chooseWindow(offset, offset + limit);
        grs.query();

        // 4. Build response
        var results = [];
        while (grs.next()) {
            results.push({
                sys_id: grs.getUniqueValue(),
                number: grs.getValue('number'),
                short_description: grs.getValue('short_description'),
                state: grs.getDisplayValue('state'),
                priority: grs.getDisplayValue('priority'),
                assigned_to: grs.getDisplayValue('assigned_to')
            });
        }

        // 5. Return structured response
        response.setStatus(200);
        response.setBody({
            result: results,
            count: results.length,
            offset: offset,
            limit: limit
        });

    } catch (ex) {
        gs.error(LOG_PREFIX + ' Error: ' + ex.getMessage());
        response.setStatus(500);
        response.setBody({
            error: {
                message: 'Internal server error',
                detail: ex.getMessage()
            }
        });
    }

})(request, response);
```

## 3. Security Requirements

| Rule | Implementation |
|------|---------------|
| Always authenticate | Set "Requires authentication" = true |
| Use GlideRecordSecure | Enforces ACLs for the API user |
| Create REST_Endpoint ACLs | Control who can call each endpoint |
| Dedicated API users | Create non-interactive service accounts |
| Least privilege roles | Only assign roles the API needs |
| Never hardcode credentials | Use Connection/Credential Aliases |
| Validate all inputs | Check types, lengths, ranges |

## 4. Proper HTTP Status Codes

```javascript
response.setStatus(200);   // OK — successful GET
response.setStatus(201);   // Created — successful POST
response.setStatus(204);   // No Content — successful DELETE
response.setStatus(400);   // Bad Request — invalid input
response.setStatus(401);   // Unauthorized — not authenticated
response.setStatus(403);   // Forbidden — authenticated but no permission
response.setStatus(404);   // Not Found — resource doesn't exist
response.setStatus(409);   // Conflict — duplicate or state conflict
response.setStatus(500);   // Internal Server Error
```

## 5. Outbound REST Integration

```javascript
// Using RESTMessageV2 for outbound calls
var rm = new sn_ws.RESTMessageV2('x_acme_app.External_System', 'POST');
rm.setStringParameterNoEscape('endpoint_path', '/api/resource');
rm.setRequestHeader('Content-Type', 'application/json');
rm.setRequestBody(JSON.stringify(payload));

var resp = rm.execute();
var httpStatus = resp.getStatusCode();
var body = resp.getBody();

if (httpStatus === 200 || httpStatus === 201) {
    var result = JSON.parse(body);
    // Process success
} else {
    gs.error('[Integration] HTTP ' + httpStatus + ': ' + body);
    // Handle error
}
```

## 6. API Versioning

```
/api/x_acme_app/itsm_api/v1/incidents     ← Version 1
/api/x_acme_app/itsm_api/v2/incidents     ← Version 2 (new schema)
```

**Rule:** Always version your APIs. Never break existing consumers.

---

*Context module for REST API and integration development.*
