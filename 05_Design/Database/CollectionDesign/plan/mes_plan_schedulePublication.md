**FILE TYPE: PROJECT SPECIFIC**

# mes_plan_schedulePublication — Collection Specification

**Document ID:** MES-COL-PLAN-schedulePublication  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** plan  
**Collection type:** Transaction  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Records publication of a schedule version for downstream planning/execution consumption.

## B. Business definition

Schedule Publication is a controlled publication event referencing one schedule and a version/revision. It records publication lifecycle and timing; downstream snapshot and rollback semantics are unresolved.

## C. Complete field dictionary

| Field | BSON type | Requiredness | Business meaning | Validation | Reference | Index guidance | Reason | Evidence | Scenario |
|---|---|---|---|---|---|---|---|---|---|
| `_id` | String | Yes | Immutable globally unique 16-digit MES ID. | Exactly 16 digits; globally unique. | No | Default unique _id index | Primary identity | Project_Context DATA-003 | All references |
| `createdAt` | Date | Yes | UTC creation time | BSON Date | No | No index by default | Audit | KeyNamingConvention | Creation |
| `createdById` | String | Yes | 16-digit creator user/process ID | Pair with createdBy | User/process source OPEN | No index by default | Attribution | DATA-003 | Creation |
| `createdBy` | String | Yes | Creator display value paired with createdById | Non-empty, matching ID | User/process display pair | No index by default | Readable attribution | KeyNamingConvention | Creation |
| `updatedAt` | Date | Yes | UTC last update time | BSON Date >= createdAt | No | No index by default | Audit | KeyNamingConvention | Updates |
| `updatedById` | String | Yes | 16-digit updater user/process ID | Pair with updatedBy | User/process source OPEN | No index by default | Attribution | DATA-003 | Updates |
| `updatedBy` | String | Yes | Updater display value paired with updatedById | Non-empty, matching ID | User/process display pair | No index by default | Readable attribution | KeyNamingConvention | Updates |
| `publicationCode` | String | Yes (proposed) | Business identifier for publication event. | Pattern/uniqueness scope OPEN. | No | Candidate code+publishedAt | Identify release event. | Inventory; SAP scheduling writeback | Schedule release |
| `scheduleId` | String | Yes | Schedule version being published. | 16-digit ID resolving mes_plan_schedule. | Yes: scheduleId + scheduleName -> mes_plan_schedule | Candidate scheduleId+publishedAt | Link publication to schedule. | DATA-003; inventory | Downstream use |
| `scheduleName` | String | Yes | Schedule display snapshot. | Paired with scheduleId. | Yes: paired with scheduleId | No index by default | Readable publication record. | DATA-003 | Administration |
| `scheduleRevision` | String | Conditional (proposed) | Published schedule version identifier. | Version ownership/uniqueness OPEN. | Scalar version pending model decision. | Candidate scheduleId+revision | Identify published snapshot. | Inventory; versioning unresolved | Reproducibility |
| `publishedAt` | Date | Yes (proposed) | Publication timestamp. | BSON Date. | No | Candidate publishedAt | Audit downstream release time. | Inventory | Execution handoff |
| `status` | String | Yes (proposed) | Publication lifecycle state. | Values, rollback, supersede semantics OPEN. | No | Candidate status+publishedAt | Track publication validity. | Lifecycle rules OPEN | Schedule consumption |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `scheduleId` — Yes: scheduleId + scheduleName -> mes_plan_schedule.
- `scheduleName` — Yes: paired with scheduleId.

## G. Validation rules

- Flat top-level scalar fields only; no nested documents or arrays.
- MES IDs are 16-digit strings; external identifiers remain separate.
- Enforce only approved code patterns and uniqueness scopes. Do not invent OPEN scopes or status vocabularies.
- Required ID/display pairs are complete and consistent; optional pairs are both absent or both populated.
- Audit actor pairs are complete; updatedAt >= createdAt.
- Retired reference records remain historically resolvable when established by decision.

## H. Status values

Publication event status, distinct from schedule status; values OPEN.

## I. Relationships

**Outgoing:** Schedule ID/name and scalar revision.  
**Incoming:** Execution/dispatch consumers may use published schedules; exact publication pointer is unresolved.  
**Dependencies:** Schedule and possibly simulation; publication approval/rollback/version rules not defined.

## J. Recommended indexes

- Default unique _id index.
- Candidate code+publishedAt — Schedule release; verify query frequency and selectivity before implementation.
- Candidate scheduleId+publishedAt — Downstream use; verify query frequency and selectivity before implementation.
- Candidate scheduleId+revision — Reproducibility; verify query frequency and selectivity before implementation.
- Candidate publishedAt — Execution handoff; verify query frequency and selectivity before implementation.
- Candidate status+publishedAt — Schedule consumption; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  publicationCode: "<publicationCode>",
  scheduleId: "<scheduleId>",
  scheduleName: "<scheduleName>",
  scheduleRevision: "<scheduleRevision>",
  publishedAt: <value>,
  status: "<status>",
  createdAt: ISODate("2026-06-01T08:00:00.000+0000"),
  createdById: "1000000000000002",
  createdBy: "MES Configuration Service",
  updatedAt: ISODate("2026-06-01T08:00:00.000+0000"),
  updatedById: "1000000000000002",
  updatedBy: "MES Configuration Service"
}


## M. Assumptions

- The current inventory purpose and module owner bound this design.
- ID/display duplication is intentional strategic denormalization; copied-name synchronization policy remains OPEN unless decided.
- APPROVED is the requested document status; it does not close OPEN business rules.

## N. Open issues

Publication grain (event vs current published state); version snapshot; authorization actor vs audit; supersession/rollback semantics; downstream acknowledgment; business-number convention; lifecycle vocabulary.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: PASS — Schedule pair defined; downstream pointer remains OPEN.
- Status vocabulary: OPEN — values not approved.
- Unresolved dependencies: see Section N; no silent resolution.


## P. Audit and Historical Data

Standard audit fields remain flat and paired. Preserve production-order/work-order lineage, planning versions, schedule publication, and execution history according to approved retention rules; where not specified, retention remains OPEN.

## Q. Real-World Manufacturing Exceptions

Handle partial order splits, holds, cancellations, schedule conflicts, stale input versions, and integration failures without merging production and work-order concepts. Exact correction and reconciliation rules remain OPEN where not covered by decisions.

## R. Missing Field Assessment

Collection-specific requirements are incomplete for some planning and schedule semantics. Review with planning, production control, shop-floor operations, integration, and database roles. Known gaps: Publication grain (event vs current published state); version snapshot; authorization actor vs audit; supersession/rollback semantics; downstream acknowledgment; business-number convention; lifecycle vocabulary.

## S. Missing Collection Assessment

No collection is added. Repeated schedule placements/operation assignments are not represented as arrays; the inventory currently lacks an identified flat placement owner. Raise that gap for inventory review if schedule details are required.

## T. Cross-Collection Consistency Check

- Inventory scope/module/type checked against v1.6.
- Production Order and Work Order remain distinct per DEC-004.
- Approved PROD number patterns and lifecycle values follow DEC-013/DEC-011 where applicable.
- DEC-010 numeric types are used for the specified PROD plannedQuantity fields only.
- Entity references use singular ID/name pairs; unresolved plan/schedule relationships remain OPEN.
- Flat-document rule respected.

## U. Field Completeness Metrics

See Sections E, F, and J for fields, references, and indexes. Detailed planning requirements are not fully linked; no complete scenario coverage percentage is claimed. Known gaps: Publication grain (event vs current published state); version snapshot; authorization actor vs audit; supersession/rollback semantics; downstream acknowledgment; business-number convention; lifecycle vocabulary.

## V. One Representative MongoDB Document

The sole illustrative flat structure appears in Section L. No additional sample documents are created.

## W. Assumptions

- Current inventory and approved decisions are authoritative.
- Repeated items and schedule placements require separate flat records.
- ID/name duplication is intentional strategic denormalization.
- APPROVED follows the Project Owner's prior instruction; OPEN questions remain unapproved.

## X. Open Issues

Publication grain (event vs current published state); version snapshot; authorization actor vs audit; supersession/rollback semantics; downstream acknowledgment; business-number convention; lifecycle vocabulary.

## Y. Research Sources

Collection-specific public references and the operational questions they support are recorded in the Research basis section below. They do not override project decisions.

## Z. Final Design Review

- **Status:** APPROVED per prior Project Owner instruction; not production readiness.
- **Covered:** inventory-bounded purpose, core fields, approved decision alignment, relationships, research, and gaps.
- **Remaining:** Section X; schedule-placement and quantity/business semantics must be resolved before implementation where marked blocking.
## Research basis

- [SAP Transferring Production Order with Scheduling Information](https://help.sap.com/docs/sap-digital-manufacturing/integration-guide/configuring-production-order-with-scheduling-information) — Scheduling information is transferred and can be used for shop-floor planning; publications need downstream version traceability.
- [SAP Scheduling Options](https://help.sap.com/docs/SAP_DIGITAL_MANUFACTURING/066baf723b0f40d7bce61590ebcec2ef/9e6b8995325a4f49994662a3c9b655b2.html) — Scheduling may write changes back to integrated planning systems; informs publication/acknowledgement questions.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


