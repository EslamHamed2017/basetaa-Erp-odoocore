# PRD v0.1 - Basetaa ERP

> Version: 0.1 (Discovery Complete)
> Date: 2026-08-02
> Owner: Eslam El Berkawy
> Status: Ready for Architecture & Spec Phase

---

## 1. Product Vision

Basetaa ERP is a Middle East-focused ERP SaaS that is simpler than Odoo, more structured than spreadsheets, and built for how MENA businesses actually work.

Differentiators:
1. Middle East-first (cheques, VAT, WHT, MENA workflows)
2. Industry-ready templates (trading, restaurant, contracting, maintenance)
3. Simpler UX than Odoo/Dynamics - no implementer required
4. Smart Document Recognition (AI-powered invoice/bank statement reading)
5. E-invoicing ready from day 1

---

## 2. Technical Foundation

| Decision | Choice |
|---|---|
| Backend | Odoo Community 18 |
| Frontend | Next.js + TypeScript + @carbon/react |
| Database | PostgreSQL (via Odoo) |
| Bridge | Next.js API routes to Odoo XML-RPC/JSON-RPC |
| Hosting | VPS (Docker Compose) |
| CI/CD | GitHub Actions |
| Architecture | SaaS multi-tenant - separate Odoo DB per tenant |
| API | Full REST API + Webhooks (versioned /v1/) |
| Mobile | Web responsive + PWA |
| i18n | Arabic + English, per-user language, RTL/LTR auto-switch |
| Currency | Multi-currency + revaluation from v1 |

---

## 3. Accounting Core (Phase 1)

### 3.1 Philosophy
- Hybrid: Odoo engine + Dynamics-inspired financial controls + MENA simplification
- Smart Posting Rules: system infers GL accounts automatically

### 3.2 Multi-Company
- From first version. Every document carries company/entity context.
- Each company: own VAT setup, base currency, bank accounts, permissions, reports, lock dates
- Consolidation: roadmap

### 3.3 Multi-Currency
- Transaction currency, company/base currency, exchange rates
- Currency gain/loss recording
- Currency revaluation at month-end/year-end (core from v1)

### 3.4 Chart of Accounts
- Country + Industry templates (UAE, KSA, Bahrain, Qatar + Trading, Services, Restaurant, Contracting, Distribution, Maintenance)
- Official support: UAE first

### 3.5 Financial Dimensions
- From first version: Branch, Department, Project, Cost Center, Salesperson, Region, Channel, Entity
- Customizable: add, hide, rename, require per document type
- Cost Center = dimension only (no allocation module in v1)
- Project = dimension only (no full project module in v1)

### 3.6 Posting & Journals
- Smart Posting Rules from v1
- Manual journals from v1 with full controls (permissions, approval, attachments, dimensions, audit trail, reverse instead of delete)
- Posting policy: Draft -> Validate -> Post (default) + Auto-post (by rules/permissions/amount thresholds)

### 3.7 Controls
- Period locking from v1
- Audit trail full from v1

### 3.8 Attachments
- Optional in v1

### 3.9 Approvals
- Core from v1, embedded in all major flows
- In-app only (approval inbox)

### 3.10 Cheques
- Core from v1: issued + received
- Due dates, status workflow, linked to supplier/customer/invoice/payment

### 3.11 Bank Reconciliation
- Manual from v1: enter/import statement, match, unmatched, bank charges
- Statement input: manual entry + CSV/Excel import
- Bank API later

### 3.12 Tax / VAT
- Use Odoo core tax engine
- Official support: UAE only in v1
- WHT: core from v1

### 3.13 E-Invoicing
- Ready from day 1 (FTA-compliant XML/JSON, QR code)

### 3.14 Fiscal Year
- Flexible: company defines start/end

### 3.15 Payment Terms
- Net 30/60/90, installments, percentage upfront

### 3.16 Expenses & Petty Cash
- Core from v1

### 3.17 Recurring Transactions
- Core from v1

### 3.18 Intercompany
- Core from v1

---

## 4. Inventory (Phase 2)

- Valuation from v1: FIFO + Average Cost
- Landed cost from v1
- Multiple warehouses + internal locations
- Barcode from v1
- Serial/Lot/Expiry: optional per product
- Reorder rules & stock alerts from v1
- Stock reservation from v1
- Negative stock: policy-controlled
- Product variants from v1

---

## 5. Purchase (Phase 3)

- Full flow: PR -> RFQ -> PO -> Receipt -> Vendor Bill -> Payment
- 3-Way Matching from v1
- Approvals on PR/PO/Bill/Payment
- Supplier price lists from v1
- Budget control: warnings only

---

## 6. Sales (Phase 4)

- Full flow: Quote -> SO -> Delivery -> Invoice -> Receipt
- Partial delivery/invoicing, backorders
- Returns/credit notes/refunds
- Customer price lists from v1
- Credit limit control from v1
- Discount approval from v1

---

## 7. CRM (Phase 5)

- Core from v1: leads, opportunities, pipeline, activities, follow-ups
- Basic reminders only (no advanced automation)

---

## 8. Reporting & Dashboards

- Financial Statements: TB, P&L, BS, GL, Cash Flow
- Detailed Reports: VAT, Journal Items, Bank Rec, Cheque aging, Customer/Supplier aging
- Interactive Dashboards: role-based, KPIs, drill-down, live alerts

---

## 9. Platform Features

- Opening Balances: wizard + Excel/CSV import
- Onboarding: Setup Wizard + Checklist
- Custom Fields: no-code from settings
- Document Templates: customizable (logo, colors, fields, layout)
- Data Import/Export + Migration Wizard (Odoo, QuickBooks)
- Customer Portal: simple v1 (SOA + invoices)
- Notifications: in-app only
- Security: password policy + optional 2FA
- Backup: admin-level, encrypted, automated

---

## 10. AI Features

- Smart Document Recognition from v1:
  - Vendor invoice PDF -> auto-extract data
  - Bank statement -> auto-match
  - Review before posting

---

## 11. Deferred to Later

- HR / Payroll
- Fixed Assets
- Budgeting module (full)
- POS
- Hijri calendar
- Project/Job Costing module (full)
- Cost Center allocations (full)
- Native mobile apps
- Supplier portal
- Email-to-document
- Bank API / Open Banking
- Consolidation / elimination
- Email/WhatsApp/Telegram notifications
- Standard Cost method

---

## 12. Business Model

- SaaS multi-tenant, subscription model
- Pricing: Tiered (Starter / Professional / Enterprise)
- Hosting: VPS with Docker
