# ADR-0001: Accounting Core Philosophy

**Date:** 2026-08-02
**Status:** Accepted
**Decision:** Hybrid — Odoo Community engine + Dynamics-inspired financial controls + MENA simplification.

## Context
ERP accounting systems follow two broad philosophies: Odoo's flexible/approachable model and Dynamics' structured/controlled model (posting groups, dimensions, role centers, strict posting setup).

## Decision
Adopt a **Hybrid** approach:
- Use Odoo Community 18 as the backend engine.
- Apply Dynamics-inspired financial control philosophy: dimensions, posting setup, role centers, period controls, structured journals.
- Simplify for MENA SMEs: no implementer required, setup wizard, industry templates.

## Consequences
- More structured than plain Odoo Community.
- Must build posting rules engine and dimensions layer ourselves.
- Better fit for serious MENA finance teams.
