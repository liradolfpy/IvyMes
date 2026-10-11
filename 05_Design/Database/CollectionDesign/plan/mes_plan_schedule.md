**FILE TYPE: PROJECT SPECIFIC**

# mes_plan_schedule — Collection Specification

**Document ID:** MES-COL-PLAN-schedule  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** plan  
**Collection type:** Transaction  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Stores a production schedule and its planning horizon, ownership, and lifecycle for execution coordination.

## B. Business definition

Schedule is the planning artifact that organizes dated operation/order placements. Inventory does not list a schedule-assignment child collection; repeated placements must be separate flat records and their owner is unresolved.

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
| `scheduleCode` | String | Yes (proposed) | Business identifier for schedule. | Pattern/uniqueness scope OPEN. | No | Candidate scheduleCode; uniqueness OPEN | Identify planning schedule. | Inventory; SAP resource orchestration | Schedule selection |
| `scheduleName` | String | Yes | Readable schedule name. | Non-empty. | No | Candidate text search | Planner display. | DATA-003 | Schedule review |
| `plantId` | String | Conditional (proposed) | Plant scope for schedule. | If present pair with plantName. | Yes: plantId + plantName -> mes_plan_plant | Candidate plant+horizon | Site schedule context. | DEC-022; SAP scheduling | Planning |
| `plantName` | String | Conditional | Plant display snapshot. | Both fields present or absent. | Yes: paired with plantId | No index by default | Readable scope. | DATA-003 | Schedule view |
| `scheduleStart` | Date | Yes (proposed) | Start of planning horizon. | BSON Date; <= scheduleEnd. | No | Candidate plant+scheduleStart | Horizon filtering. | Inventory; SAP scheduling | Plan horizon |
| `scheduleEnd` | Date | Yes (proposed) | End of planning horizon. | BSON Date >= scheduleStart. | No | Candidate plant+scheduleEnd | Bound plan horizon. | Inventory | Schedule analysis |
| `status` | String | Yes (proposed) | Schedule lifecycle state. | Values/transitions OPEN. | No | Candidate status+plant | Control schedule usage/publication. | Lifecycle rules OPEN | Planner workflow |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `plantId` — Yes: plantId + plantName -> mes_plan_plant.
- `plantName` — Yes: paired with plantId.

## G. Validation rules

- Flat top-level scalar fields only; no nested documents or arrays.
- MES IDs are 16-digit strings; external identifiers remain separate.
- Enforce only approved code patterns and uniqueness scopes. Do not invent OPEN scopes or status vocabularies.
- Required ID/display pairs are complete and consistent; optional pairs are both absent or both populated.
- Audit actor pairs are complete; updatedAt >= createdAt.
- Retired reference records remain historically resolvable when established by decision.

## H. Status values

Schedule lifecycle and lock/publication semantics OPEN.

## I. Relationships

**Outgoing:** Plant pair; operation/order placements have no assigned owner collection in inventory.  
**Incoming:** Schedule Simulation and Schedule Publication; production execution/dispatch may consume released placements.  
**Dependencies:** Production Plan, Production Order, Work Order, productionCalendar, resourceCapacity, and routing/operation definitions. Schedule detail grain is unresolved.

## J. Recommended indexes

- Default unique _id index.
- Candidate scheduleCode; uniqueness OPEN — Schedule selection; verify query frequency and selectivity before implementation.
- Candidate text search — Schedule review; verify query frequency and selectivity before implementation.
- Candidate plant+horizon — Planning; verify query frequency and selectivity before implementation.
- Candidate plant+scheduleStart — Plan horizon; verify query frequency and selectivity before implementation.
- Candidate plant+scheduleEnd — Schedule analysis; verify query frequency and selectivity before implementation.
- Candidate status+plant — Planner workflow; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  scheduleCode: "<scheduleCode>",
  scheduleName: "<scheduleName>",
  plantId: "<plantId>",
  plantName: "<plantName>",
  scheduleStart: <value>,
  scheduleEnd: <value>,
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

BLOCKER: repeated operation placements/assignments require a separate record owner; identity of schedule details; work-center vs capacity target; schedule number rule; revisions; status/publication/version behavior; conflict/overbooking.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: BLOCKED — schedule placement owner missing.
- Status vocabulary: OPEN — values not approved.
- Unresolved dependencies: see Section N; no silent resolution.


## P. Audit and Historical Data

Standard audit fields remain flat and paired. Preserve production-order/work-order lineage, planning versions, schedule publication, and execution history according to approved retention rules; where not specified, retention remains OPEN.

## Q. Real-World Manufacturing Exceptions

Handle partial order splits, holds, cancellations, schedule conflicts, stale input versions, and integration failures without merging production and work-order concepts. Exact correction and reconciliation rules remain OPEN where not covered by decisions.

## R. Missing Field Assessment

Collection-specific requirements are incomplete for some planning and schedule semantics. Review with planning, production control, shop-floor operations, integration, and database roles. Known gaps: BLOCKER: repeated operation placements/assignments require a separate record owner; identity of schedule details; work-center vs capacity target; schedule number rule; revisions; status/publication/version behavior; conflict/overbooking.

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

See Sections E, F, and J for fields, references, and indexes. Detailed planning requirements are not fully linked; no complete scenario coverage percentage is claimed. Known gaps: BLOCKER: repeated operation placements/assignments require a separate record owner; identity of schedule details; work-center vs capacity target; schedule number rule; revisions; status/publication/version behavior; conflict/overbooking.

## V. One Representative MongoDB Document

The sole illustrative flat structure appears in Section L. No additional sample documents are created.

## W. Assumptions

- Current inventory and approved decisions are authoritative.
- Repeated items and schedule placements require separate flat records.
- ID/name duplication is intentional strategic denormalization.
- APPROVED follows the Project Owner's prior instruction; OPEN questions remain unapproved.

## X. Open Issues

BLOCKER: repeated operation placements/assignments require a separate record owner; identity of schedule details; work-center vs capacity target; schedule number rule; revisions; status/publication/version behavior; conflict/overbooking.

## Y. Research Sources

Collection-specific public references and the operational questions they support are recorded in the Research basis section below. They do not override project decisions.

## Z. Final Design Review

- **Status:** APPROVED per prior Project Owner instruction; not production readiness.
- **Covered:** inventory-bounded purpose, core fields, approved decision alignment, relationships, research, and gaps.
- **Remaining:** Section X; schedule-placement and quantity/business semantics must be resolved before implementation where marked blocking.
## Research basis

- [SAP Digital Manufacturing Resource Orchestration](https://help.sap.com/docs/sap_digital_manufacturing/066baf723b0f40d7bce61590ebcec2ef) — Production supervisors schedule and dispatch operations to work centers/resources based on priority and availability.
- [SAP Scheduling Options](https://help.sap.com/docs/SAP_DIGITAL_MANUFACTURING/066baf723b0f40d7bce61590ebcec2ef/9e6b8995325a4f49994662a3c9b655b2.html) — Scheduling supports forward/backward, insert/find-slot/infinite modes and writeback; used to identify version and placement requirements.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


