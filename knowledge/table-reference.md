# Knowledge Base: Key System Tables

> **File:** `table-reference.md`
> **Purpose:** Reference for commonly used system tables and relationships

---

## Task Hierarchy

| Table | Label | Extends | Key Use |
|-------|-------|---------|---------|
| `task` | Task | — | Base table for all task types |
| `incident` | Incident | task | IT incident management |
| `problem` | Problem | task | Root cause analysis |
| `change_request` | Change Request | task | Change management |
| `sc_request` | Request | task | Service catalog request |
| `sc_req_item` | Requested Item | task | Individual catalog item |
| `sc_task` | Catalog Task | task | Fulfillment task |
| `kb_submission` | KB Submission | task | Knowledge article draft |
| `sn_hr_core_case` | HR Case | task | HR service delivery |
| `sn_customerservice_case` | CSM Case | task | Customer service |

## User & Group

| Table | Label | Key Fields |
|-------|-------|------------|
| `sys_user` | User | user_name, email, active, vip, manager |
| `sys_user_group` | Group | name, manager, active, type |
| `sys_user_grmember` | Group Member | user → sys_user, group → sys_user_group |
| `sys_user_has_role` | User Role | user → sys_user, role → sys_user_role |
| `sys_user_role` | Role | name, suffix, assignable_by |

## CMDB

| Table | Label | Key Fields |
|-------|-------|------------|
| `cmdb_ci` | Configuration Item | name, asset_tag, sys_class_name, operational_status |
| `cmdb_ci_server` | Server | host_name, os, cpu_count, ram |
| `cmdb_ci_service` | Business Service | busines_criticality |
| `cmdb_rel_ci` | CI Relationship | parent, child, type → cmdb_rel_type |
| `cmdb_rel_type` | Relationship Type | name (e.g., "Runs on") |

## Service Catalog

| Table | Label | Key Fields |
|-------|-------|------------|
| `sc_catalog` | Catalog | title |
| `sc_category` | Category | title, catalog → sc_catalog |
| `sc_cat_item` | Catalog Item | name, category, workflow |
| `item_option_new` | Variable | name, type, cat_item |
| `sc_cat_item_category` | Item Category | sc_cat_item, sc_category |

## Knowledge

| Table | Label | Key Fields |
|-------|-------|------------|
| `kb_knowledge` | Knowledge Article | number, short_description, text, kb_knowledge_base |
| `kb_knowledge_base` | Knowledge Base | title |
| `kb_category` | KB Category | label |

## System

| Table | Label | Key Fields |
|-------|-------|------------|
| `sys_properties` | System Property | name, value, suffix |
| `sys_script_include` | Script Include | name, api_name, client_callable |
| `sys_script` | Business Rule | name, collection, when, order |
| `sys_script_client` | Client Script | name, table, type |
| `sys_update_set` | Update Set | name, state, application |
| `syslog` | System Log | level, message, source |
| `sys_metadata` | Metadata | sys_name, sys_class_name (base for all config records) |

## Notifications & Events

| Table | Label | Key Fields |
|-------|-------|------------|
| `sysevent` | Event | name, instance, parm1, parm2 |
| `sysevent_email_action` | Notification | name, event_name, collection |
| `sys_email` | Email | recipients, subject, body, type |

---

*System table reference for ServiceNow development.*
