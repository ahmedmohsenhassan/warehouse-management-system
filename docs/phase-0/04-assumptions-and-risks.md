# 04 — Assumptions & Risk Register

> **Enterprise Warehouse Management & Workforce Productivity System**
> Document Version: 1.0 — Phase 0 Discovery
> Classification: Project Governance & Architecture Risk

---

## 1. Project Assumptions

### 1.1 Business Assumptions
1. **Single Company Scope**: The system serves one registered legal entity operating multiple warehouses. Multi-tenant partitioning is not required in Phase 1.
2. **Standard Non-Serialized Inventory**: Items are tracked by standard SKU/Code, Barcode, and quantity in defined units of measure (UOM). No individual serial numbers, lot tracking, or expiry dates are required for initial deployment.
3. **Productivity Measured via Tasks Only**: Employee performance is strictly derived from verified, supervisor-reviewed task execution sessions. No biometric devices, gate access, or external timeclock data will be integrated.
4. **Shift Operations**: Operations occur within standard shifts managed under the Cairo timezone (`Africa/Cairo`).
5. **Separation of Concerns**: General ledger accounting, accounts payable, accounts receivable, and human resource payroll remain outside this system's functional boundary.

### 1.2 Technical Assumptions
1. **Target Platform**: Initial mobile frontend is built exclusively for Android (targeting API 26+ / Android 8.0 through Android 14+), optimized for handheld mobile devices and warehouse barcode scanner sleds.
2. **Backend Infrastructure**: Supabase Cloud (Managed PostgreSQL 15+, Supabase Auth, GoTrue, Realtime Engine, Storage) will serve as the primary backend.
3. **Database as Source of Truth**: All transactional constraints, idempotency checks, inventory ledger postings, and state machine transitions reside in PostgreSQL functions, triggers, and Row Level Security (RLS) policies.
4. **Offline Capability via Drift**: Workers operating in dead zones require offline task execution and barcode capture, synchronized reliably via an SQLite/Drift queue.
5. **Push Notifications**: Firebase Cloud Messaging (FCM) is assumed to have reliable Google Play Services availability on target Android hardware.

### 1.3 Integration Assumptions
1. **Al Yaseer System Isolation**: Al Yaseer is the external ERP/accounting package. Direct unmanaged write access to its database is strictly prohibited until official API/SDK/Staging connector documentation is formally provided.
2. **Master Authority Principle**: Master entities (e.g., Item Catalog, Supplier Catalog) will have a strictly defined Single Source of Truth (SSoT) to prevent circular write conflicts.

---

## 2. Risk Register & Mitigation Matrix

| Risk ID | Category | Risk Description | Likelihood | Impact | Severity | Mitigation Strategy | Owner |
|:---:|---|---|:---:|:---:|:---:|---|:---:|
| **RSK-01** | Integration | **Al Yaseer API unavailability or vendor refusal**<br>Vendor does not offer modern REST APIs or direct database access. | High | High | **CRITICAL** | Design a modular, decoupled integration adapter supporting staged file-based interchange (CSV/Excel scheduled export/import) as an official fallback. | Backend Architect |
| **RSK-02** | Data Integrity | **Negative inventory or race conditions during peak hours**<br>Simultaneous issue requests cause stock balance corruption. | Med | High | **HIGH** | Use PostgreSQL pessimistic row-locking (`FOR UPDATE`) in atomic posting functions. Forbid direct table updates from client. | Database Architect |
| **RSK-03** | Security | **RLS policy bypass or misconfiguration**<br>Store workers view unauthorized warehouse data or approve transactions. | Low | Critical | **HIGH** | Write comprehensive automated pgTAP test suites covering both positive and negative authorization cases for all 6 roles. | Security Engineer |
| **RSK-04** | Offline Sync | **Clock manipulation or offline timestamp drift**<br>Workers adjust device clock to inflate recorded productivity hours. | Med | Med | **MEDIUM** | Record device boot uptime alongside monotonic timestamps. Flag offline sessions exceeding variance thresholds for supervisor review. | Mobile Architect |
| **RSK-05** | Offline Sync | **Stale offline drafts conflicting with posted inventory**<br>Worker creates offline issue for items already depleted online. | Med | High | **HIGH** | Mark offline transactions as provisional drafts. Server validates stock availability at moment of atomic posting. | Backend Engineer |
| **RSK-06** | Hardware | **Barcode scanner hardware incompatibility**<br>Industrial Android scanners (e.g., Zebra, Honeywell) fail to input codes. | Med | Med | **MEDIUM** | Implement standard camera scanning via ML Kit AND hardware broadcast intent / keyboard wedge listeners in Flutter. | Mobile Engineer |
| **RSK-07** | Network | **Supabase Realtime dropouts in large metal warehouses**<br>Realtime WebSocket connections disconnect frequently. | High | Low | **MEDIUM** | Design UI with optimistic local states and automatic exponential backoff reconnection. Rely on periodic REST polling fallback. | Mobile Architect |
| **RSK-08** | Performance | **Dashboard query latency over large stock ledger**<br>Ledger table grows to millions of rows, slowing report generation. | Med | Med | **MEDIUM** | Maintain pre-aggregated transactional cache tables (`item_warehouse_balances`). Index ledger by `(warehouse_id, item_id, created_at)`. | Database Architect |
| **RSK-09** | Operations | **Worker resistance to digital task timers**<br>Workers forget to pause/complete tasks or log incorrect quantities. | High | Med | **HIGH** | Implement intuitive one-touch UI, mandatory pause reason dialogs, and supervisor override/correction workflows with full audit trails. | UX Designer / PM |
| **RSK-10** | Compliance | **Unauthorized self-approval of inventory requests**<br>Store manager approves transactions they drafted. | Low | High | **HIGH** | Enforce database-level check constraints: `CHECK (approved_by <> created_by)`. Incur immediate transaction rollback on violation. | Database Architect |
| **RSK-11** | Scalability | **Supabase connection pooling exhaustion**<br>Spike in concurrent warehouse devices drains PostgreSQL pool. | Med | High | **HIGH** | Use Supabase Supavisor connection pooler in Transaction Mode. Optimize Flutter repository connections to avoid lingering connections. | DevOps Engineer |
| **RSK-12** | Storage | **Uncontrolled attachment uploads consuming cloud storage**<br>High-resolution camera photos of damaged stock exceed storage budget. | High | Low | **LOW** | Enforce client-side image compression (max 1920x1080 JPEG at 80% quality) and server-side Supabase storage bucket size limits (max 5MB). | Mobile Engineer |
| **RSK-13** | Notification | **FCM notification delivery delays or background killed**<br>Battery saver modes on aggressive Android OEM skins kill background push. | Med | Med | **MEDIUM** | Combine FCM push for external alerts with Supabase Realtime in-app channels and high-priority FCM notification payloads. | Mobile Engineer |
| **RSK-14** | Architecture | **Uncontrolled scope creep toward full HR/Payroll**<br>Stakeholders request leave management, salaries, and attendance. | Med | High | **HIGH** | Reiterate strict Phase 0 boundaries in all milestone sign-offs. Reaffirm that productivity metrics measure operational output only. | Project Manager |
| **RSK-15** | Data Loss | **Accidental deletion of audit history**<br>Privileged user purges logs to cover operational discrepancy. | Low | Critical | **CRITICAL** | Revoke `DELETE` and `UPDATE` permissions on `audit_logs` table for all roles including `postgres` role via trigger prevention. Append-only ledger. | Security Engineer |

---

## 3. Technical & Environmental Constraints

1. **Operating Environment**: Dusty, metallic warehouse environments with intermittent Wi-Fi coverage and ambient temperature extremes.
2. **Hardware Constraints**: Diverse Android devices ranging from standard budget smartphones to specialized ruggedized devices (IP65/IP67 rated).
3. **Data Residency & Compliance**: Data hosted in compliant cloud regions with daily automated PostgreSQL WAL backups and point-in-time recovery (PITR).
4. **Zero Client Secrets**: The Supabase Service Role Key will NEVER be compiled into the Flutter Android APK. All privileged operations occur via security definer RPC functions with internal permission checks.

---

## 4. Open Questions & Required Stakeholder Decisions

The following architectural questions are formally submitted to the project steering committee for confirmation:

| # | Question / Topic | Proposed Recommendation | Impacted Module |
|---|---|---|---|
| **Q1** | What is the exact deployment method of Al Yaseer (Cloud, On-Premise SQL Server, Local Access MDB)? | Await vendor questionnaire response; prepare CSV export/import adapter as default fallback. | Module 25 (Integration) |
| **Q2** | Should warehouse supervisors be able to edit task output quantities directly, or must corrections create an immutable delta adjustment? | Force delta adjustments with required reason logging to maintain complete auditability. | Module 15 (Task Output) |
| **Q3** | Are standard task productivity targets fixed company-wide, or variable by warehouse and zone difficulty? | Support warehouse-specific override parameters in `task_types`. | Module 12 (Task Types) |
| **Q4** | What is the maximum acceptable latency for low-stock alert notifications? | Under 2 minutes via Supabase Realtime + FCM push notifications. | Module 22 (Notifications) |
| **Q5** | Should stock counts be strictly "blind" (worker counts without knowing system balance) to prevent bias? | Yes, blind counting is the gold standard for enterprise inventory auditing. | Module 10 (Inventory Count) |
