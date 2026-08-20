# AGENTS.md — Basetaa ERP

> **Read this file before doing anything in this repo.**

## Project Overview

Basetaa ERP is a Middle East-focused ERP SaaS product built on Odoo Community 18, with a Next.js + IBM Carbon frontend. It competes with Odoo Enterprise and Dynamics by being simpler, Middle East-first, and industry-ready.

## Source of Truth

The **repo** is the source of truth — not chat memory. Always:
1. Read `PROJECT_STATE.md` for current status.
2. Read the active spec/PRD in `docs/`.
3. Check `tasks/tasks.yaml` for task assignments.

## Model Ownership

| Role | Model | Responsibility |
|---|---|---|
| Founder/Product Owner | Eslam | Final product/business decisions |
| Orchestrator | Hermes | Plans, dispatches, verifies, reports, keeps repo state |
| Spec/PRD/Analysis | Claude | Spec Kit `/specify`, `/clarify`, `/analyze`, PRD, user stories |
| Review/Gatekeeper | Codex | Spec review, implementation review, security/quality gate |
| Backend/Odoo | GLM 5.2 | Odoo modules, business logic, APIs, migrations, tests |
| Frontend | Gemini | Next.js/React/Carbon UI, RTL/LTR, dashboards, forms |

## Operating Rules

1. **No implementation without spec.** Every task needs goal, scope, acceptance criteria.
2. **No role substitution.** If a model hits quota, pause and resume same model.
3. **Small tasks, small commits.** One logical task per branch/commit.
4. **Codex is the quality gate.** Implementation is not done until reviewed.
5. **Evidence beats claims.** Report actual test/build results.
6. **Human approval gates.** Ask Eslam before first push, production release, destructive actions, or major architecture changes.

## Before You Act

```text
1. Read PROJECT_STATE.md
2. Read the active spec or PRD
3. Check your assigned tasks
4. Follow the spec-first workflow
5. Report results with evidence
```

## Skills to Load

- `mena-erp-ai-delivery-factory` — project orchestration
- `mena-erp-accounting-core-design` — all product decisions (85 Q&A)
- `mena-erp-carbon-ui-ux` — UI/UX rules
- `product-discovery-interview` — if resuming discovery

## Directory Structure

```text
basetaa-erp/
├── AGENTS.md              ← you are here
├── PROJECT_STATE.md       ← current project status
├── README.md
├── docs/
│   ├── prd/               ← Product Requirements Documents
│   │   └── PRD-v0.1.md
│   ├── architecture/      ← Technical architecture
│   │   └── architecture.md
│   └── decisions/         ← Architecture Decision Records (ADR)
├── specs/                 ← Feature specs (Spec Kit)
├── tasks/
│   ├── tasks.yaml         ← Task backlog + status
│   └── reports/           ← Progress reports
├── scripts/               ← Utility scripts
└── (code directories created during implementation)
```

---

## Custom Odoo Modules (for fresh deployments)

When deploying a fresh Odoo instance (erpprofit, odoo_test, etc.), include the **basetaa_erp_config** module to prevent the Publisher Warranty cron from resetting DB expiration to trial.

**Module:** `https://github.com/EslamHamed2017/basetaa_erp_config`

**What it does:**
- Disables the `Publisher: Update Notification` cron (mail.ir_cron_module_update_notification)
- Prevents `database.expiration_date` from being reset to 30-day trial
- Keeps DB expiration at 2099-12-31 (permanent)

**Deployment steps:**
1. Clone module to `custom-addons/basetaa_erp_config` (or mount as volume)
2. Add to Odoo `--addons-path`: `/mnt/custom-addons,/mnt/enterprise-addons,/usr/lib/python3/dist-packages/odoo/addons`
3. Module has `auto_install: True` — installs automatically on DB init
4. `post_init_hook` disables the cron immediately

**Verification after deploy:**
```sql
SELECT cron_name, active FROM ir_cron WHERE id = 4;  -- should be f (false)
SELECT * FROM ir_config_parameter WHERE key IN ('database.expiration_date', 'database.expiration_reason');
-- expiration_date = 2099-12-31 23:59:59
-- expiration_reason = none
```

---

## Odoo Panel Deployment (odoo-panel.basetaa.com)

**Panel location:** `/home/hermesops/odoo-panel/` (pm2 process: `odoo-panel`)
**Panel URL:** https://odoo-panel.basetaa.com/

### How it works
- Panel reads `lib/templates.js` for docker-compose generation
- `lib/deploy.js` handles local + remote deployments
- Custom addons are mounted from `/home/hermesops/odoo-panel/data/custom-addons`

### Important notes
1. **Custom addons mount:** `deployLocal` creates a symlink `./custom-addons` → `/home/hermesops/odoo-panel/data/custom-addons`
2. **Remote deployments:** `ensureRemoteEnterprise` uploads `custom-addons.tar.gz` to `/opt/odoo-shared/custom-addons`
3. **Module auto-install:** `basetaa_erp_config` has `auto_install: True` — installs automatically on DB init
4. **2099 fix:** `post_init_hook` disables Publisher cron; `setup.sh` sets `expiration_date = 2099-12-31`

### Updating custom-addons
```bash
cd /home/hermesops/odoo-panel/data/custom-addons
# Edit module files
git add . && git commit -m "update" && git push origin main
# For remote servers, re-run deploy or manually sync /opt/odoo-shared/custom-addons
```

### Restart panel after code changes
```bash
pm2 restart odoo-panel
```
