**FILE TYPE: PROJECT SPECIFIC — REFERENCE-ONLY PROMPT**

# Stage 5 — Module-Level Review

Read `121_Collection_Design_Common_Rules.md` first. Review the completed specifications in `[MODULE_CODE]` against the current project artifacts. This is an audit/review; do not silently redesign or correct anything.

## Read

- Current project instructions, context, decisions, functional module/section definitions, relevant requirements, naming/modeling/ID conventions.
- Current Collection Inventory and Dependency Map.
- Every available collection specification under `05_Design/Database/CollectionDesign/[MODULE_CODE]/`.
- Referenced collection specifications in other modules where needed to verify cross-module ownership/references.
- Stage 1–4 outputs relevant to this module.

## Check

- Every inventory collection in the module has one correctly named spec at the prescribed path; no duplicate/missing concepts.
- Functional-section coverage and responsibility boundaries are clear.
- Field names and types, singular reference stems, mandatory ID/display pairs, audit fields, status meanings, business identifiers, and uniqueness rules follow current project authority.
- Flat-document compliance; separate-record representation of repeated details; parent identity, ordering, query/index, and cross-record consistency are defined.
- References target valid current owners; external vs MES-owned identity is clear; no broken/circular required dependency.
- Required operational exceptions, indexes, history, integration, retention, and query patterns are addressed where justified.
- The spec does not conflict with approved decisions or another specification. Prompt and inventory status are not mistaken for schema approval.

## Output

Produce a discrepancy report with unique IDs and severity (CRITICAL/HIGH/MEDIUM/LOW), artifact/section, evidence, impact, and proposed correction. Add a module coverage matrix and unresolved-dependency list. Do not change files unless the current user explicitly authorized corrections; if authorized, make traceable changes and update Change_Log, then report exactly what changed. Do not invent a business decision or mark all specs APPROVED by inference.

## Completion handoff

State whether the module is ready for Stage 6, ready with OPEN non-blocking issues, or blocked by specified decisions/artifacts.
