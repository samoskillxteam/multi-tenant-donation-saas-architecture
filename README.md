# Multi-Tenant Financial Governance Platform

### Architecture Case Study (Laravel SaaS)

---

## Executive Summary

This project documents the architecture and engineering decisions behind a **multi-tenant donation and membership governance platform** designed to support independent church entities operating under hierarchical oversight structures.

The system was built to:

* Enforce strict financial traceability
* Support multi-currency normalization
* Implement hierarchical governance via RBAC
* Enable offline-to-online financial data synchronization
* Maintain audit-ready compliance logging
* Provide operational analytics across distributed entities

Rather than optimizing purely for feature velocity, the architecture prioritizes **governance integrity, financial correctness, and long-term scalability**.

---

## 🎥 System Walkthrough (2-Minute Silent Demo)

A short visual walkthrough demonstrating:

- Multi-tenant role scoping
- Donation lifecycle & approval workflow
- Multi-currency normalization
- Governance enforcement constraints
- Audit logging & analytics filtering

## 🎥 System Walkthrough
[![Watch the demo](https://img.shields.io/badge/Watch-5--Minute%20Demo-blue?style=for-the-badge)](https://www.loom.com/share/b73fad63cb0e4e50b0858dc3a60c1248)

---

# 1️⃣ System Context & Design Constraints

### Operating Constraints

* Multiple independent churches under grouped oversight
* Financial operations across different currencies
* Limited technical operators (non-technical administrators)
* Need for strict leadership hierarchy enforcement
* Offline donation ingestion from remote locations
* Audit traceability for financial changes
* SMS + Email communication across regions

### Architectural Philosophy

* Modular monolith over microservices (team size & operational efficiency)
* Database-level integrity + model-level enforcement
* Service-layer abstraction for extensibility
* Queue-based communication for reliability
* Governance constraints enforced in code, not UI

---

# 2️⃣ Multi-Tenancy Strategy

### Approach: Scoped Relational Tenancy (Single Database)

Instead of database-per-tenant or subdomain tenancy, the system uses:

* Foreign key scoping (`church_id`, `group_id`)
* Computed accessibleChurchIds() resolution
* Guard-separated authentication
* Policy-level enforcement

### Why Not Separate Databases?

Rejected due to:

* Operational complexity
* Migration overhead
* Reporting aggregation challenges
* Increased infrastructure cost

The selected model supports centralized reporting while preserving strict access boundaries.

---

# 3️⃣ Hierarchical RBAC Enforcement

The platform enforces structured governance across multiple levels:

* Super Admin
* Group Admin
* Church Admin
* Manager (scoped by purpose or church)
* PCF Leader / Cell Leader
* Member

### Key Architectural Decisions

✔ Role hierarchy mapped to financial scope
✔ Model-level enforcement using booted() lifecycle hooks
✔ Automatic synchronization of church-group relationships
✔ Prevention of leadership demotion without replacement
✔ Single-leader enforcement for structured units

Governance constraints are enforced at the **data layer**, preventing UI bypass vulnerabilities.

---

# 4️⃣ Financial Engine & Multi-Currency Normalization

## Core Design Goal

Maintain financial reporting consistency across geographically distributed entities.

### Dual-Currency Storage Model

Each donation stores:

* Original currency + original amount
* Normalized base currency value
* Exchange rate snapshot at time of transaction

### Why Snapshot Exchange Rates?

To prevent retroactive financial distortion in reporting.

If exchange rates fluctuate, historical financial records remain stable and audit-safe.

---

## Donation Lifecycle

1. Submission (Online or Offline API)
2. Purpose resolution
3. Role-scoped approval workflow
4. Notification dispatch
5. Audit log generation

Approval workflow prevents:

* Unauthorized fund categorization
* Mis-scoped financial reporting
* Cross-church fund leakage

---

# 5️⃣ Offline Sync API (Distributed Ingestion)

Designed for environments with intermittent connectivity.

### Capabilities

* JSON-based donation ingestion endpoint
* Idempotent request validation
* Automatic user provisioning for unknown donors
* Error logging + safe fallback behavior

### Risk Mitigation

* Rate limiting on ingestion
* Validation guardrails
* Structured error response logging
* No direct financial auto-approval

This ensures data reliability while supporting distributed submission channels.

---

# 6️⃣ Audit Logging & Compliance

A centralized AuditLogger service tracks:

* Donation creation & approval
* Role changes
* Profile updates
* Church reassignment attempts

Each entry stores:

* Actor
* Entity affected
* Old state
* New state
* Timestamp

Designed to support compliance, internal audits, and governance transparency.

---

# 7️⃣ Notification & Communication Pipeline

Multi-channel abstraction layer supporting:

* Email (Mailable classes)
* SMS (service abstraction layer)
* In-app notifications
* Queued verification workflows

Why Queue Notifications?

* Prevent request blocking
* Improve reliability
* Allow retry logic
* Support high-volume dispatch

---

# 8️⃣ Analytics & Reporting Engine

Built to provide operational insight across multiple entities.

### Supported Metrics

* Total donations
* Average donation
* Largest donation
* Top purpose
* Top contributor
* Breakdown by:

  * Month
  * Church
  * Payment method
  * Purpose

### Technical Design

* Currency-aware aggregation logic
* Date-range filtering
* Church-scoped reporting
* CSV export pipeline
* Chart dataset generation for reactive frontends

Supports real-time filtering using Livewire + Chart.js.

---

# 9️⃣ Security Design

* Multi-guard authentication separation
* Role-scoped policy enforcement
* Spam honeypot validation
* Rate-limited ingestion endpoints
* Controlled church reassignment logic
* Scoped purpose-level permissions

Sensitive operations require contextual role validation at model level.

---

# 🔟 Tradeoffs & Rejected Approaches

### Microservices

Rejected due to:

* Small engineering team
* Infrastructure cost
* Operational overhead

### Event Sourcing

Considered for auditability but rejected in favor of:

* Structured audit logging
* Lower system complexity
* Faster delivery timeline

### Real-Time Broadcasting

Deferred in favor of queued notifications to prioritize reliability over immediacy.

---

# 11️⃣ Scalability Strategy

Designed to support:

* Thousands of users
* Multi-region currency handling
* Expansion to external payment gateways
* Modular extension for additional financial modules

Queue workers horizontally scalable.
Database optimized with strict FK constraints and indexed aggregation fields.

---

# 12️⃣ Engineering Principles Applied

* Governance-first architecture
* Financial data immutability principles
* Defense-in-depth validation
* Explicit role boundary enforcement
* Service abstraction for integration flexibility
* Controlled complexity over premature distribution

---

# Outcome & Impact

* Digitized distributed financial workflows
* Enforced structured governance constraints
* Improved traceability of donation lifecycle
* Enabled offline-to-online financial synchronization
* Built extensible foundation for regional expansion

---

# What This Case Study Demonstrates

* Multi-tenant SaaS architecture design
* Financial systems thinking
* Governance-aware RBAC enforcement
* Distributed ingestion handling
* Compliance-focused logging
* Senior-level tradeoff evaluation



