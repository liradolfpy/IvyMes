# MES Project Context

**Document ID:** MES-CTRL-CONTEXT-001
**Version:** 1.1
**Status:** CURRENT
**Last Updated:** 2026-10-11

## Purpose and ownership

This is the authoritative home for current MES facts, selected technologies, and project-specific configuration. Generic working behavior belongs in `Project_Instructions.md`; generic modeling procedure belongs in `01_Requirements/DataModelingConvention.md`. Context selects settings without redefining those procedures.

## Project identity and objective

- Name: MES — Manufacturing Execution System.
- Owner: MES Project Team.
- Root: the current workspace directory; no machine-specific absolute path is prescribed.
- Objective: define and document the MES architecture, functional structure, information and database models, schematics, workflows, interfaces, business rules, traceability/genealogy, and representative documents and datasets.
- Phase: architecture and documentation design; not implementation or production validation.
- Terminology: MES means Manufacturing Execution System. Use domain terms from current functional structure and requirements consistently.

## Scope and deliverables

`03_Functional_Structure/ProjectScopeBoundary.md` is authoritative for the detailed scope and exclusions. This project produces requirements and functional/technical specifications, system and solution architecture, schemas and dictionaries, process/data-flow/relationship diagrams, interface contracts, configuration definitions, document templates, samples, and decision/assumption/issue records.

Implementation, production UI, hardware engineering, testing workstreams, deployment, commissioning, operational rollout, training delivery, and operational support remain outside scope. UI representations are illustrative documentation only. Real production data is not processed unless explicitly supplied and authorized as reference/sample data. Finance/accounting and unrelated enterprise functions are outside scope unless explicitly defined as an integration boundary. These boundaries apply to MES, not automatically to a new project.

## Selected technologies and functional structure

- Database: MongoDB, established by DEC-001.
- Frontend, backend, cloud, operating system, PLC, hardware, and deployment technology: not selected; remain neutral until decided and recorded.
- Functional modules and codes: `03_Functional_Structure/FunctionalStructureModules.md`.
- Functional sections: `03_Functional_Structure/FunctionalStructureSections.md`.
- Folder system: `Folder_Guide.md`; use its optional technical folders when applicable.

## Identity and collection naming

- Application/collection prefix: `mes`; collection format: `mes_<moduleCode>_<singularEntityName>`.
- Every primary `_id` and scalar entity reference uses a custom 16-digit string. ObjectId and 24-character hexadecimal IDs are prohibited. IDs are immutable and globally unique across the MES database, including historical records. The exact generator remains OPEN.
- References use MES IDs, separate from external identifiers and human-facing business numbers. Include the corresponding display-value pair, including audit references.
- Module codes and membership are defined only in `03_Functional_Structure/FunctionalStructureModules.md`; use that mapping for collection names and module folders.

## Database modeling settings

**DATA-001:** Every MES MongoDB document is flat, with top-level scalar fields only. No embedded documents or arrays, without exception. Repeated items and relationships use separate flat documents. Document query patterns, indexes, ordering, and cross-record consistency. Separate queries and aggregation, including `$lookup`, are allowed where appropriate; routine use is not required.

**DATA-002:** Every numeric field names a concrete BSON type; the ambiguous label `Decimal` is prohibited. Do not use decimal constructors in project samples or store numeric quantities as strings. DEC-010 specifies BSON Double, represented with `Double(value)`, for `mes_prod_downtimeEvent.durationSeconds`, `mes_prod_productionOrder.plannedQuantity`, `mes_prod_productionReport.reportedQuantity`, `mes_prod_reworkRecord.reworkQuantity`, `mes_prod_routingOperation.standardCycleTime`, `mes_prod_scrapRecord.scrapQuantity`, and `mes_prod_workOrder.plannedQuantity`. Units, precision, rounding, and range require documented field rules and remain OPEN where undefined. Scaled integers require a documented scale, units, range, and conversion rule.

**DATA-003:** Scalar entity-reference stems are singular, with ID/display pairs. Repeated references use separate records under DATA-001.

**DATA-004:** Specifications remain at `05_Design/Database/CollectionDesign/<moduleCode>/<collectionName>.md` using the functional module mapping, exact existing collection names, and no current specifications in the root.

**DATA-005:** Samples remain at `05_Design/Database/Collections/<moduleCode>/<collectionName>.js` using exact existing names; no current sample files in the root. Samples are illustrative and do not establish production readiness.

## Sample generation settings

- Generate exactly 10 realistic, varied documents per requested collection sample file.
- Use dates between April 2026 and September 2026 unless an explicit user instruction changes that range.
- Output one directly executable MongoDB `insertMany()` statement for Studio 3T IntelliShell. MongoDB scalar constructors are permitted; strict RFC JSON is not the output format.
- Use `ISODate()` for BSON dates, `NumberInt()` for appropriate 32-bit integer fields, and `Double(value)` for fields specified as Double. Match the exact BSON type, requiredness, status, and reference rules in the current specification.
- The outer `insertMany()` batch array is an output container, not a document field. Arrays inside documents remain prohibited.
- Include schema summary, denormalization explanation, and document consistency checklist. Do not add unrelated fields or new collections merely to generate examples.

## Operational approval boundaries

Manufacturing release, quality hold/release, waiver, and operational authorization concepts and established status vocabularies remain unchanged. `approvalStatus`, `approvedAt`, `PendingApproval`, and `Approved` describe MES business behavior; removing documentation sign-off does not remove these controls. DEC-011 and DEC-012 remain the recorded vocabulary baselines. Unresolved transitions and other business rules remain OPEN.

## Production Execution business numbering — DEC-013

Under current `DEC-013`, Production Execution business numbers use the patterns and collection-level uniqueness scopes below. `YYYY` is the four-digit calendar year of assignment; `######` is a six-digit, zero-padded sequence. A pattern prefix is reserved for the named collection. Uniqueness applies across the entire collection, including records in every status, unless the table identifies a compound scope.

| Collection | Field | Required? | Current pattern / code rule | Uniqueness scope |
|---|---|---:|---|---|
| `mes_prod_andonCase` | `andonCaseNo` | Yes | `AC-YYYY-######` | Globally unique within collection |
| `mes_prod_andonEvent` | `andonEventNo` | Yes | `AE-YYYY-######` | Globally unique within collection |
| `mes_prod_dispatchAssignment` | `dispatchAssignmentNo` | Yes | `DA-YYYY-######` | Globally unique within collection |
| `mes_prod_downtimeEvent` | `downtimeEventNo` | No | `DT-YYYY-######` when assigned | Unique within collection when present |
| `mes_prod_operation` | `operationCode` | Yes | Controlled code `OP-######` | Globally unique within collection |
| `mes_prod_operationExecution` | `operationExecutionNo` | No | `OX-YYYY-######` when assigned | Unique within collection when present |
| `mes_prod_productionOrder` | `productionOrderNo` | Yes | `PO-YYYY-######` | Globally unique within collection |
| `mes_prod_productionReport` | `productionReportNo` | Yes | `PR-YYYY-######` | Globally unique within collection |
| `mes_prod_reworkRecord` | `reworkNo` | Yes | `RW-YYYY-######` | Globally unique within collection |
| `mes_prod_routing` | `routingNo` | Yes | Stable routing-lineage number `RT-######`; revision is a separate field | `routingNo` identifies one lineage globally; (`routingNo`, `revision`) is globally unique |
| `mes_prod_scrapReason` | `scrapReasonCode` | Yes | Controlled code `SCR-######` | Globally unique within collection |
| `mes_prod_scrapRecord` | `scrapNo` | Yes | `SC-YYYY-######` | Globally unique within collection |
| `mes_prod_shiftHandover` | `handoverNo` | Yes | `HO-YYYY-######` | Globally unique within collection |
| `mes_prod_workInstruction` | `workInstructionNo` | Yes | Stable instruction-lineage number `WI-######`; revision is a separate field | `workInstructionNo` identifies one lineage globally; (`workInstructionNo`, `revision`) is globally unique |
| `mes_prod_workOrder` | `workOrderNo` | Yes | `WO-YYYY-######` | Globally unique within collection |

The `operationCode` and `scrapReasonCode` values are controlled codes, not year/sequence business numbers. Optional numbers may be omitted; when present, they must follow the listed pattern and be unique in their stated scope. The sequence-allocation mechanism remains subject to the concurrency requirements in Section 8.3 of `01_Requirements/DocumentIdAndReferenceConvention.md` and is not selected by `DEC-013`.


## Existing artifact examples

`mes_prod_workOrder` maps to `05_Design/Database/CollectionDesign/prod/mes_prod_workOrder.md` and `05_Design/Database/Collections/prod/mes_prod_workOrder.js` when those artifacts exist. This path example does not establish the presence of a file.

## Open settings and supporting decisions

The ID generator, sequence storage/allocation, non-Production-Execution numbering scopes, name snapshot/synchronization rules, deletion/retention rules, unresolved lifecycle transitions, and field precision/range requirements remain OPEN where not established by a current decision or field specification. Vocabulary decisions do not define transition graphs. Do not fill gaps by treating an assumption as a decision.

Consult `Decisions_Log.md` for decision provenance and `Change_Log.md` for material changes. The active documentation workflow is GEN-001 in the master instructions; it is not repeated here. `ProjectSpecificRules.md` is a reference index to this context and dedicated domain documents, not another configuration source.

## Revision history

| Version | Date | Change | Status |
|---|---|---|---|
| 1.1 | 2026-10-11 | Consolidate MES settings here; remove repeated generic procedures and module mapping. | CURRENT |
