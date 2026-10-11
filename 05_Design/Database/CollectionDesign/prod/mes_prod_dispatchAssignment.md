**FILE TYPE: PROJECT SPECIFIC**

# mes_prod_dispatchAssignment — Collection Specification

**Document ID:** MES-COL-PROD-dispatchAssignment  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** prod  
**Collection type:** Transaction  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Records the dispatch of a Work Order operation step to its execution context and planned time.

## B. Business definition

Dispatch Assignment is a shop-floor scheduling/assignment record for a specific Work Order and routing operation. It does not replace the parent Production Order or Work Order.

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
| `dispatchAssignmentNo` | String | Yes | Approved business number. | DA-YYYY-######; globally unique within collection across statuses (DEC-013). | No | Unique dispatchAssignmentNo | Dispatch transaction identity. | DEC-013 | Dispatch tracking |
| `workOrderId` | String | Yes | Work Order being dispatched. | 16-digit ID paired with workOrderName. | Yes: workOrderId + workOrderName -> mes_prod_workOrder | Candidate workOrderId+status+plannedStart | Execution assignment parent. | DEC-004; DATA-003 | Worklist |
| `workOrderName` | String | Yes | Work Order display/business-number snapshot. | Paired with workOrderId. | Yes: paired with workOrderId | No index by default | Readable assignment. | DATA-003 | Dispatch UI |
| `routingOperationId` | String | Yes (proposed) | Routing-specific operation step being assigned. | 16-digit ID paired with routingOperationName. | Yes: routingOperationId + routingOperationName -> mes_prod_routingOperation | Candidate workOrder+step | Assign exact step, not reusable operation only. | DEC-003; inventory | Operation dispatch |
| `routingOperationName` | String | Yes (proposed) | Step display snapshot. | Paired with routingOperationId. | Yes: paired with routingOperationId | No index by default | Readable worklist. | DATA-003 | Dispatch display |
| `workCenterId` | String | Conditional (proposed) | Planning work-center context if dispatch is work-center assigned. | If present paired with workCenterName; not a machine/resource association. | Yes: workCenterId + workCenterName -> mes_plan_workCenter | Candidate workCenter+status+plannedStart | Shop-floor organization. | DEC-022; inventory | Dispatch board |
| `workCenterName` | String | Conditional | Work-center display snapshot. | Both fields present or absent. | Yes: paired with workCenterId | No index by default | Readable context. | DATA-003 | Dispatch |
| `plannedStart` | Date | Yes (proposed) | Planned dispatch/execution start. | BSON Date; <= plannedEnd. | No | Candidate workCenter+status+plannedStart | Schedule operation. | Inventory; SAP dispatch | Work queue |
| `plannedEnd` | Date | Conditional | Planned end. | BSON Date >= plannedStart. | No | Candidate planned window | Capacity/conflict check. | Inventory | Dispatch |
| `status` | String | Yes | Dispatch assignment lifecycle. | Planned, Dispatched, InProgress, Completed, Cancelled (DEC-011). | No | Candidate status+plannedStart | Dispatch state. | DEC-011 | Worklist |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `workOrderId` — Yes: workOrderId + workOrderName -> mes_prod_workOrder.
- `workOrderName` — Yes: paired with workOrderId.
- `routingOperationId` — Yes: routingOperationId + routingOperationName -> mes_prod_routingOperation.
- `routingOperationName` — Yes: paired with routingOperationId.
- `workCenterId` — Yes: workCenterId + workCenterName -> mes_plan_workCenter.
- `workCenterName` — Yes: paired with workCenterId.

## G. Validation rules

- Flat top-level scalar fields only; no nested documents or arrays.
- MES IDs are 16-digit strings; external identifiers remain separate.
- Enforce only approved code patterns and uniqueness scopes. Do not invent OPEN scopes or status vocabularies.
- Required ID/display pairs are complete and consistent; optional pairs are both absent or both populated.
- Audit actor pairs are complete; updatedAt >= createdAt.
- Retired reference records remain historically resolvable when established by decision.

## H. Status values

Approved collection-specific values per DEC-011. State transitions and reassignment behavior OPEN.

## I. Relationships

**Outgoing:** Work Order, Routing Operation, optional Work Center pairs.  
**Incoming:** Operation Execution and work-center dispatch monitoring.  
**Dependencies:** Work Order, routing step; resource/capacity target not defined. Do not equate Work Center and machine/resource.

## J. Recommended indexes

- Default unique _id index.
- Unique dispatchAssignmentNo — Dispatch tracking; verify query frequency and selectivity before implementation.
- Candidate workOrderId+status+plannedStart — Worklist; verify query frequency and selectivity before implementation.
- Candidate workOrder+step — Operation dispatch; verify query frequency and selectivity before implementation.
- Candidate workCenter+status+plannedStart — Dispatch board; verify query frequency and selectivity before implementation.
- Candidate workCenter+status+plannedStart — Work queue; verify query frequency and selectivity before implementation.
- Candidate planned window — Dispatch; verify query frequency and selectivity before implementation.
- Candidate status+plannedStart — Worklist; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  dispatchAssignmentNo: "<dispatchAssignmentNo>",
  workOrderId: "<workOrderId>",
  workOrderName: "<workOrderName>",
  routingOperationId: "<routingOperationId>",
  routingOperationName: "<routingOperationName>",
  workCenterId: "<workCenterId>",
  workCenterName: "<workCenterName>",
  plannedStart: <value>,
  plannedEnd: <value>,
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

Dispatch idempotency/duplicate step assignment; exact target (work center vs resource); shift/calendar and capacity references; priority; reschedule/reassignment history; status transitions and concurrency.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: PASS — all defined MES references use ID/name pairs.
- Status vocabulary: PASS — exact values from DEC-011.
- Unresolved dependencies: see Section N; no silent resolution.


## P. Audit and Historical Data

Standard audit fields remain flat and paired. Operation execution, dispatch changes, downtime closure, reservation conflicts, and corrections require a durable chronology; detailed event-history ownership remains OPEN.

## Q. Real-World Manufacturing Exceptions

Handle reassignment, overlapping reservations, partial execution, pause/resume, unclosed downtime, and conflicting resource identity without silently altering parent references. Preserve correction history under approved controls.

## R. Missing Field Assessment

Collection-specific operational requirements are incomplete. Review with production supervision, workforce, tool-room, maintenance, performance, integration, and database roles. Known gaps: Dispatch idempotency/duplicate step assignment; exact target (work center vs resource); shift/calendar and capacity references; priority; reschedule/reassignment history; status transitions and concurrency.

## S. Missing Collection Assessment

No collection is added. Do not infer a machine/resource entity from Work Center. If a target, assignment history, or repeated event structure needs another owner, raise it for inventory review rather than embedding arrays.

## T. Cross-Collection Consistency Check

- Inventory scope checked against v1.6.
- Work Order and operation step references align with DEC-003/DEC-004.
- Approved PROD business-number/status rules applied where applicable (DEC-011/DEC-013).
- DEC-010 Double is used only for the specified downtime duration field here.
- Entity references use ID/name pairs; resource/asset gaps remain OPEN.
- Flat-document rule respected.

## U. Field Completeness Metrics

Field, reference, and index counts are in Sections E, F, and J. Detailed workflow requirements are not complete; no full scenario coverage percentage is claimed. Known gaps: Dispatch idempotency/duplicate step assignment; exact target (work center vs resource); shift/calendar and capacity references; priority; reschedule/reassignment history; status transitions and concurrency.

## V. One Representative MongoDB Document

The single illustrative flat structure is in Section L. No additional samples are generated.

## W. Assumptions

- Current inventory and approved project decisions govern scope.
- Repeated assignments and event history are separate records.
- ID/name duplication is intentional strategic denormalization.
- APPROVED follows prior user direction; open questions remain unresolved.

## X. Open Issues

Dispatch idempotency/duplicate step assignment; exact target (work center vs resource); shift/calendar and capacity references; priority; reschedule/reassignment history; status transitions and concurrency.

## Y. Research Sources

Collection-specific public references and the manufacturing scenarios they support are documented in Research basis below; vendor behavior does not set project rules.

## Z. Final Design Review

- **Status:** APPROVED per prior Project Owner instruction; not production readiness.
- **Covered:** bounded purpose, operational field baseline, relationships, approved decision alignment, research, and gaps.
- **Remaining:** Section X; assignment, event overlap, and target ownership rules must be resolved before implementation where marked blocking.
## Research basis

- [SAP Automatic Dispatching of Operations](https://help.sap.com/docs/sap-digital-manufacturing/resource-orchestration/automatic-dispatching-of-operations) — Operations are dispatched to resources with scheduling/availability and alternate-selection logic.
- [SAP Order Operation Dispatching](https://help.sap.com/docs/SAP_S4HANA_ON-PREMISE/e72f747389b340229f7fa343975bfa57/2177829da470429eb4bb1708e3c789c8.html) — Dispatch confirms an operation is scheduled at the correct work center/time and depends on material/tool readiness.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


