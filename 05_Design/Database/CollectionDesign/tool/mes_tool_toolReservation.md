**FILE TYPE: PROJECT SPECIFIC**

# mes_tool_toolReservation — Collection Specification

**Document ID:** MES-COL-TOOL-toolReservation  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** tool  
**Collection type:** Transaction  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Records reservation of a Tool for a scheduled order or operation over a defined time interval.

## B. Business definition

Tool Reservation is a time-bounded claim/reservation, distinct from Tool master identity, physical movement, and actual usage. Each reservation is a separate flat transaction.

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
| `toolId` | String | Yes | Reserved Tool identity. | 16-digit ID paired with toolName. | Yes: toolId + toolName -> mes_tool_tool | Candidate tool+time | Identify reserved equipment. | Inventory; SAP Schedule Tools | Availability check |
| `toolName` | String | Yes | Tool display snapshot. | Paired with toolId. | Yes: paired with toolId | No index by default | Readable reservation. | DATA-003 | Tool assignment |
| `workOrderId` | String | Conditional (proposed) | Work Order consuming reservation. | If present pair with workOrderName. | Yes: workOrderId + workOrderName -> mes_prod_workOrder | Candidate workOrderId+start | Execution relationship. | DEC-004; inventory | Order planning |
| `workOrderName` | String | Conditional | Work Order display snapshot. | Both fields present or absent. | Yes: paired with workOrderId | No index by default | Readable link. | DATA-003 | Reservation display |
| `operationId` | String | Conditional (proposed) | Operation associated with reserved tool. | If present pair with operationName; reusable operation vs routing step unresolved. | Yes: operationId + operationName -> mes_prod_operation | Candidate operationId+start | Process-specific tool planning. | DEC-003; inventory | Operation resource requirement |
| `operationName` | String | Conditional | Operation display snapshot. | Both fields present or absent. | Yes: paired with operationId | No index by default | Readable context. | DATA-003 | Assignment UI |
| `reservedStart` | Date | Yes (proposed) | Start of reserved interval. | BSON Date; <= reservedEnd. | No | Candidate tool+reservedStart | Conflict detection. | SAP Schedule Tools | Availability |
| `reservedEnd` | Date | Yes (proposed) | End of reservation interval. | BSON Date >= reservedStart. | No | Candidate tool+reservedEnd | Release reservation window. | Inventory | Availability |
| `status` | String | Yes (proposed) | Reservation lifecycle state. | Vocabulary, conflict/override and release semantics OPEN. | No | Candidate tool+status+reservedStart | Track allocation state. | Lifecycle rules OPEN | Tool availability |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `toolId` — Yes: toolId + toolName -> mes_tool_tool.
- `toolName` — Yes: paired with toolId.
- `workOrderId` — Yes: workOrderId + workOrderName -> mes_prod_workOrder.
- `workOrderName` — Yes: paired with workOrderId.
- `operationId` — Yes: operationId + operationName -> mes_prod_operation.
- `operationName` — Yes: paired with operationId.

## G. Validation rules

- Flat top-level scalar fields only; no nested documents or arrays.
- MES IDs are 16-digit strings; external identifiers remain separate.
- Enforce only approved code patterns and uniqueness scopes. Do not invent OPEN scopes or status vocabularies.
- Required ID/display pairs are complete and consistent; optional pairs are both absent or both populated.
- Audit actor pairs are complete; updatedAt >= createdAt.
- Retired reference records remain historically resolvable when established by decision.

## H. Status values

Reservation-specific lifecycle, distinct from Tool status and usage; values OPEN.

## I. Relationships

**Outgoing:** Tool, optional Work Order and Operation pairs.  
**Incoming:** Tool availability, schedule and execution workflows.  
**Dependencies:** Tool master; Work Order; reusable Operation or routing step; reservation conflict rule and schedule target unresolved.

## J. Recommended indexes

- Default unique _id index.
- Candidate tool+time — Availability check; verify query frequency and selectivity before implementation.
- Candidate workOrderId+start — Order planning; verify query frequency and selectivity before implementation.
- Candidate operationId+start — Operation resource requirement; verify query frequency and selectivity before implementation.
- Candidate tool+reservedStart — Availability; verify query frequency and selectivity before implementation.
- Candidate tool+reservedEnd — Availability; verify query frequency and selectivity before implementation.
- Candidate tool+status+reservedStart — Tool availability; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  toolId: "<toolId>",
  toolName: "<toolName>",
  workOrderId: "<workOrderId>",
  workOrderName: "<workOrderName>",
  operationId: "<operationId>",
  operationName: "<operationName>",
  reservedStart: <value>,
  reservedEnd: <value>,
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

Operation vs routingOperation reference; required order context; quantity/count; overlap/conflict locking; cancel/release and actual-use linkage; reservation number convention; external scheduling synchronization.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: OPEN — Operation vs routing step reference unresolved.
- Status vocabulary: OPEN — values not approved.
- Unresolved dependencies: see Section N; no silent resolution.


## P. Audit and Historical Data

Standard audit fields remain flat and paired. Operation execution, dispatch changes, downtime closure, reservation conflicts, and corrections require a durable chronology; detailed event-history ownership remains OPEN.

## Q. Real-World Manufacturing Exceptions

Handle reassignment, overlapping reservations, partial execution, pause/resume, unclosed downtime, and conflicting resource identity without silently altering parent references. Preserve correction history under approved controls.

## R. Missing Field Assessment

Collection-specific operational requirements are incomplete. Review with production supervision, workforce, tool-room, maintenance, performance, integration, and database roles. Known gaps: Operation vs routingOperation reference; required order context; quantity/count; overlap/conflict locking; cancel/release and actual-use linkage; reservation number convention; external scheduling synchronization.

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

Field, reference, and index counts are in Sections E, F, and J. Detailed workflow requirements are not complete; no full scenario coverage percentage is claimed. Known gaps: Operation vs routingOperation reference; required order context; quantity/count; overlap/conflict locking; cancel/release and actual-use linkage; reservation number convention; external scheduling synchronization.

## V. One Representative MongoDB Document

The single illustrative flat structure is in Section L. No additional samples are generated.

## W. Assumptions

- Current inventory and approved project decisions govern scope.
- Repeated assignments and event history are separate records.
- ID/name duplication is intentional strategic denormalization.
- APPROVED follows prior user direction; open questions remain unresolved.

## X. Open Issues

Operation vs routingOperation reference; required order context; quantity/count; overlap/conflict locking; cancel/release and actual-use linkage; reservation number convention; external scheduling synchronization.

## Y. Research Sources

Collection-specific public references and the manufacturing scenarios they support are documented in Research basis below; vendor behavior does not set project rules.

## Z. Final Design Review

- **Status:** APPROVED per prior Project Owner instruction; not production readiness.
- **Covered:** bounded purpose, operational field baseline, relationships, approved decision alignment, research, and gaps.
- **Remaining:** Section X; assignment, event overlap, and target ownership rules must be resolved before implementation where marked blocking.
## Research basis

- [SAP Digital Manufacturing Worklist and Reserve Tools](https://help.sap.com/docs/sap-digital-manufacturing/resource-orchestration/worklist-area) — Tool reservation checks availability for an operation and overlapping reservations can conflict.
- [SAP Assigning Work Centers and Resources to Tools](https://help.sap.com/docs/sap-digital-manufacturing/resource-orchestration/assigning-work-centers-and-resources-to-tools) — Tool allocations are time-scoped and can be assigned across dates/work centers/resources; project resource relation remains constrained.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


