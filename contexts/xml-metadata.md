# Context: ServiceNow XML Metadata

This module defines the mapping between ServiceNow XML export tags and the internal data structures used by SNow-Claude for analysis and code generation.

## 1. Global Metadata (Unload Wrapper)
Large exports are often wrapped in an `unload` or `sys_update_set` tag.
- `<sys_update_xml>`: Each child represents one artifact.
- `<action>`: `INSERT_OR_UPDATE` or `DELETE`.
- `<payload>`: Contains the CDATA encoded XML of the actual artifact.

## 2. Business Rules (`sys_script`)
| XML Tag | SNow-Claude Mapping | Notes |
|---|---|---|
| `<name>` | Artifact Name | |
| `<collection>` | Table | Target table for the BR |
| `<when>` | Trigger (Before/After) | |
| `<script>` | Logic (JavaScript) | The core Rhino/ES5 code |
| `<condition>` | Filter Condition | Encoded query or boolean logic |
| `<active>` | Status | |

## 3. Script Includes (`sys_script_include`)
| XML Tag | SNow-Claude Mapping | Notes |
|---|---|---|
| `<name>` | Class Name | |
| `<script>` | Class Definition | Core logic |
| `<api_name>` | Scoped Name | e.g., `x_vendor_app.Utils` |
| `<access>` | Visibility | public/package_private |

## 4. Client Scripts (`sys_script_client`)
| XML Tag | SNow-Claude Mapping | Notes |
|---|---|---|
| `<name>` | Name | |
| `<table_name>` | Table | |
| `<type>` | Type | onLoad, onChange, onSubmit |
| `<script>` | Client Side Logic | |

## 5. Decomposition Protocol
When processing an "Entire Application Scope" XML:
1. Identify all `<sys_update_xml>` nodes.
2. Extract the `name` (e.g., `sys_script_12345`) and `type` (`sys_script`).
3. Summarize the `payload` into a 1-sentence logic description.
4. Maintain an **Artifact Inventory Map** for the `story-analyzer`.

---
*Created: 2026-05-10*
