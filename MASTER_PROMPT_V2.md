# MASTER PROJECT PROMPT V2.0
# ENTERPRISE WAREHOUSE MANAGEMENT & WORKFORCE PRODUCTIVITY SYSTEM

## Flutter + Supabase + PostgreSQL

> **Changelog from V1.0:**
> - Added API Contracts First requirement (Section 4.5)
> - Added Offline Conflict Resolution Strategy (Section 24.1)
> - Added Performance Budgets (Section 35)
> - Clarified Multi-tenancy Boundary (Section 2.1)
> - Strengthened Definition of Done per Phase (Section 31.x)
> - Added formal Task State Machine (Section 12.1)
> - Added Error Budget & Monitoring section (Section 36)
> - Added Testing Framework Versions (Section 4.4)
> - Clarified Drift justification (Section 4.3)
> - Added FCM Token lifecycle (Section 22.1)
> - Added Integration Risk Assessment as Phase 12 deliverable

---

# 1. YOUR ROLE

You are a complete senior software engineering team responsible for architecting, developing, testing, and documenting a professional Enterprise Warehouse Management System.

You must act as:

- Senior Software Architect.
- Senior Flutter Architect.
- ERP / WMS Architect.
- PostgreSQL Database Architect.
- Supabase Backend Engineer.
- Inventory Management Specialist.
- Workforce Management Specialist.
- Security Engineer.
- API Integration Engineer.
- QA Automation Engineer.
- DevOps Engineer.

You are responsible for delivering a real production-oriented business application, not a simple demonstration or CRUD application.

Your priorities:

1. Data integrity.
2. Inventory accuracy.
3. Security.
4. Employee productivity measurement.
5. Reliable approval workflows.
6. Multi-warehouse management.
7. Real-time synchronization.
8. Maintainability.
9. Scalability.
10. Professional user experience.

Use professional engineering practices throughout the entire project.

---

# 2. PROJECT VISION

Build an integrated Warehouse Management System (WMS) for a real company operating multiple warehouses.

The system must help management control:

- Inventory.
- Warehouse operations.
- Stock movements.
- Warehouse employees.
- Daily work schedules.
- Employee task assignments.
- Task execution.
- Time spent on each task.
- Actual employee productivity.
- Warehouse performance.
- Management approvals.
- Reporting and auditing.

The system must allow management to know exactly:

- What each warehouse employee worked on.
- Which warehouse and warehouse zone the employee worked in.
- Which task was assigned.
- When the employee started the task.
- When the employee completed it.
- How much active time the task required.
- How many units or items were processed.
- How much output was accepted or rejected.
- Whether the employee achieved the assigned target.
- How employee productivity compares with similar tasks.
- How employees should be distributed across warehouses and tasks.

This is primarily a WAREHOUSE MANAGEMENT AND WORKFORCE PRODUCTIVITY SYSTEM.

It is NOT a complete ERP accounting application.

It is NOT an HR Management System.

## 2.1 Multi-tenancy Boundary

**This system is designed for a single company only.**

The Supabase project will serve one company.

Row-based isolation (via RLS) is used for warehouse-level and role-level separation — not schema-per-tenant.

The system architecture must NOT block future multi-tenant extraction, but must NOT over-engineer for it now.

If a future multi-tenant requirement arises, a company_id column migration plan must be documented before implementation.

---

# 3. CONFIRMED BUSINESS DECISIONS

These decisions are final and must guide the architecture.

Company Structure:
- One company.
- Multiple warehouses.
- Multiple warehouse zones.
- Multiple users.
- Multiple employee accounts.

Inventory:
- Standard quantity-based items.
- Multiple measurement units.
- No initial requirement for serial-number tracking.
- No initial requirement for batch or expiry tracking.

Employee Management:
- Basic employee profiles.
- Employee skills.
- Warehouse assignments.
- Task assignments.
- Task execution history.
- Employee productivity analytics.

Workforce Management:
- Daily work planning.
- Weekly work planning.
- Employee task scheduling.
- Task timers.
- Pause and resume.
- Output measurement.
- Supervisor reviews.
- Productivity calculations.

External Systems:
- Integration with Al Yaseer software.
- Actual integration method must be verified before implementation.

Platform:
- Android application initially.

No Biometric Attendance:
- Do not integrate fingerprint machines.
- Do not build attendance tracking.
- Do not build employee check-in/check-out.
- Do not build biometric APIs.
- Do not build HR attendance workflows.

No Full HRMS:
- No payroll.
- No recruitment.
- No insurance administration.
- No employee contract management.
- No full HR administration.

Employee working time must be measured from actual assigned task execution sessions, not from biometric attendance.

---

# 4. TECHNOLOGY STACK

## 4.1 Frontend

Use:

- Latest stable Flutter.
- Compatible stable Dart SDK.
- Material Design 3.
- Clean Architecture.
- Feature-First Architecture.
- Riverpod State Management.
- GoRouter.
- Freezed.
- JSON Serializable.
- Responsive UI.
- Arabic and English localization.
- Full RTL support.
- Reusable UI components.

Verify actual stable package versions and compatibility before selecting dependencies.

Do not invent package versions.

## 4.2 Backend

Use:

- Supabase.
- PostgreSQL.
- Supabase Auth.
- Supabase Storage.
- Supabase Realtime.
- PostgreSQL Functions.
- PostgreSQL Triggers.
- Row Level Security.
- Edge Functions when appropriate.

## 4.3 Local Database

Use **Drift** over raw SQLite.

**Justification for Drift:**
- Type-safe query builder eliminates SQL injection risk in local queries.
- Schema migration system is built-in and versioned.
- Reactive streams integrate naturally with Riverpod.
- Code generation aligns with the existing Freezed/JSON Serializable toolchain.
- Offline sync queue can be modeled as a typed Drift table.

Use for:
- Local caching.
- Offline drafts.
- Synchronization queue.
- Idempotency keys.
- Conflict detection.

## 4.4 Testing Frameworks

Use these specific frameworks and versions (verify latest stable before use):

| Layer | Framework |
|-------|-----------|
| Unit & Widget | `flutter_test` (bundled with Flutter SDK) |
| Mocking | `mocktail` |
| Integration | `integration_test` (bundled with Flutter SDK) |
| Golden tests | `golden_toolkit` |
| PostgreSQL/RLS | pgTAP (via Supabase local dev) |
| BDD-style | `bdd_widget_test` (optional, if team prefers) |

Do not use `mockito` — prefer `mocktail` for null-safe code.

Verify package compatibility with current Flutter/Dart SDK before selection.

## 4.5 API Contracts First (NEW)

**Before implementing any Flutter feature screen**, the following must be documented and approved:

1. **Supabase RPC signatures**: function name, parameter types, return type, error codes.
2. **Edge Function endpoints**: URL path, HTTP method, request body schema, response schema, auth requirement.
3. **Realtime channel contracts**: channel name pattern, event names, payload schema per event.
4. **Storage bucket policies**: bucket name, allowed file types, max size, signed URL expiry.
5. **Error code catalogue**: a shared enum of all possible error codes returned by the backend, consumed by Flutter error handling.

These contracts form the boundary between backend and frontend development.

Changes to a contract require a version increment and approval before implementation.

## 4.6 Notifications

Use:

- Supabase Realtime.
- Firebase Cloud Messaging.
- In-app notifications.

## 4.7 Testing Coverage

Use:

- Unit Tests.
- Widget Tests.
- Integration Tests.
- PostgreSQL Tests.
- RLS Tests.
- Workflow Tests.
- Concurrency Tests.
- Offline Synchronization Tests.

---

# 5. SYSTEM MODULES

The project must contain these primary modules:

1. Authentication and User Management.
2. Roles and Permissions.
3. Warehouse Management.
4. Warehouse Zones Management.
5. Inventory Management.
6. Stock Ledger.
7. Stock Transactions.
8. Stock Approval Workflow.
9. Employee Management.
10. Employee Skills.
11. Workforce Scheduling.
12. Task Management.
13. Task Execution Tracking.
14. Employee Productivity Analytics.
15. Warehouse Productivity Analytics.
16. Management Dashboard.
17. Reports and Exports.
18. Realtime Notifications.
19. Activity Logs.
20. Offline Synchronization.
21. Al Yaseer Integration.
22. System Settings.

Develop these modules through the approved project phases.

---

# 6. MODULE: AUTHENTICATION & USER MANAGEMENT

Implement:

- Login.
- Logout.
- Forgot Password.
- Password Reset.
- User Profile.
- Profile Image.
- User Activation.
- User Deactivation.
- Session Management.
- Employee Account Linking.

User fields:

- User ID.
- Employee ID.
- Employee Number.
- Full Name.
- Email.
- Phone.
- Role.
- Active Status.
- Profile Image.
- Created Date.

An employee record does not necessarily require an application login account.

Use Supabase Auth.

Never expose the Supabase Service Role Key inside Flutter.

---

# 7. MODULE: RBAC & PERMISSIONS

Create a fully configurable Role-Based Access Control system.

Initial roles:

1. Store Worker.
2. Store Keeper.
3. Store Manager.
4. Operations Manager.
5. General Manager.
6. System Admin.

The permission model must be database-driven.

Required tables:

- roles.
- permissions.
- user_roles.
- role_permissions.
- user_permissions.
- warehouse_user_access.

Example permissions:

```
inventory.view
inventory.create
inventory.update
inventory.archive
inventory.approve

warehouse.view
warehouse.manage
warehouse.assign_users

stock.issue.create
stock.issue.review
stock.issue.approve

stock.receive.create
stock.receive.review
stock.receive.approve

stock.transfer.create
stock.transfer.approve

stock.count.create
stock.count.review
stock.count.approve

employees.view
employees.create
employees.update

tasks.create
tasks.assign
tasks.reassign
tasks.view

tasks.start
tasks.pause
tasks.resume
tasks.complete

tasks.review
tasks.approve

schedules.create
schedules.update

productivity.view
productivity.report

reports.view
reports.export

users.manage
roles.manage
settings.manage
```

Requirements:

- Permissions must be checked server-side.
- RLS must enforce data access.
- Users must be restricted to authorized warehouses.
- Workers should only see their assigned tasks and permitted inventory information.
- Supervisors should see tasks and employees under their authorized scope.
- Managers should manage permitted warehouses.
- Admin permissions must be configurable.

Never rely only on hiding Flutter buttons to enforce security.

Prevent users from approving their own requests where separation of duties is required.

---

# 8. MODULE: MULTI-WAREHOUSE MANAGEMENT

Support multiple warehouses in one company.

Warehouse fields:

- Warehouse ID.
- Warehouse Code.
- Warehouse Name.
- Description.
- Address.
- Manager.
- Active Status.
- Created Date.
- Updated Date.

Warehouse features:

- Create Warehouse.
- Edit Warehouse.
- Activate/Deactivate Warehouse.
- Assign Employees.
- Assign Store Keepers.
- Assign Store Managers.
- Define Warehouse Access.
- View Warehouse Inventory.
- View Warehouse Transactions.
- View Warehouse Performance.

## Warehouse Zones

Each warehouse can contain multiple zones or sections.

Examples:

- Receiving Area.
- Storage Area A.
- Storage Area B.
- Picking Area.
- Packing Area.
- Loading Area.
- Dispatch Area.

A task can be associated with a specific warehouse zone.

An item may have a default storage location or zone.

---

# 9. MODULE: INVENTORY MANAGEMENT

Design a complete inventory system.

## Item Fields

- Internal Item ID.
- Item Code.
- Barcode.
- Elyaseer Code.
- Item Name.
- Description.
- Category.
- Unit.
- Default Warehouse Location.
- Minimum Quantity.
- Item Image.
- Active Status.
- Created Date.
- Updated Date.

Support Arabic and English item names.

## Inventory Features

- Add Item.
- Edit Item.
- Archive Item.
- Search Item.
- Filter Items.
- Item Categories.
- Measurement Units.
- Barcode Scanner.
- QR Code Support.
- Barcode Generator.
- Item Images.
- Item History.
- Warehouse-Specific Balances.
- Low Stock Alerts.
- Minimum Quantity Settings per Warehouse.

An item can exist in multiple warehouses.

Each warehouse must have an independent quantity balance.

## Stock Balance Architecture

Use:

1. `stock_ledger` as the permanent inventory movement history.
2. `item_warehouse_balances` as a cached current balance.

The stock ledger is the historical source of truth.

The balance table is maintained transactionally for fast queries.

Do not allow direct manual stock balance updates from Flutter.

All stock changes must be performed through authorized database functions.

---

# 10. MODULE: STOCK TRANSACTIONS

Supported transaction types:

- Issue.
- Receive.
- Return.
- Stock Adjustment.
- Inventory Count.
- Warehouse Transfer.

## Transaction Header

- Transaction ID.
- Transaction Number.
- Transaction Type.
- Warehouse.
- Source Warehouse when applicable.
- Destination Warehouse when applicable.
- Created By.
- Employee.
- Customer or Supplier when applicable.
- Transaction Date.
- Notes.
- Attachments.
- Status.

## Transaction Lines

- Item.
- Quantity.
- Unit.
- Description.
- Notes.

Support multiple items per transaction.

## Approval Workflow

Default issue workflow:

```
Worker
→ Store Keeper Review
→ Store Manager Approval
→ Posted
```

Required statuses:

- Draft.
- Pending Review.
- Pending Approval.
- Approved.
- Posted.
- Rejected.
- Returned for Correction.
- Cancelled.

Maintain approval history:

- Reviewer.
- User.
- Date Time.
- Decision.
- Notes.
- Rejection Reason.

## Critical Stock Rules

1. Never deduct inventory when an issue request is initially created.
2. Deduct inventory only after final approval and successful posting.
3. Do not add received stock before approval.
4. Create ledger entries for every posted movement.
5. Prevent duplicate posting.
6. Use idempotency keys.
7. Use PostgreSQL atomic transactions.
8. Use row locking during stock updates.
9. Prevent negative inventory by default.
10. Handle simultaneous operations safely.
11. Never delete posted inventory transactions.
12. Reverse posted operations using reversal transactions.
13. Update source and destination warehouse balances atomically during transfers.

Inventory counts must compare:

- System Quantity.
- Actual Count.
- Variance.
- Adjustment Reason.

Count variances must be reviewed and approved.

---

# 11. MODULE: EMPLOYEE MANAGEMENT

This module is for managing warehouse workers and operational personnel.

It is NOT an HRMS.

Employee fields:

- Employee ID.
- Employee Number.
- Full Name.
- Phone.
- Email when available.
- Department.
- Job Title.
- Primary Warehouse.
- Employee Status.
- Joining Date when required.
- Profile Image.
- Application User Account when applicable.

Features:

- Add Employee.
- Edit Employee.
- Search Employee.
- Filter Employee.
- Employee Profile.
- Assign Warehouse.
- Temporary Warehouse Assignment.
- Assign Task.
- View Employee Task History.
- View Employee Productivity.
- Employee Skills Management.

## Employee Skills

Maintain skills to support intelligent work assignment.

Examples:

- Loading.
- Unloading.
- Receiving.
- Picking.
- Packing.
- Inventory Counting.
- Forklift Operation.
- Stock Organization.

Do not assume employee certifications without actual records.

---

# 12. MODULE: TASK MANAGEMENT

This is one of the most important modules.

The system must allow management to plan, distribute, monitor, and evaluate warehouse work.

## 12.1 Task State Machine (NEW)

Tasks follow a strict finite state machine. No state transition is permitted outside this diagram.

```
                    ┌─────────────┐
                    │   CREATED   │
                    └──────┬──────┘
                           │ Manager assigns employee(s)
                    ┌──────▼──────┐
                    │  ASSIGNED   │
                    └──────┬──────┘
                           │ Employee starts task
                    ┌──────▼──────┐
              ┌────►│  IN_PROGRESS│◄────┐
              │     └──────┬──────┘     │
              │            │ Employee   │
   Employee   │            │ pauses     │ Employee
   resumes    │     ┌──────▼──────┐     │ resumes
              └─────│   PAUSED   │─────┘
                    └──────┬──────┘
                           │ Employee submits
                    ┌──────▼──────┐
                    │  SUBMITTED  │
                    └──────┬──────┘
                           │ Supervisor reviews
              ┌────────────┼────────────┐
              │            │            │
       ┌──────▼─────┐  ┌───▼────┐  ┌───▼──────────┐
       │  APPROVED  │  │REJECTED│  │RETURNED_FOR_ │
       └────────────┘  └────────┘  │  CORRECTION  │
                                   └──────┬────────┘
                                          │ Employee corrects
                                          └──► IN_PROGRESS
```

**Transition ownership:**

| Transition | Authorized Actor |
|------------|-----------------|
| CREATED → ASSIGNED | Manager, Operations Manager |
| ASSIGNED → IN_PROGRESS | Assigned Employee |
| IN_PROGRESS → PAUSED | Assigned Employee |
| PAUSED → IN_PROGRESS | Assigned Employee |
| IN_PROGRESS → SUBMITTED | Assigned Employee |
| SUBMITTED → APPROVED | Supervisor (not the submitting employee) |
| SUBMITTED → REJECTED | Supervisor |
| SUBMITTED → RETURNED_FOR_CORRECTION | Supervisor |
| RETURNED_FOR_CORRECTION → IN_PROGRESS | Assigned Employee |
| ANY → CANCELLED | Manager (with reason required) |
| ASSIGNED → REASSIGNED | Manager (creates audit record) |

**Rules:**
- State transitions are enforced at the PostgreSQL function level, not only in Flutter.
- An employee cannot approve their own submitted task.
- A cancelled task cannot be reactivated; create a new task instead.

## Task Types

Initially support:

- Receiving Goods.
- Unloading Goods.
- Loading Goods.
- Issuing Items.
- Picking Orders.
- Packing Orders.
- Sorting Items.
- Organizing Shelves.
- Inventory Counting.
- Warehouse Transfers.
- Stock Replenishment.
- Preparing Orders.
- Other Warehouse Activities.

Task types must be configurable.

Each task type may define:

- Default Measurement Unit.
- Standard Productivity Metric.
- Default Target.
- Expected Duration.
- Required Skills.
- Quality Requirements.

Examples:

```
Receiving:          Units per Hour.
Picking:            Orders per Hour.
Loading:            Boxes per Hour.
Organizing:         Shelves Completed.
Inventory Counting: Items Counted per Hour.
```

Do not compare different task categories using inappropriate metrics.

## Task Information

Every task must have:

- Task ID.
- Task Number.
- Title.
- Task Type.
- Description.
- Warehouse.
- Warehouse Zone.
- Priority.
- Assigned Employees.
- Assigned Team when applicable.
- Planned Start.
- Planned End.
- Expected Duration.
- Target Quantity.
- Measurement Unit.
- Related Item when applicable.
- Related Stock Transaction when applicable.
- Supervisor.
- Status.
- Notes.
- Attachments.

Support:

- Single Employee Tasks.
- Multiple Employee Tasks.
- Team Tasks.
- Reassignment.
- Task Dependencies when required.
- Daily Tasks.
- Weekly Tasks.
- Recurring Tasks when required.

Prevent inappropriate conflicting assignments.

---

# 13. MODULE: DAILY WORKFORCE PLANNER

Create an important management screen called:

**DAILY WORKFORCE PLANNER.**

The manager must be able to distribute employees across:

- Warehouses.
- Zones.
- Tasks.
- Teams.
- Supervisors.

The screen must show:

- Available Employees for Planning.
- Assigned Employees.
- Active Tasks.
- Pending Tasks.
- Completed Tasks.
- Delayed Tasks.
- Warehouse Workload.
- Task Priorities.

## Planning Features

- Create Daily Plan.
- Create Weekly Plan.
- Assign Employee to Task.
- Assign Multiple Employees.
- Reassign Employee.
- Move Task Between Warehouses.
- Change Priority.
- Set Target Quantity.
- Set Expected Duration.
- Copy Previous Work Plan.
- Review Planned vs Actual.

Support calendar, timeline, and list views.

**IMPORTANT:**

This is a task scheduling system, not biometric attendance or employee check-in/check-out.

A worker who does not start a task must not automatically be considered absent.

The system must measure actual task execution only.

---

# 14. MODULE: TASK EXECUTION TRACKING

Each employee should see assigned tasks in the Android application.

Task actions:

- Start Task.
- Pause Task.
- Resume Task.
- Complete Task.
- Submit Results.

## Work Sessions

Record each employee's task execution independently.

Required fields:

- Session ID.
- Task ID.
- Employee ID.
- Start Date Time.
- End Date Time.
- Active Duration.
- Paused Duration.
- Session Status.

If multiple employees perform one task:

Each employee must have an independent work session.

Do not use only the team's total working time as every employee's individual time.

## Pause Reasons

- Waiting for Materials.
- Waiting for Approval.
- Equipment Problem.
- System Problem.
- Blocked by Another Task.
- Other.

Record every pause and resume event.

Separate:

- Active Work Time.
- Paused Time.
- Waiting Time.
- Total Elapsed Time.

Use trusted server timestamps for online operations.

For offline execution, preserve local event timestamps and synchronization metadata, and mark records requiring validation.

Prevent employees from silently changing task time or results.

Any correction must require authorization and generate an audit record.

Do not use hidden tracking or continuous GPS tracking.

---

# 15. MODULE: TASK OUTPUT & QUALITY CONTROL

Every measurable task should support output recording.

Examples:

- Number of Items Received.
- Number of Orders Picked.
- Number of Boxes Loaded.
- Number of Shelves Organized.
- Number of Items Counted.

Output fields:

- Task ID.
- Employee or Team.
- Measurement Unit.
- Target Quantity.
- Actual Quantity.
- Accepted Quantity.
- Rejected Quantity.
- Defect Quantity when applicable.
- Notes.
- Submission Date.
- Reviewer.

Allow partial progress updates when appropriate.

Do not treat submitted quantity as automatically accepted quantity.

Allow authorized supervisors to review and approve results.

Maintain output correction history.

---

# 16. MODULE: TASK REVIEW & APPROVAL

Default workflow:

```
Manager Creates Task
→ Employee Receives Assignment
→ Employee Starts Work
→ Employee Pauses / Resumes
→ Employee Completes Task
→ Employee Submits Results
→ Supervisor Reviews Results
→ Approved / Rejected / Returned for Correction
```

Record:

- Reviewer.
- Review Date.
- Approval Status.
- Accepted Quantity.
- Rejected Quantity.
- Notes.
- Rejection Reason.
- Correction History.

Support partial acceptance if appropriate.

Prevent workers from approving their own submitted results when independent review is required.

The approval workflow must be auditable.

---

# 17. MODULE: EMPLOYEE PRODUCTIVITY ANALYTICS

Productivity must be calculated from actual task sessions and approved work outputs.

Do not allow manually editing productivity percentages.

Required metrics:

- Total Assigned Tasks.
- Completed Tasks.
- Pending Tasks.
- Overdue Tasks.
- Active Working Hours on Tasks.
- Paused Hours.
- Planned Duration.
- Actual Duration.
- Target Output.
- Actual Output.
- Accepted Output.
- Rejected Output.
- Target Achievement Percentage.
- Output Per Active Hour.
- Average Task Duration.
- Quality Acceptance Rate.
- Task Completion Rate.

## Suggested Formulas

**Target Achievement:**
```
Accepted Output / Target Output × 100
```

**Output Per Active Hour:**
```
Accepted Output / Active Work Hours
```

**Quality Acceptance Rate:**
```
Accepted Output / Submitted Output × 100
```

**Task Completion Rate:**
```
Approved Completed Tasks / Assigned Tasks × 100
```

Handle:

- Zero Targets.
- Zero Working Hours.
- Incomplete Tasks.
- Non-Quantity Tasks.
- Shared Team Tasks.
- Different Measurement Units.

Use appropriate SQL Views or server-side calculations.

## Fair Productivity Evaluation

Do not compare employees performing unrelated jobs.

Productivity comparisons must consider:

- Task Type.
- Measurement Unit.
- Task Complexity.
- Warehouse Conditions.
- Waiting Time.
- Accepted Output.
- Quality.
- Assigned Workload.

Separate individual output from team output.

Provide historical trends instead of relying on one daily percentage.

Do not create an arbitrary employee performance score without documented and configurable rules.

---

# 18. MODULE: PRODUCTIVITY DASHBOARD

Create a professional management dashboard.

Display:

- Employees Assigned Today.
- Employees Currently Working.
- Total Tasks Today.
- Completed Tasks.
- Pending Tasks.
- Delayed Tasks.
- Tasks Awaiting Review.
- Total Active Task Hours.
- Total Accepted Output.
- Average Task Duration.
- Employee Productivity.
- Warehouse Productivity.
- Task Type Performance.
- Planned vs Actual Performance.
- Daily Productivity Trends.
- Weekly Productivity Trends.
- Monthly Productivity Trends.

Filters:

- Date.
- Employee.
- Warehouse.
- Warehouse Zone.
- Task Type.
- Supervisor.
- Task Status.

Support detailed drill-down from every dashboard metric to its underlying records.

---

# 19. MODULE: EMPLOYEE WORK HISTORY

Each employee must have a complete operational profile.

Display:

- Employee Information.
- Assigned Warehouses.
- Skills.
- Current Tasks.
- Previous Tasks.
- Task Types Performed.
- Task Durations.
- Active Work Time.
- Paused Time.
- Accepted Output.
- Rejected Output.
- Target Achievement.
- Supervisor Reviews.
- Historical Productivity.

Allow filtering by date and task type.

Allow export to Excel and PDF.

---

# 20. MODULE: DASHBOARD

Create a main management dashboard with:

## Warehouse Statistics

- Total Warehouses.
- Total Items.
- Total Inventory Quantity.
- Low Stock Items.
- Today's Transactions.
- Pending Transactions.
- Recent Inventory Movements.
- Inventory Alerts.

## Workforce Statistics

- Total Employees.
- Employees Assigned to Tasks.
- Active Tasks.
- Completed Tasks.
- Pending Tasks.
- Overdue Tasks.
- Productivity Indicators.
- Tasks Awaiting Review.

All dashboard data must be permission-aware.

Use Realtime for important updates.

---

# 21. MODULE: REPORTS

## Inventory Reports

- Inventory Balance.
- Warehouse Inventory.
- Item Movement History.
- Low Stock.
- Stock Transactions.
- Issue Reports.
- Receiving Reports.
- Warehouse Transfers.
- Stock Adjustment.
- Inventory Count Variance.

## Employee Reports

- Employee List.
- Employee Work History.
- Employee Task History.
- Employee Workload.
- Employee Performance.

## Workforce Reports

- Daily Work Schedule.
- Weekly Work Schedule.
- Task Completion Report.
- Task Duration Report.
- Planned vs Actual Report.
- Employee Productivity.
- Warehouse Productivity.
- Task Type Productivity.
- Quality and Rejection Report.
- Team Productivity.

Exports:

- Excel.
- PDF.

Use secure server-side reporting where appropriate.

Ensure reports use approved output data where applicable.

---

# 22. MODULE: REALTIME & NOTIFICATIONS

Use Supabase Realtime.

Notifications:

- New Task Assigned.
- Task Reassigned.
- Task Started.
- Task Delayed.
- Task Completed.
- Task Awaiting Review.
- Task Approved.
- Task Rejected.
- New Stock Transaction.
- Approval Needed.
- Transaction Approved.
- Transaction Rejected.
- Low Stock.
- Inventory Variance.

Use Firebase Cloud Messaging for push notifications.

Maintain in-app notification history.

Users must only receive notifications they are authorized to access.

## 22.1 FCM Token Lifecycle (NEW)

Manage FCM device tokens with the following rules:

1. **Registration**: Store the FCM token in `push_device_tokens` on first login and on each app launch if the token has changed.
2. **Rotation**: Firebase rotates tokens periodically. The app must listen to `FirebaseMessaging.instance.onTokenRefresh` and update the stored token immediately.
3. **Multi-device**: One user can have multiple active tokens (multiple devices). Send notifications to all active tokens.
4. **Expiry cleanup**: After sending a notification, check for `messaging/registration-token-not-registered` errors and delete the corresponding token from the database.
5. **Logout**: On logout, delete the current device's FCM token from the database.
6. **Token fields**: `id`, `user_id`, `device_token`, `platform` (android/ios/web), `created_at`, `last_used_at`, `is_active`.

---

# 23. MODULE: ACTIVITY LOG & AUDIT

Record important system actions.

Examples:

- Employee assigned to a task.
- Task reassigned.
- Employee started a task.
- Employee paused a task.
- Employee resumed a task.
- Employee submitted completed work.
- Supervisor approved results.
- Manager changed task targets.
- Store Keeper reviewed a transaction.
- Manager approved an inventory issue.
- Inventory quantity changed.

Every log must record:

- User ID.
- Employee ID when applicable.
- Action.
- Entity Type.
- Entity ID.
- Timestamp.
- Previous Values when appropriate.
- New Values when appropriate.
- Reason or Context.

Audit records must be append-only through secure database design.

Normal users cannot modify or delete them.

Record sensitive operations at database/server level.

---

# 24. MODULE: OFFLINE MODE

Provide controlled offline capability.

Use Drift for local database (see Section 4.3 for justification).

Support:

- Local Cache.
- Local Draft Transactions.
- Local Task Sessions.
- Local Task Progress.
- Pending Synchronization Queue.
- Sync Status.
- Automatic Retry.
- Conflict Detection.
- Idempotency Keys.
- Sync Logs.

## 24.1 Offline Conflict Resolution Strategy (NEW)

The following rules define what happens when an offline action conflicts with the server state upon sync:

**Resolution Strategy: Server-Authoritative with Flagging**

| Entity | Conflict Type | Resolution |
|--------|--------------|------------|
| Task Session (start/pause/resume) | Server has a newer session event | Preserve local timestamps; flag record as `PENDING_VALIDATION`; require supervisor confirmation |
| Task Output | Server output already reviewed | Block overwrite; present conflict to user; require manual resolution |
| Stock Draft | Item already posted by another transaction | Mark draft as `CONFLICT`; prevent posting; notify user |
| Employee Profile | Field updated both offline and online | Server wins; local change is discarded with notification to user |
| Notification read status | Diverged | Local read status is merged (union) |

**Conflict Record Fields:**
```
sync_conflicts:
  id UUID PRIMARY KEY
  entity_type TEXT
  entity_id UUID
  local_data JSONB
  server_data JSONB
  conflict_reason TEXT
  detected_at TIMESTAMPTZ
  resolved_at TIMESTAMPTZ
  resolved_by UUID
  resolution TEXT  -- 'server_won' | 'local_won' | 'manual' | 'discarded'
```

**Sync Timeout:** Offline queue entries older than 72 hours without successful sync must be flagged for manual review and must NOT be automatically discarded.

**Important:**

Offline inventory transactions are provisional and cannot change authoritative inventory balances until validated and posted by the server.

Offline task events must preserve their original local timestamps and synchronization timestamps.

Prevent duplicate task submissions.

Prevent conflicting offline assignments or unauthorized approvals.

Clearly show:

- Local Draft.
- Pending Sync.
- Sync Failed.
- Synchronized.
- Pending Review.
- Approved.
- Posted.

---

# 25. MODULE: AL YASEER INTEGRATION

The company uses an external Al Yaseer accounting/inventory software package.

Integration is an important project requirement.

However, its exact product, version, supported API, database structure, and connection method must be investigated first.

Do not invent an API.

Do not guess database tables.

Do not connect directly to a third-party database without documented authorization.

## Integration Architecture

Prepare:

- integration_systems.
- integration_entity_mappings.
- integration_sync_runs.
- integration_inbox.
- integration_outbox.
- integration_errors.

Support mapping for:

- Item Codes.
- External Item IDs.
- Elyaseer Codes.
- Barcode.
- Warehouse Codes.
- Transaction Numbers.
- Customers and Suppliers when required.

## Integration Methods

Investigate:

1. Official API.
2. Official SDK.
3. Vendor-Supported Connector.
4. Authorized Read-Only Database Integration.
5. Excel/CSV Import and Export.
6. Secure Local Integration Agent.

Do not write directly into the external software database unless officially supported and authorized.

## Integration Rules

Before actual synchronization, define which system is authoritative for each entity.

Create an integration responsibility matrix covering:

- Item Master.
- Item Codes.
- Warehouse Information.
- Inventory Balances.
- Stock Transactions.
- Customers.
- Suppliers.

Do not allow two systems to overwrite each other's stock quantities silently.

Provide:

- Sync Logs.
- Error Logs.
- Retry.
- Duplicate Prevention.
- Mapping Validation.
- Reconciliation Reports.
- Manual Synchronization.
- Scheduled Synchronization when supported.

Design the integration architecture now.

Implement the actual connector only after verifying the exact software version and supported integration method.

---

# 26. MODULE: STORAGE

Use Supabase Storage.

Support:

- Employee Images.
- Item Images.
- Transaction Attachments.
- Task Attachments.
- Supporting Documents.

Use private buckets for sensitive data.

Implement signed URLs.

Enforce access permissions.

Validate file size and file type.

---

# 27. DATABASE ARCHITECTURE

Database design must be completed before Flutter screen implementation.

Use PostgreSQL.

Design normalized relational tables.

Recommended table groups:

## Security

- user_profiles.
- roles.
- permissions.
- user_roles.
- role_permissions.
- user_permissions.
- warehouse_user_access.

## Organization

- departments.
- job_titles.
- employees.
- employee_skills.
- employee_skill_assignments.

## Warehouse

- warehouses.
- warehouse_zones.

## Inventory

- items.
- item_categories.
- units.
- item_barcodes.
- item_warehouse_settings.
- item_warehouse_balances.

## Stock Transactions

- stock_transactions.
- stock_transaction_lines.
- stock_transaction_approval_steps.
- stock_ledger.
- inventory_counts.
- inventory_count_lines.

## Workforce Planning

- task_types.
- work_schedules.
- work_schedule_items.
- work_tasks.
- work_task_assignments.
- task_dependencies when justified.

## Task Execution

- work_sessions.
- work_session_events.
- work_task_outputs.
- task_reviews.
- task_reassignment_history.

## Notifications

- notifications.
- user_notification_reads.
- push_device_tokens.

## Audit

- audit_logs.
- sync_conflicts. *(NEW — offline conflict records)*

## Integrations

- integration_systems.
- integration_entity_mappings.
- integration_sync_runs.
- integration_inbox.
- integration_outbox.
- integration_errors.

This table list is a starting point, not a reason to create unnecessary duplication.

Justify any additional tables.

## Database Requirements

Use:

- UUID Primary Keys where appropriate.
- Foreign Keys.
- Unique Constraints.
- Check Constraints.
- Appropriate Indexes.
- created_at.
- updated_at.
- created_by.
- updated_by where appropriate.
- Soft Delete where justified.
- Immutable Inventory History.
- Immutable Task Execution History where appropriate.
- Proper Timezone Handling.

Use `Africa/Cairo` as the default business timezone.

Store timestamps in UTC and convert them for display.

Define a clear source of truth for inventory balances and productivity metrics.

Never permit arbitrary client-side modification of critical derived data.

Use proper PostgreSQL transactions and row-level locking.

---

# 28. SECURITY & RLS

Implement complete database security.

Requirements:

- Enable RLS on all applicable exposed tables.
- Role-Based Access Control.
- Warehouse-Level Access Control.
- Task-Level Access Control.
- Employee Data Protection.
- Approval Authorization.
- Audit Log Protection.
- Private Storage Policies.
- Secure PostgreSQL Functions.
- Search Path Protection for SECURITY DEFINER Functions.
- Server-Side Validation.

Never expose Service Role Keys in Flutter.

Create separate tests for:

- Store Worker.
- Store Keeper.
- Store Manager.
- Operations Manager.
- General Manager.
- System Admin.

Test both authorized and unauthorized operations.

---

# 29. FLUTTER ARCHITECTURE

Use Feature-First Clean Architecture.

Suggested folder structure:

```
lib/
  app/
    app.dart
    router/
    theme/
    localization/

  core/
    constants/
    errors/
    network/
    database/
    security/
    sync/
    services/
    utils/
    widgets/

  features/
    authentication/
      data/
      domain/
      presentation/

    dashboard/
    employees/
    warehouses/
    inventory/
    stock_transactions/
    workforce/
    tasks/
    productivity/
    notifications/
    reports/
    integrations/
```

Each feature must use appropriate:

- Models.
- DTOs.
- Repositories.
- Repository Implementations.
- Services.
- Providers.
- Controllers or Notifiers.
- Screens.
- Reusable Widgets.
- Tests.

Do not put business logic inside Flutter screens.

---

# 30. USER INTERFACE REQUIREMENTS

Create a polished enterprise Material 3 application.

Support:

- Arabic.
- English.
- Full RTL.
- Responsive Phones.
- Tablets.
- Reusable Widgets.
- Consistent Design System.
- Professional Data Tables.
- Search and Filters.
- Loading States.
- Error States.
- Empty States.
- Offline Indicators.

*(Full screen list as defined in V1.0 Section 30 — unchanged)*

Implement screens progressively according to the approved development phases.

---

# 31. DEVELOPMENT ROADMAP

The project MUST be developed in the following phases.

Never skip a phase.

Never begin the next phase before receiving explicit approval.

Each phase ends with a **Definition of Done** checklist (see below).

## Phase Definition of Done Template

A phase is not complete until ALL of the following are true:

- [ ] All deliverables listed for the phase are present and complete.
- [ ] No TODO, placeholder, or stub remains in delivered code.
- [ ] All relevant tests have been written.
- [ ] Test commands and expected outputs are documented.
- [ ] Any architectural decision made during the phase is documented with justification.
- [ ] API contracts (if any) for the phase are documented.
- [ ] The developer has clearly stated: what is **Designed**, what is **Implemented**, what is **Executed**, and what is **Verified**.
- [ ] No new dependency has been introduced without version verification and justification.

---

## PHASE 0 — REQUIREMENTS & ARCHITECTURE DISCOVERY

Tasks:

1. Confirm the business requirements.
2. Document the project scope.
3. Identify all assumptions.
4. Identify unknown Al Yaseer integration requirements.
5. Document the inventory approval workflow.
6. Document the employee task workflow.
7. Define roles and permissions.
8. Define system boundaries.
9. Create the high-level architecture.
10. Prepare the implementation plan.

Deliverables:

- Scope Document.
- Architecture Overview.
- Business Workflow Diagrams.
- Role Matrix.
- Assumptions and Risks.
- Integration Discovery Plan.

Do not start implementation during Phase 0.

**STOP. Do not proceed to Phase 1 without explicit approval.**

---

## PHASE 1 — DATABASE ARCHITECTURE & DESIGN

This is the first implementation phase.

Deliver:

1. Complete ERD.
2. Database Schema.
3. All Tables and Columns.
4. Primary Keys.
5. Foreign Keys.
6. Relationships.
7. Constraints.
8. Indexes.
9. Triggers.
10. PostgreSQL Functions.
11. RLS Policies.
12. RBAC Design.
13. Stock Ledger Architecture.
14. Stock Approval Workflow.
15. Workforce Scheduling Architecture.
16. Task Execution Architecture (including State Machine enforcement).
17. Productivity Calculation Architecture.
18. Al Yaseer Integration Architecture.
19. Migration Scripts.
20. Seed Data.
21. Database Tests (using pgTAP).

Provide complete SQL code organized into ordered migration files.

The SQL must be designed for a clean Supabase project.

Explain execution order and prerequisites.

Do not claim SQL execution or verification unless it was actually performed.

**STOP. Do not proceed to Phase 2 without explicit approval.**

---

## PHASE 2 — SUPABASE BACKEND FOUNDATION

Implement:

- Supabase Configuration.
- Authentication.
- User Profiles.
- Roles.
- Permissions.
- RLS Policies.
- Secure Database Functions.
- Storage.
- Realtime.
- Initial Administrative User Setup.
- Backend Tests.
- API Contract Documentation (Section 4.5) for all Phase 2 interfaces.

**STOP. Do not proceed to Phase 3 without explicit approval.**

---

## PHASE 3 — FLUTTER PROJECT FOUNDATION

Implement:

- Flutter Project Creation.
- Dependencies (verified stable versions only).
- Project Structure.
- Material 3 Theme.
- Arabic and English Localization.
- RTL Support.
- Riverpod.
- GoRouter.
- Supabase Client.
- Error Handling.
- Logging.
- Reusable UI Components.
- Responsive Layout.
- Offline status indicator widget.

**STOP. Do not proceed to Phase 4 without explicit approval.**

---

## PHASE 4 — AUTHENTICATION & ADMINISTRATION
*(unchanged from V1.0)*

**STOP for approval.**

---

## PHASE 5 — EMPLOYEE & WAREHOUSE MANAGEMENT
*(unchanged from V1.0)*

**STOP for approval.**

---

## PHASE 6 — INVENTORY MANAGEMENT
*(unchanged from V1.0)*

**STOP for approval.**

---

## PHASE 7 — STOCK TRANSACTIONS & APPROVALS
*(unchanged from V1.0)*

**STOP for approval.**

---

## PHASE 8 — WORKFORCE PLANNING & TASK MANAGEMENT

Implement:

- Task Types.
- Work Schedules.
- Daily Workforce Planner.
- Weekly Work Planner.
- Task Creation.
- Task Assignments.
- Task Reassignment.
- Team Tasks.
- Warehouse Zone Assignments.
- **Task State Machine enforcement at PostgreSQL function level.**

**STOP for approval.**

---

## PHASE 9 — TASK EXECUTION & PRODUCTIVITY
*(unchanged from V1.0)*

**STOP for approval.**

---

## PHASE 10 — DASHBOARD, NOTIFICATIONS & REPORTS

Implement:

- Management Dashboard.
- Inventory Dashboard.
- Workforce Dashboard.
- Productivity Dashboard.
- Realtime Notifications.
- Firebase Push Notifications with FCM Token Lifecycle (Section 22.1).
- Excel Reports.
- PDF Reports.

**STOP for approval.**

---

## PHASE 11 — OFFLINE MODE

Implement:

- Drift local database setup.
- Local Caching.
- Offline Draft Tasks.
- Offline Task Sessions.
- Offline Progress.
- Offline Stock Drafts.
- Synchronization Queue.
- **Conflict Detection and Resolution (Section 24.1).**
- Idempotency.
- Retry (with exponential backoff).
- Sync Logs.
- Conflict review UI.

**STOP for approval.**

---

## PHASE 12 — AL YASEER INTEGRATION

Investigate and implement the verified integration.

Confirm:

- Exact Software Name.
- Manufacturer.
- Product Version.
- Database Type.
- API Availability.
- SDK Availability.
- Export and Import Formats.
- Vendor Restrictions.
- Source of Truth.
- Sync Direction.

**Additional Deliverable (NEW): Integration Risk Assessment**

Document the following before any integration code is written:

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|-----------|
| No official API available | Medium | High | Fall back to CSV import/export |
| Vendor restricts DB access | High | High | Use file-based connector only |
| Data format mismatch | Medium | Medium | Build mapping validation layer |
| Sync causes duplicate records | Medium | High | Idempotency keys + reconciliation |

Implement the safest supported connector.

If required information is unavailable, document the blocker and deliver the integration framework without inventing technical details.

**STOP for approval.**

---

## PHASE 13 — TESTING, SECURITY & PRODUCTION DEPLOYMENT
*(unchanged from V1.0, plus performance tests against Performance Budgets in Section 35)*

---

# 32. STRICT DEVELOPMENT RULES

*(All 27 rules from V1.0 unchanged, plus the following additions)*

28. Never implement a Flutter feature screen before its API contract is documented and approved (Section 4.5).
29. Never implement offline sync without defining the conflict resolution strategy first (Section 24.1).
30. Never implement a task state transition outside the approved State Machine (Section 12.1).
31. Never introduce a new dependency mid-phase without documenting the version and justification.
32. Always respect Performance Budgets (Section 35) during implementation and report deviations.

---

# 33. ACCEPTANCE CRITERIA

*(All scenarios from V1.0 unchanged)*

**Additional Acceptance Criteria (NEW):**

## Task State Machine

- Unauthorized state transitions are rejected at the database level.
- An employee cannot approve their own submitted task.
- A cancelled task cannot be reactivated.

## Offline Sync

- Conflicts are detected and recorded in `sync_conflicts`.
- Conflicting records are flagged, not silently overwritten.
- Offline queue entries older than 72 hours are flagged for manual review.
- The UI shows the correct sync status at all times.

## Performance

- Dashboard initial load meets the target defined in Section 35.
- Realtime task update delivery meets the latency target in Section 35.

---

# 34. FIRST EXECUTION INSTRUCTION

*(Same as V1.0 — Start from Phase 0, then Phase 1 only)*

**START THE PROJECT FROM ZERO.**

Do not generate Flutter application screens yet.

Start with PHASE 0, followed by PHASE 1 only.

Your first response must include all 19 deliverables listed in Phase 1.

Do not proceed to Phase 2.

Do not start Flutter development.

Do not assume this phase is approved.

Finish the requested deliverables, clearly state what is designed versus actually tested, and wait for explicit approval.

**THESE REQUIREMENTS ARE THE SINGLE SOURCE OF TRUTH FOR THIS PROJECT.**

---

# 35. PERFORMANCE BUDGETS (NEW)

Define measurable performance targets. These targets must be validated during Phase 13.

| Screen / Operation | Target | Measurement Method |
|-------------------|--------|--------------------|
| Dashboard initial load (cold) | < 3 seconds | Integration test with stopwatch |
| Dashboard initial load (warm/cached) | < 1 second | Integration test with stopwatch |
| Task list (100 items, paginated) | < 1.5 seconds | Integration test |
| Realtime task notification delivery | < 2 seconds | Manual test with two devices |
| Barcode scan to item lookup | < 500 ms | Integration test |
| Stock posting (atomic DB function) | < 2 seconds | pgTAP with EXPLAIN ANALYZE |
| Offline sync queue flush (10 items) | < 10 seconds | Integration test on slow network |
| Report generation (100 rows, PDF) | < 5 seconds | Integration test |
| App cold start to Login screen | < 3 seconds | Flutter DevTools trace |

If a target cannot be met, document the reason and propose a mitigation before Phase 13 approval.

---

# 36. ERROR BUDGET & MONITORING (NEW)

Define acceptable error thresholds and monitoring requirements for production.

## Error Thresholds

| Error Type | Acceptable Rate | Action if Exceeded |
|-----------|----------------|--------------------|
| Supabase RPC failure rate | < 0.5% per hour | Alert + investigate |
| Sync queue failure rate | < 2% per day | Automatic retry + alert at 5% |
| FCM delivery failure rate | < 1% per day | Token cleanup + investigate |
| Database connection errors | < 0.1% per hour | Immediate alert |
| RLS policy violation errors | 0% (any is a security event) | Immediate alert + audit log |

## Monitoring Requirements

Implement the following before production deployment (Phase 13):

1. **Supabase Dashboard**: Review function error rates weekly.
2. **Sync Log Table**: Dashboard widget showing sync failures in the last 24 hours.
3. **Low Stock Alerts**: Automated notification when item quantity drops below minimum.
4. **Audit Log Review**: Admin screen for reviewing audit logs (filterable by user, action, entity).
5. **Integration Error Dashboard**: Screen showing Al Yaseer sync errors with retry capability.

Do not use external APM tools unless the user requests them. Use Supabase built-in observability and Flutter DevTools first.
