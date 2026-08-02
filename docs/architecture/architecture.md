# Architecture Document - Basetaa ERP

> Date: 2026-08-02
> Status: Draft v0.1

---

## 1. High-Level Architecture

```
[User Browser / PWA]
       |
       v
[Next.js Frontend + API Routes]
  - @carbon/react UI
  - RTL/LTR
  - PWA
  - API proxy layer
       |
       v
[Odoo Community 18 (JSON-RPC/XML-RPC)]
  - Custom accounting modules
  - Inventory modules
  - Purchase modules
  - Sales modules
  - CRM modules
       |
       v
[PostgreSQL]
  - Separate DB per tenant
```

## 2. Multi-Tenant Strategy

- Each tenant (customer) gets a separate Odoo database
- Central management service handles:
  - Tenant provisioning (create Odoo DB)
  - User management
  - Subscription/billing
  - Routing (subdomain -> DB mapping)
- Tenant isolation is at the database level (strongest isolation)

## 3. Frontend Architecture

```
basetaa-erp/
  frontend/              # Next.js app
    app/                 # App router
      [locale]/          # i18n routing (ar, en)
        (auth)/          # Auth group
        (dashboard)/     # Main ERP screens
        (portal)/        # Customer portal
    components/
      carbon/            # Carbon wrappers
      erp/               # ERP-specific components
    lib/
      odoo/              # Odoo JSON-RPC client
      i18n/              # i18n config
    public/
```

## 4. Backend Architecture (Odoo Custom Modules)

```
basetaa-erp/
  odoo-addons/
    basetaa_core/              # Base: dimensions, number series, multi-company extensions
    basetaa_accounting/        # COA templates, posting rules, journals, revaluation
    basetaa_cheques/           # Cheque management (issued/received)
    basetaa_vat/               # UAE VAT, WHT, e-invoicing
    basetaa_purchase/          # Purchase extensions
    basetaa_sales/             # Sales extensions
    basetaa_inventory/         # Inventory extensions (variants, landed cost, barcode)
    basetaa_crm/               # CRM extensions
    basetaa_approval/          # Approval workflow engine
    basetaa_expenses/          # Expenses & petty cash
    basetaa_recurring/         # Recurring transactions
    basetaa_intercompany/      # Intercompany transactions
    basetaa_reports/           # Financial reports & dashboards
    basetaa_document_ai/       # Smart Document Recognition
    basetaa_onboarding/        # Setup wizard & checklist
```

## 5. Deployment Architecture

```
VPS (srv1602368)
  Docker Compose:
    - nginx (reverse proxy, SSL, subdomain routing)
    - nextjs-app (frontend + API)
    - odoo-18 (backend, multi-DB)
    - postgresql (shared instance, separate DBs)
    - redis (caching, sessions)
    - watchtower (auto-update images) - optional

  GitHub Actions:
    - test (lint, typecheck, unit tests)
    - build (Docker images)
    - deploy (SSH -> docker compose pull -> up)
```

## 6. Data Flow Example: Creating a Sales Invoice

```
1. User fills invoice form in Next.js/Carbon UI
2. Next.js API route validates input
3. Next.js calls Odoo JSON-RPC: account.move.create()
4. Odoo applies posting rules -> creates journal entries
5. Odoo checks approvals, dimensions, period lock
6. Response returned to frontend
7. UI shows invoice with status (Draft/Posted)
```

## 7. AI Document Recognition Flow

```
1. User uploads vendor invoice PDF
2. Next.js sends to AI service (OCR + extraction)
3. AI extracts: vendor, date, amount, VAT, line items
4. Pre-filled vendor bill form returned to user
5. User reviews and confirms
6. On confirm -> create vendor bill in Odoo
```

## 8. Security Architecture

- All traffic over HTTPS (Let's Encrypt via nginx)
- API authentication: session-based (Odoo) + API keys (external)
- 2FA: optional per user
- Tenant isolation: database-level
- No secrets in code (environment variables only)
- Admin-level backups, encrypted at rest

## 9. Scalability Path

- V1: single VPS, Docker Compose
- When needed: migrate to managed cloud (DB-as-a-service, container orchestration)
- Architecture is designed to allow migration without rewrite:
  - Stateless frontend
  - Standard Odoo (portable)
  - Standard PostgreSQL (portable)
