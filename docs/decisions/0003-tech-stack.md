# ADR-0003: Technology Stack

**Date:** 2026-08-02
**Status:** Accepted

## Decision
| Layer | Technology |
|---|---|
| Backend | Odoo Community 18 |
| Frontend | Next.js + TypeScript + @carbon/react |
| Database | PostgreSQL |
| Bridge | Next.js API routes to Odoo JSON-RPC |
| Hosting | VPS (Docker Compose) |
| CI/CD | GitHub Actions |
| Mobile | PWA (no native apps in v1) |

## Consequences
- Odoo provides battle-tested accounting engine.
- Next.js/Carbon provides modern, accessible, RTL-capable UI.
- Docker enables future cloud migration without rewrite.
