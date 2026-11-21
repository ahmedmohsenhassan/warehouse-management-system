# 05 — Al Yaseer Integration Discovery Plan

> **Enterprise Warehouse Management & Workforce Productivity System**
> Document Version: 1.0 — Phase 0 Discovery
> Classification: Integration Architecture & Vendor Due Diligence

---

## 1. Objective & Mandate

The client organization utilizes **Al Yaseer** (اليَسير) as their primary accounting and enterprise back-office package. Seamless interoperability between this mobile WMS and Al Yaseer is critical for synchronized stock valuation and sales fulfillment.

### Core Architectural Directive:
> **"Do not invent an API. Do not guess database tables. Do not connect directly to a third-party database without documented authorization."**

This document establishes the structured discovery framework, vendor questionnaire, technical evaluation criteria, and staged integration architecture to be executed prior to Phase 12.

---

## 2. Integration Responsibility & Source of Truth Matrix

Before exchanging a single data packet, the authoritative system of record for each business entity is explicitly defined:

| Business Entity | System of Record (SSoT) | Secondary / Consumer | Sync Direction | Sync Frequency | Conflict Resolution Rule |
|---|---|---|:---:|---|---|
| **Item Master (SKU, Name, Barcode)** | Al Yaseer (ERP) | Mobile WMS | Al Yaseer → WMS | Daily / On-Demand | Al Yaseer is master; WMS cannot alter master SKU data. |
| **Warehouse Master & Locations** | Mobile WMS | Al Yaseer | WMS → Al Yaseer | Static / Manual | WMS defines physical zones; mapped to Al Yaseer branches. |
| **Physical Stock Balances** | Mobile WMS (Ledger) | Al Yaseer | WMS → Al Yaseer | Real-time / Scheduled | WMS is physical source of truth; updates ERP ledger upon posting. |
| **Purchase Receipts (Receiving)** | Mobile WMS | Al Yaseer | WMS → Al Yaseer | Event-driven (Posted) | WMS logs physical arrival; feeds ERP Accounts Payable. |
| **Sales Order Picking / Issues** | Al Yaseer (Sales Orders) | Mobile WMS | Bi-directional | Near Real-time | Al Yaseer issues pick request; WMS returns confirmed picked lines. |
| **Stock Adjustments & Counts** | Mobile WMS | Al Yaseer | WMS → Al Yaseer | Post-approval | Approved count variance posts adjustment journal into ERP. |
| **Employee Task & Productivity** | Mobile WMS | None (Isolated) | Internal Only | Real-time | Al Yaseer has no workforce/task subsystem. |

---

## 3. Technical Integration Channels Evaluation

We evaluate five possible integration vectors in order of security, maintainability, and vendor support:

```
                  ┌─────────────────────────────────────────────────────────┐
                  │           AL YASEER ERP / ACCOUNTING SYSTEM            │
                  └───────────────────────────┬─────────────────────────────┘
                                              │
              ┌───────────────────────────────┼──────────────────────────────┐
              │                               │                              │
     [Tier 1: Preferred]             [Tier 2: Direct DB]           [Tier 3: File Staging]
      REST / SOAP API or             Read-Only View / Staging       Automated SFTP / Folder
      Vendor Supported SDK           Table in SQL Server            CSV / Excel Exchange
              │                               │                              │
              ▼                               ▼                              ▼
  ┌───────────────────────┐       ┌───────────────────────┐      ┌────────────────────────┐
  │ Supabase Edge Function│       │ Integration Agent     │      │ Integration In/Outbox  │
  │ (OAuth2 / API Key)    │       │ (Secure Tunnel / VPN) │      │ Parser & Validator     │
  └───────────┬───────────┘       └───────────┬───────────┘      └───────────┬────────────┘
              │                               │                              │
              └───────────────────────────────┼──────────────────────────────┘
                                              ▼
                             ┌─────────────────────────────────┐
                             │    WMS INTEGRATION SUBSYSTEM    │
                             │  (Mappings, Inbox, Outbox,      │
                             │   Idempotency, Audit Logs)      │
                             └─────────────────────────────────┘
```

### Detailed Evaluation:

| Tier | Method | Feasibility | Security & Integrity | Architecture Recommendation |
|:---:|---|:---:|:---:|---|
| **Tier 1** | **Official REST/SOAP API** | High if vendor supports modern modules | Maximum. Vendor validates transactions internally. | **Primary Target**. If available, build authenticated HTTPS connector via Supabase Edge Functions. |
| **Tier 2** | **Authorized SQL Read-Only Staging** | High for on-premise installations (usually MS SQL Server) | Medium. Requires secure VPN/wireguard tunnel and strictly read-only service account. | **Viable Alternative** for master item extraction. Outbound transactions written only to a dedicated staging table. |
| **Tier 3** | **Scheduled CSV / Excel Interchange** | 100% universal across all legacy software | High resilience, zero direct DB risk. Slight latency (scheduled batches). | **Guaranteed Fallback**. Even if vendor is unresponsive, all ERPs can export/import flat files. |
| **Tier 4** | **Direct Unmanaged DB Writes** | Structurally possible | **ZERO (Unacceptable)**. Bypasses ERP validation triggers and invalidates vendor support contracts. | **STRICTLY PROHIBITED**. Under no circumstances will WMS execute direct `INSERT/UPDATE` on Al Yaseer core tables. |

---

## 4. Vendor Discovery Questionnaire

This questionnaire will be formally dispatched to Al Yaseer's technical support/integration team during Phase 0:

### Category A: System Identification
1. What is the exact commercial edition, build number, and release year of the Al Yaseer software in production?
2. What underlying Database Management System (DBMS) does it operate on? (e.g., Microsoft SQL Server 2014/2019, Oracle, MySQL, Microsoft Access)?
3. Is Al Yaseer hosted on a local on-premises server, a local LAN host, or in a private cloud environment?

### Category B: Integration Capabilities
4. Does Al Yaseer provide an official API (REST, SOAP, or RPC) or an external integration module?
5. Does Al Yaseer provide an SDK or command-line CLI tool for batch transaction imports?
6. Does Al Yaseer support automated background export/import via hot folders (e.g., watching a directory for XML/JSON/CSV)?
7. Are there standard templates or specifications for importing Goods Receipts, Issue Vouchers, and Stock Adjustments?

### Category C: Data Dictionary & Identifiers
8. What is the unique primary key structure for inventory items (Item Code, Internal Auto-ID, Barcode)?
9. How are multiple warehouses/branches represented within the Al Yaseer database?
10. Does Al Yaseer support alphanumeric transaction reference numbers generated by third-party systems?

---

## 5. Staged Integration Architecture in PostgreSQL

Regardless of the eventual communication protocol selected, the WMS database schema in Phase 1 will include a decoupled, audit-ready integration staging subsystem:

```sql
-- 1. Integration System Registry
CREATE TABLE integration_systems (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    system_code TEXT UNIQUE NOT NULL, -- 'al_yaseer'
    system_name TEXT NOT NULL,
    connection_type TEXT NOT NULL,    -- 'api', 'db_staging', 'file_exchange'
    is_active BOOLEAN DEFAULT true,
    config JSONB DEFAULT '{}'::jsonb,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- 2. Entity Mapping Table (Cross-Reference)
CREATE TABLE integration_entity_mappings (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    system_id UUID REFERENCES integration_systems(id),
    entity_type TEXT NOT NULL,        -- 'item', 'warehouse', 'uom'
    internal_id UUID NOT NULL,        -- WMS UUID
    external_code TEXT NOT NULL,      -- Al Yaseer Code
    sync_status TEXT DEFAULT 'synced',
    last_synced_at TIMESTAMPTZ,
    UNIQUE(system_id, entity_type, external_code)
);

-- 3. Staged Outbox (Transactions waiting to push to Al Yaseer)
CREATE TABLE integration_outbox (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    system_id UUID REFERENCES integration_systems(id),
    event_type TEXT NOT NULL,         -- 'stock_posted', 'variance_approved'
    payload JSONB NOT NULL,
    status TEXT DEFAULT 'pending',     -- 'pending', 'processing', 'completed', 'failed'
    retry_count INT DEFAULT 0,
    idempotency_key TEXT UNIQUE NOT NULL,
    error_message TEXT,
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- 4. Staged Inbox (Data received from Al Yaseer waiting for processing)
CREATE TABLE integration_inbox (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    system_id UUID REFERENCES integration_systems(id),
    entity_type TEXT NOT NULL,        -- 'item_master', 'sales_order'
    external_id TEXT NOT NULL,
    payload JSONB NOT NULL,
    status TEXT DEFAULT 'pending',
    processed_at TIMESTAMPTZ,
    error_message TEXT,
    created_at TIMESTAMPTZ DEFAULT NOW()
);
```

---

## 6. Phase 12 Execution Roadmap

```
[Phase 0]
  │ Questionnaire dispatched & Integration Responsibility Matrix signed off
  ▼
[Phase 1-11]
  │ Core WMS & Mobile App built with generic Integration Staging Tables
  ▼
[Phase 12: Active Integration]
  ├── Step 1: Ingest Questionnaire Responses & Vendor Specs
  ├── Step 2: Establish Secure Tunnel / API Auth Handshake
  ├── Step 3: Implement Entity Mapping Adapters (Item SKU & Warehouse Codes)
  ├── Step 4: Run Dry-Run Reconciliation (Compare WMS balances vs Al Yaseer balances)
  ├── Step 5: Test Staged Transaction Ingestion (Draft Mode)
  └── Step 6: Go-Live with Automated Sync & Error Handling Dashboard
```
