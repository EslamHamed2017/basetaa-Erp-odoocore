# ADR-0002: Multi-Company & Multi-Tenant Architecture

**Date:** 2026-08-02
**Status:** Accepted

## Decision
- **Multi-company:** from first version, within each tenant.
- **Multi-tenant:** separate Odoo database per tenant (strongest isolation).

## Consequences
- Strong data isolation between customers.
- Central management layer needed for tenant provisioning.
- Consolidation: roadmap (architecture must not block it).
