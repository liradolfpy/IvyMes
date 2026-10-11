**FILE TYPE: PROJECT SPECIFIC**

# mes_prod_operationExecution — Collection Specification

**Document ID:** MES-COL-PROD-operationExecution  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** prod  
**Collection type:** Transaction  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Records actual start/completion and execution outcomes for a Work Order’s routing-specific operation step.

## B. Business definition

Operation Execution records one execution attempt/confirmation for a routing step, supporting quantities, timing, operator and work-center evidence. Repeated attempts or pauses must be separate records/events according to a rule not yet settled.

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
| `operationExecutionNo` | String | No (DEC-013) | Optional business number for execution record. | OX-YYYY-###### when present; unique within collection when present (DEC-013). | No | Unique partial index when present; scope per DEC-013 | External/reportable execution identifier. | DEC-013 | Execution reconciliation |
| `workOrderId` | String | Yes | Parent Work Order. | 16-digit ID paired with workOrderName. | Yes: workOrderId + workOrderName -> mes_prod_workOrder | Candidate workOrderId+status+actualStart | Execution parent. | DEC-004; DATA-003 | Work Order progress |
| `workOrderName` | String | Yes | Work Order display snapshot. | Paired with workOrderId. | Yes: paired with workOrderId | No index by default | Readable reference. | DATA-003 | Execution display |
| `routingOperationId` | String | Yes | Exact routing operation step executed. | 16-digit ID paired with routingOperationName. | Yes: routingOperationId + routingOperationName -> mes_prod_routingOperation | Candidate workOrder+step+actualStart | Execution at correct step. | DEC-003; DATA-003 | Operation tracking |
| `routingOperationName` | String | Yes | Routing step display snapshot. | Paired with routingOperationId. | Yes: paired with routingOperationId | No index by default | Operator display. | DATA-003 | Execution |
| `operatorId` | String | Conditional (proposed) | Operator performing the work. | If present paired with operatorName. | Yes: operatorId + operatorName -> mes_man_operator | Candidate operator+actualStart | Labor attribution. | DEC-022; inventory | Execution accountability |
| `operatorName` | String | Conditional | Operator display snapshot. | Both fields present or absent. | Yes: paired with operatorId | No index by default | Readable attribution. | DATA-003 | Execution UI |
| `workCenterId` | String | Conditional (proposed) | Organizational work-center context for execution. | If present paired with workCenterName; not a resource identity. | Yes: workCenterId + workCenterName -> mes_plan_workCenter | Candidate workCenter+actualStart | Execution location context. | DEC-022; inventory | Shop-floor execution |
| `workCenterName` | String | Conditional | Work-center display snapshot. | Both fields present or absent. | Yes: paired with workCenterId | No index by default | Readable context. | DATA-003 | Execution display |
| `actualStart` | Date | Conditional | Execution start timestamp. | BSON Date; <= actualEnd. | No | Candidate workOrder+actualStart | Cycle/labor time evidence. | SAP Start Operation action | Start |
| `actualEnd` | Date | Conditional | Execution completion timestamp. | BSON Date >= actualStart. | No | Candidate workOrder+actualEnd | Completion evidence. | SAP Start/Complete operation | Complete |
| `status` | String | Yes | Operation execution lifecycle. | NotStarted, InProgress, Paused, Completed, Cancelled (DEC-011). | No | Candidate status+workOrder | Operation state. | DEC-011 | Execution lifecycle |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `workOrderId` — Yes: workOrderId + workOrderName -> mes_prod_workOrder.
- `workOrderName` — Yes: paired with workOrderId.
- `routingOperationId` — Yes: routingOperationId + routingOperationName -> mes_prod_routingOperation.
- `routingOperationName` — Yes: paired with routingOperationId.
- `operatorId` — Yes: operatorId + operatorName -> mes_man_operator.
- `operatorName` — Yes: paired with operatorId.
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

Approved collection-specific values per DEC-011. Attempt/retry, pause, and correction transition rules OPEN.

## I. Relationships

**Outgoing:** Work Order, Routing Operation, optional Operator and Work Center pairs.  
**Incoming:** Production Report, Downtime, Quality, Material, Traceability, Performance and labor records.  
**Dependencies:** Work Order, Routing Operation, optional Operator/Work Center. Actual quantity types are unresolved except specified fields under DEC-010.

## J. Recommended indexes

- Default unique _id index.
- Unique partial index when present; scope per DEC-013 — Execution reconciliation; verify query frequency and selectivity before implementation.
- Candidate workOrderId+status+actualStart — Work Order progress; verify query frequency and selectivity before implementation.
- Candidate workOrder+step+actualStart — Operation tracking; verify query frequency and selectivity before implementation.
- Candidate operator+actualStart — Execution accountability; verify query frequency and selectivity before implementation.
- Candidate workCenter+actualStart — Shop-floor execution; verify query frequency and selectivity before implementation.
- Candidate workOrder+actualStart — Start; verify query frequency and selectivity before implementation.
- Candidate workOrder+actualEnd — Complete; verify query frequency and selectivity before implementation.
- Candidate status+workOrder — Execution lifecycle; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  operationExecutionNo: "<operationExecutionNo>",
  workOrderId: "<workOrderId>",
  workOrderName: "<workOrderName>",
  routingOperationId: "<routingOperationId>",
  routingOperationName: "<routingOperationName>",
  operatorId: "<operatorId>",
  operatorName: "<operatorName>",
  workCenterId: "<workCenterId>",
  workCenterName: "<workCenterName>",
  actualStart: <value>,
  actualEnd: <value>,
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

Execution grain (one record vs each attempt); production quantity/scrap fields and numeric type; pause intervals; machine/resource ownership; operator cardinality; corrected/void records; actual time semantics and status transitions.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: PASS — defined entities use ID/name pairs.
- Status vocabulary: PASS — exact values from DEC-011.
- Unresolved dependencies: see Section N; no silent resolution.


## P. Audit and Historical Data

Standard audit fields remain flat and paired. Operation execution, dispatch changes, downtime closure, reservation conflicts, and corrections require a durable chronology; detailed event-history ownership remains OPEN.

## Q. Real-World Manufacturing Exceptions

Handle reassignment, overlapping reservations, partial execution, pause/resume, unclosed downtime, and conflicting resource identity without silently altering parent references. Preserve correction history under approved controls.

## R. Missing Field Assessment

Collection-specific operational requirements are incomplete. Review with production supervision, workforce, tool-room, maintenance, performance, integration, and database roles. Known gaps: Execution grain (one record vs each attempt); production quantity/scrap fields and numeric type; pause intervals; machine/resource ownership; operator cardinality; corrected/void records; actual time semantics and status transitions.

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

Field, reference, and index counts are in Sections E, F, and J. Detailed workflow requirements are not complete; no full scenario coverage percentage is claimed. Known gaps: Execution grain (one record vs each attempt); production quantity/scrap fields and numeric type; pause intervals; machine/resource ownership; operator cardinality; corrected/void records; actual time semantics and status transitions.

## V. One Representative MongoDB Document

The single illustrative flat structure is in Section L. No additional samples are generated.

## W. Assumptions

- Current inventory and approved project decisions govern scope.
- Repeated assignments and event history are separate records.
- ID/name duplication is intentional strategic denormalization.
- APPROVED follows prior user direction; open questions remain unresolved.

## X. Open Issues

Execution grain (one record vs each attempt); production quantity/scrap fields and numeric type; pause intervals; machine/resource ownership; operator cardinality; corrected/void records; actual time semantics and status transitions.

## Y. Research Sources

Collection-specific public references and the manufacturing scenarios they support are documented in Research basis below; vendor behavior does not set project rules.

## Z. Final Design Review

- **Status:** APPROVED per prior Project Owner instruction; not production readiness.
- **Covered:** bounded purpose, operational field baseline, relationships, approved decision alignment, research, and gaps.
- **Remaining:** Section X; assignment, event overlap, and target ownership rules must be resolved before implementation where marked blocking.
## Research basis

- [SAP Digital Manufacturing Start Operation/Phase](https://help.sap.com/docs/sap-digital-manufacturing/application-help-for-manage-pods-2-0/start-operation-phase) — Operation start records operator, plant, resource, order, process lot and SFC context; used for scenario derivation.
- [SAP Digital Manufacturing Order POD](https://help.sap.com/docs/sap-digital-manufacturing/execution/order-pod) — Execution reports yield/scrap, starts/completes operations and records work-instruction context.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


