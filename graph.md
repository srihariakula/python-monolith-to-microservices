# Migration execution graph (PROPOSED; no implementation authorized)

## Task configuration
Target: https://github.com/srihariakula/python-monolith-to-microservices
Goal: create a reference Python monolith, then plan an incremental strangler-pattern migration to independently deployable services.
Scope authorized: planning and baseline discovery only. No publishing, deployments, database changes, or production operations.
Retry limit: initial attempt + 3 corrections per node.
Assumptions: FastAPI, SQLite, users/catalog/orders/inventory/simulated payments; actual requirements TBD.

## Baseline
Repository public and empty, as observed on GitHub on 2026-10-09.
Application baseline: UNRESOLVED (no source, tests, or dependencies present).
No commands run against a checked-out repository.

## Completion contract
R01: Repository identity and current contents observed — public GitHub page — PASS.
R02: Existing application tests assessed — test logs — UNRESOLVED until source exists.
R03: Bounded graph and evaluator definitions — graph.md review — PROPOSED.
R04: Traceable check register — checks.md review — PROPOSED.
R05: Accurate progress state — progress.md review — PROPOSED.
R06: No unauthorized publication or code changes — local artifact review — PASS for this planning session.

## Node registry
| Node | Type | Primary output | Dependencies | Evaluator | Risk |
|---|---|---|---|---|---|
| C01 | CODE | baseline/baseline.json | none | inventory coverage + test exit codes | low |
| A01 | AGENT | discovery/architecture-inventory.md | C01 | endpoints/imports/workflows mapped | medium |
| A02 | AGENT | discovery/data-lineage.md | C01 | entity/write-path coverage | high |
| A03 | AGENT | discovery/operational-risks.md | C01 | security/operability checklist | high |
| G1 | CODE+review | gates/discovery.json | A01,A02,A03 | no unaccepted critical discovery gaps | high |
| A04 | AGENT | design/service-boundaries.md | G1 | bounded contexts and ownership approved | high |
| C02 | CODE | design/contract-validation.json | A04 | schemas valid; dependency graph acyclic | medium |
| G2 | CODE+review | gates/design.json | C02 | architecture and data contracts approved | high |
| A05 | AGENT | services/service-build-manifest.json | G2 | isolated builds and contract tests pass | high |
| A06 | AGENT | data/data-migration-plan.md | G2 | reconciliation/restore design reviewed | high |
| A07 | AGENT | platform/platform-manifest.json | G2 | security and operability requirements met | high |
| C03 | CODE | verification/integration-results.json | A05,A06,A07 | functional, contract, security and data checks | high |
| G3 | CODE+review | gates/integration.json | C03 | no failed mandatory tests | high |
| A08 | AGENT | release/rollout-plan.md | G3 | rollback and canary plan reviewed | high |
| C04 | CODE | release/release-evidence.json | A08 | artifact integrity and release policy | high |
| G4 | CODE+approval | gates/release.json | C04 | explicit operator approval + rollback rehearsal | critical |

## Dependency graph
C01 -> {A01,A02,A03} -> G1 -> A04 -> C02 -> G2 -> {A05,A06,A07} -> C03 -> G3 -> A08 -> C04 -> G4

## Parallel groups
Discovery: A01/A02/A03. Implementation (not authorized): A05/A06/A07. Per-service A05 subgraphs may run in parallel only with disjoint ownership.
No genuine subagents have been dispatched.

## Deterministic code nodes
C01 inventory and test execution; C02 schema/cycle validation; C03 test aggregation and reconciliation; C04 release manifest and integrity verification.

## Evaluator definitions
Verdicts: PASS, FAIL, UNRESOLVED. Missing evidence = UNRESOLVED.
G0 baseline must record existing failures rather than relabel them PASS.
G1 requires mapped entrypoints, dependencies, external integrations, critical workflows.
G2 requires validated contracts, explicit data ownership, no forbidden cycles.
G3 requires passing agreed functional/contract/security/reconciliation suites.
G4 requires approved canary/abort thresholds, observability and rehearsed rollback.
Never weaken thresholds to obtain PASS.

## Risk gates and recovery
Stop at each high-risk gate. Preserve artifacts and logs. Fix only the originating node, rerun the same evaluator, up to three corrections. On exhaustion mark BLOCKED and request approval.
For failed canary, stop traffic shift and return to known-good routing only if data remains backward compatible. Data recovery requires reconciled checkpoints and explicit authorization; never assume destructive rollback is safe.
Publishing, spending, external communications, destructive actions and production changes need separate approval.

## Decision log
2026-10-09: User approved planning and baseline discovery only.
2026-10-09: User selected public repository and supplied URL. Repository observed empty.
2026-10-09: Local planning artifacts prepared; no remote writes authorized or performed.

## Accepted learning rules
None yet: no execution evaluators have passed.

## Execution constraints
Do not modify application code or publish without further approval. Isolate concurrent writes; verify paths and version hashes before merge. Save artifacts before evaluating.
