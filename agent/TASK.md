# Current task

## Ecommerce table plan - 2026-10-05

- Rewrote `agent/ecommerce-table.md` as a proposed customer journey from home page through checkout, delivery, returns, and reconciliation.
- Reviewed official Shopify, Medusa, Saleor, and commercetools documentation. Added linked references and optional growth modules.
- Completed the governance connection and document review. This task changes documentation only. Database migrations and application tests did not run.

## Package migration - 2026-10-05

- [x] Retrieve authenticated cloud governance before this migration.
- [x] Update active package imports, helpers and manifests to the shorter public names.
- [x] Install and verify the published registry packages.
- [x] Commit and push the reviewed migration.


## Completion wave - 2026-10-04

Source 0.1.2 was committed and pushed. Release verification and GitHub CI passed. Preview sessions are not authentication. Platform migration depends on the accepted coordinated release. Business features require owner requirements.

- [x] Reconcile current status with the GitHub source release and latest owner audit.
- [x] Retrieve fresh authenticated cloud governance before this wave.
- [x] Record current source and foundation dependencies.
- [x] Migrate to the verified local Cxsun foundation; see FOUNDATION-PARITY.md in Cxsun. Browser and production acceptance remain open.

Use projects/cxsun/agent/REMAINING-WORK.md for ordered cross-owner dependencies.
Production deployment and real SMTP acceptance remain deferred. No pending external gate is marked complete.

## Prior records

Complete standalone development for ecommerce.

## Completed

Installed @devxcrew/tools@0.1.7 from npm. Maintenance scripts and CI require no sibling checkout.
Workspace and isolated verification, browser flows, port handling, and restart checks passed.
Environment setup and optional source development commands are documented in README.md.

## Verification limits

One clean install validated the identical dependency graph used by all five apps.
Each isolated app owned that installation during its verification. Five separate clean installs are not claimed.
Read AUDIT.md for evidence and prerequisites. Revised CI has not run on GitHub yet.

App release versions are unchanged. These implementation changes are local and uncommitted.
Real identity and business capabilities remain outside this task.
## Workspace GitHub release - 2026-10-04

Release title: Align Ecommerce foundation delivery.
Align standalone maintenance, CI and public shared package boundaries. Preview sessions remain unauthenticated.
Update version records, review release checks, then commit and push the current owner branch.
Preserve existing task history and incomplete acceptance gates.

## Dependency alignment - 2026-10-05

- [x] Align consumed shared packages and common direct dependency versions.
- [x] Install dependencies with lifecycle scripts disabled.
- [x] Keep app dependency ownership and public peer ranges.
- [x] Exclude Veyrezio from this change.

Source version: 0.1.4. Published package archives retain their existing versions.
The baseline is recorded in projects/cxsun/agent/DEPENDENCY-BASELINE.json.


## Shared alignment audit - 2026-10-05

Full verification and package boundaries passed with Framework 0.1.11. Preview sessions remain; identity migration is separate work.

Authenticated live MCP verification passed. See the [alignment audit](D:/codexsun/projects/cxsun/agent/SHARED-ALIGNMENT.md). Version numbers remain unchanged. No release delivery was performed by this audit.

## Cxsun foundation parity - 2026-10-05

Current source and exact dependencies match Cxsun after app-name and port substitutions.
This app keeps its own ID, release version, database, Git repository and history.
Verification passed: 46 tests, lint, types, build, compiled identity, package boundaries and authenticated live MCP.
SQLite migration, configured seed and connection checks passed. Preview source was removed. Blank bootstrap fields create no accounts.

See [foundation parity](D:/codexsun/projects/cxsun/agent/FOUNDATION-PARITY.md) for evidence and remaining acceptance work. Live inventory needs refresh after this change. No commit, push, publication or deployment was performed.
