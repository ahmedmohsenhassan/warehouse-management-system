# 03 — Role & Permission Matrix (RBAC)

> **Enterprise Warehouse Management & Workforce Productivity System**
> Document Version: 1.0 — Phase 0 Discovery
> Classification: Architecture Specification

---

## 1. Executive Summary & Philosophy

The Security & Authorization Model of this Enterprise WMS is **database-driven, zero-trust at the client layer, and enforced strictly via PostgreSQL Row Level Security (RLS) and Security Definer Functions**.

### Key Tenets:
1. **Never Trust the Client**: Flutter client-side permission checks serve only to enhance user experience (e.g., hiding unauthorized buttons, showing contextual disabled states). The source of truth and enforcement boundary is 100% inside PostgreSQL.
2. **Separation of Duties (SoD)**: Users cannot approve their own inventory requests or verify their own task outputs.
3. **Warehouse Scoping**: All operational data (inventory, transactions, tasks, schedules) is filtered by the user's explicit warehouse authorization (`warehouse_user_access`).
4. **Least Privilege**: Users are granted only the minimum permissions necessary for their daily operational assignments.

---

## 2. Defined System Roles

| Role Key | Role Name (EN) | المسمى الوظيفي (AR) | Primary Scope & Responsibilities |
|---|---|---|---|
| `store_worker` | Store Worker | عامل مستودع | Executes assigned tasks (picking, loading, counting), logs session time (start/pause/resume/complete), submits quantities, scans barcodes. No approval rights. |
| `store_keeper` | Store Keeper | أمين مستودع | Manages daily stock intake/issue, reviews worker draft transactions, verifies physical stock, initiates stock movements and counts. |
| `store_manager` | Store Manager | مدير مستودع | Has operational authority over assigned warehouse(s). Approves stock transactions, assigns tasks, reviews completed work, authorizes variances. |
| `operations_manager` | Operations Manager | مدير عمليات | Manages all warehouses across the enterprise. Plans weekly workforce schedules, reallocates resources, monitors macro productivity KPIs. |
| `general_manager` | General Manager | مدير عام | Executive oversight. Read-only access to all dashboards, macro financial/stock valuation reports, high-level audit logs, and strategic reports. |
| `system_admin` | System Administrator | مدير النظام | Configures system parameters, manages user accounts, assigns roles, configures master task types, manages integration endpoints and error queues. |

---

## 3. Comprehensive Permission Matrix

The permissions are structured hierarchically using dot-notation: `<module>.<entity>.<action>`.

### Legend:
- ✅ **Full Permission**: Can execute action subject to warehouse scoping.
- 👁 **Read-Only / Scoped View**: Can view data within their assigned warehouse or assigned tasks only.
- 📝 **Provisional / Draft Only**: Can create drafts but cannot post or approve.
- ❌ **No Access**: Explicitly denied by RLS and API endpoints.

| Permission Identifier | Store Worker | Store Keeper | Store Manager | Operations Manager | General Manager | System Admin |
|---|:---:|:---:|:---:|:---:|:---:|:---:|
| **Authentication & Users** | | | | | | |
| `auth.profile.view_self` | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| `auth.profile.update_self` | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| `users.view_all` | ❌ | 👁 (Assigned WH) | 👁 (Assigned WH) | ✅ | 👁 | ✅ |
| `users.manage` | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ |
| `roles.manage` | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ |
| **Warehouses & Zones** | | | | | | |
| `warehouse.view` | 👁 (Assigned WH) | 👁 (Assigned WH) | 👁 (Assigned WH) | ✅ | ✅ | ✅ |
| `warehouse.manage` | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ |
| `warehouse.assign_users` | ❌ | ❌ | ❌ | ✅ | ❌ | ✅ |
| `warehouse.zones.manage` | ❌ | ❌ | ✅ | ✅ | ❌ | ✅ |
| **Inventory Master & Balances** | | | | | | |
| `inventory.view` | 👁 (Assigned Items) | ✅ | ✅ | ✅ | ✅ | ✅ |
| `inventory.create` | ❌ | ❌ | ❌ | ✅ | ❌ | ✅ |
| `inventory.update` | ❌ | ❌ | ❌ | ✅ | ❌ | ✅ |
| `inventory.archive` | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ |
| `inventory.balances.view` | ❌ | ✅ (Assigned WH) | ✅ (Assigned WH) | ✅ | ✅ | ✅ |
| `inventory.min_qty.manage` | ❌ | ❌ | ✅ | ✅ | ❌ | ✅ |
| **Stock Transactions: Issue** | | | | | | |
| `stock.issue.create_draft` | 📝 | ✅ | ✅ | ✅ | ❌ | ✅ |
| `stock.issue.review` | ❌ | ✅ | ✅ | ✅ | ❌ | ✅ |
| `stock.issue.approve` | ❌ | ❌ | ✅ | ✅ | ❌ | ✅ |
| `stock.issue.post` | ❌ | ❌ | ✅ (Atomic fn) | ✅ (Atomic fn) | ❌ | ✅ |
| **Stock Transactions: Receive** | | | | | | |
| `stock.receive.create_draft` | 📝 | ✅ | ✅ | ✅ | ❌ | ✅ |
| `stock.receive.review` | ❌ | ✅ | ✅ | ✅ | ❌ | ✅ |
| `stock.receive.approve` | ❌ | ❌ | ✅ | ✅ | ❌ | ✅ |
| `stock.receive.post` | ❌ | ❌ | ✅ (Atomic fn) | ✅ (Atomic fn) | ❌ | ✅ |
| **Stock Transactions: Transfer** | | | | | | |
| `stock.transfer.create_draft` | ❌ | ✅ | ✅ | ✅ | ❌ | ✅ |
| `stock.transfer.dispatch` | ❌ | ✅ | ✅ | ✅ | ❌ | ✅ |
| `stock.transfer.receive_confirm`| ❌ | ✅ | ✅ | ✅ | ❌ | ✅ |
| `stock.transfer.approve` | ❌ | ❌ | ✅ | ✅ | ❌ | ✅ |
| **Stock Transactions: Count & Adjust** | | | | | | |
| `stock.count.initiate` | ❌ | ❌ | ✅ | ✅ | ❌ | ✅ |
| `stock.count.record_blind` | ✅ | ✅ | ✅ | ✅ | ❌ | ❌ |
| `stock.count.view_variance` | ❌ | ❌ | ✅ | ✅ | ✅ | ✅ |
| `stock.count.approve_adjust` | ❌ | ❌ | ✅ (Up to limit) | ✅ | ❌ | ✅ |
| `stock.adjust.direct` | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ *(Forbidden)* |
| **Workforce & Schedules** | | | | | | |
| `schedules.view` | 👁 (Self) | 👁 (Assigned WH) | ✅ (Assigned WH) | ✅ | 👁 | ✅ |
| `schedules.create_daily` | ❌ | ❌ | ✅ | ✅ | ❌ | ✅ |
| `schedules.create_weekly` | ❌ | ❌ | ❌ | ✅ | ❌ | ✅ |
| `schedules.reassign_worker` | ❌ | ❌ | ✅ | ✅ | ❌ | ✅ |
| **Tasks & Execution** | | | | | | |
| `tasks.view` | 👁 (Assigned to self)| 👁 (Assigned WH) | ✅ (Assigned WH) | ✅ | 👁 | ✅ |
| `tasks.create` | ❌ | ❌ | ✅ | ✅ | ❌ | ✅ |
| `tasks.assign` | ❌ | ❌ | ✅ | ✅ | ❌ | ✅ |
| `tasks.start_pause_resume` | ✅ (Self only) | ❌ | ❌ | ❌ | ❌ | ❌ |
| `tasks.complete_submit` | ✅ (Self only) | ❌ | ❌ | ❌ | ❌ | ❌ |
| `tasks.review_output` | ❌ | ✅ (Designated) | ✅ | ✅ | ❌ | ✅ |
| `tasks.approve_output` | ❌ | ❌ | ✅ | ✅ | ❌ | ✅ |
| **Productivity & Analytics** | | | | | | |
| `productivity.view_self` | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| `productivity.view_warehouse` | ❌ | 👁 (Summary) | ✅ (Assigned WH) | ✅ | ✅ | ✅ |
| `productivity.view_all_wh` | ❌ | ❌ | ❌ | ✅ | ✅ | ✅ |
| `productivity.export_excel_pdf`| ❌ | ❌ | ✅ | ✅ | ✅ | ✅ |
| **Audit Logs & Integrations** | | | | | | |
| `audit.view_own_actions` | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| `audit.view_warehouse_logs` | ❌ | ❌ | 👁 (Assigned WH) | ✅ | ✅ | ✅ |
| `audit.view_system_logs` | ❌ | ❌ | ❌ | ❌ | 👁 | ✅ |
| `integration.view_sync_logs` | ❌ | ❌ | 👁 | ✅ | 👁 | ✅ |
| `integration.trigger_sync` | ❌ | ❌ | ❌ | ✅ | ❌ | ✅ |
| `integration.resolve_conflict` | ❌ | ❌ | ❌ | ✅ | ❌ | ✅ |

---

## 4. Separation of Duties (SoD) Constraints

To guarantee compliance, internal controls, and prevent fraud or error, PostgreSQL constraints and triggers enforce the following inviolable rules:

### Rule SoD-1: Creator Cannot Approve (No Self-Approval)
```sql
-- Transaction Header Approval Constraint
CHECK (approved_by IS NULL OR approved_by <> created_by)
```
*A user who creates a stock transaction (Issue, Receive, Transfer, Adjustment) cannot be the approving user.*

### Rule SoD-2: Submitter Cannot Review/Approve Task Output
```sql
-- Task Review Constraint
CHECK (reviewed_by IS NULL OR reviewed_by <> employee_user_id)
```
*A worker who executes a task and logs the output cannot approve their own output or calculate their own productivity.*

### Rule SoD-3: Keeper Cannot Approve Major Discrepancy Adjustments
*Store Keepers can participate in blind counts, but only Store Managers or Operations Managers can approve inventory adjustments resulting from variance.*

---

## 5. Warehouse Data Scoping Architecture

Access to inventory records, ledger movements, tasks, and reports is strictly filtered by the `warehouse_user_access` junction table:

```
┌─────────────────────┐       ┌───────────────────────────┐       ┌─────────────────────┐
│     auth.users      │◄─────►│   warehouse_user_access   │◄─────►│     warehouses      │
└─────────────────────┘       └───────────────────────────┘       └─────────────────────┘
                                      │ user_id
                                      │ warehouse_id
                                      │ is_primary (boolean)
                                      │ can_write  (boolean)
```

### PostgreSQL RLS Function Helper:
```sql
CREATE OR REPLACE FUNCTION auth.get_user_warehouse_ids()
RETURNS SETOF UUID STABLE SECURITY DEFINER AS $$
BEGIN
    -- System Admin, Operations Manager, and General Manager have enterprise-wide access
    IF EXISTS (
        SELECT 1 FROM user_roles ur
        JOIN roles r ON ur.role_id = r.id
        WHERE ur.user_id = auth.uid() 
        AND r.role_key IN ('system_admin', 'operations_manager', 'general_manager')
    ) THEN
        RETURN QUERY SELECT id FROM warehouses WHERE is_active = true;
    ELSE
        -- Other roles are restricted to explicitly assigned warehouses
        RETURN QUERY 
        SELECT warehouse_id FROM warehouse_user_access 
        WHERE user_id = auth.uid() AND is_active = true;
    END IF;
END;
$$ LANGUAGE plpgsql;
```

---

## 6. Permission Inheritance & Conflict Resolution

1. **Additive Permissions**: If a user is assigned multiple roles (e.g., `store_keeper` and temporary `store_manager` proxy), their effective permissions are the **union** of all granted permissions.
2. **Explicit Denials Override**: Certain critical operations (such as deleting audit logs or making direct balance adjustments) have no grantable permission in the entire schema — they are structurally impossible regardless of role.
3. **Emergency Elevation**: No "super-user" bypass is available through the Flutter application. Supabase Service Role Key is strictly isolated from client distribution.
