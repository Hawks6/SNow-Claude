# Spec Template: Scripted REST API

> Fill in with SYSTEM.md + contexts/rest-api.md

---

```yaml
# ═══════════════════════════════════════════
# SCRIPTED REST API SPECIFICATION
# ═══════════════════════════════════════════

artifact_type: Scripted REST API
api_name: <App Name API>
api_id: <x_vendor_app_api>
scope: <x_vendor_app>
base_path: /api/<scope>/<api_id>
version: v1
requires_auth: true
auth_method: <basic | oauth2 | api_key>

# ───────────────────────────────────────────
# RESOURCES (ENDPOINTS)
# ───────────────────────────────────────────

resources:
  - name: <Resource Name>
    method: <GET | POST | PUT | PATCH | DELETE>
    path: /<version>/<resource_path>
    description: <What this endpoint does>
    request:
      headers:
        - name: Content-Type
          value: application/json
          required: true
      query_params:
        - name: <param>
          type: <string | number>
          required: <true | false>
          description: <Purpose>
      body_schema: |
        {
          "field": "type — description"
        }
    response:
      success_code: <200 | 201>
      success_schema: |
        {
          "result": {}
        }
      error_codes:
        - code: 400
          condition: <When this occurs>
        - code: 404
          condition: <When this occurs>

# ───────────────────────────────────────────
# SECURITY
# ───────────────────────────────────────────

acl_rules:
  - <ACL requirements for each resource>
allowed_roles:
  - <Roles that can access this API>
use_gliderecord_secure: true
rate_limiting: <requests per minute if applicable>

# ───────────────────────────────────────────
# ACCEPTANCE CRITERIA
# ───────────────────────────────────────────

criteria:
  - <Criterion 1>
  - <Criterion 2>
```
