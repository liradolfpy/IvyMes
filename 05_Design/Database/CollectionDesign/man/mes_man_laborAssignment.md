**FILE TYPE: PROJECT SPECIFIC**

# mes_man_laborAssignment — Collection Specification

**Document ID:** MES-COL-MAN-laborAssignment  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** man  
**Collection type:** Transaction  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Records assignment of an Operator to an order/operation, shift, and work area for a scheduled or actual labor interval.

## B. Business definition

Labor Assignment is a temporal workforce-to-execution relationship. It does not replace Employee, Operator, crew, shift, or time-entry records; repeated assignments are separate documents.

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
| `operatorId` | String | Yes | Operator assigned to the work. | 16-digit ID paired with operatorName. | Yes: operatorId + operatorName -> mes_man_operator | Candidate operator+start | Workforce assignment. | DEC-022; DATA-003 | Staffing |
| `operatorName` | String | Yes | Operator display snapshot. | Paired with operatorId. | Yes: paired with operatorId | No index by default | Readable assignment. | DATA-003 | Shop-floor UI |
| `workOrderId` | String | Conditional (proposed) | Work Order this labor assignment supports. | If present pair with workOrderName. | Yes: workOrderId + workOrderName -> mes_prod_workOrder | Candidate workOrderId+start | Execution relationship. | DEC-004; DATA-003 | Work Order staffing |
| `workOrderName` | String | Conditional | Work Order display/business-number snapshot. | Both fields present or absent. | Yes: paired with workOrderId | No index by default | Readable context. | DATA-003 | Assignment list |
| `shiftId` | String | Conditional (proposed) | Shift associated with assignment. | If present pair with shiftName. | Yes: shiftId + shiftName -> mes_man_shift | Candidate shift+start | Shift staffing context. | Inventory; SAP Schedule Labor | Shift roster |
| `shiftName` | String | Conditional | Shift display snapshot. | Both fields present or absent. | Yes: paired with shiftId | No index by default | Readable assignment. | DATA-003 | Roster |
| `assignedStart` | Date | Yes (proposed) | Start of labor assignment interval. | BSON Date; <= assignedEnd. | No | Candidate operator+interval | Schedule and conflict detection. | SAP Schedule Labor | Assignment window |
| `assignedEnd` | Date | Conditional | End of labor assignment interval. | BSON Date >= assignedStart. | No | Candidate operator+interval | Close assignment. | Inventory temporal relationship | Conflict detection |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `operatorId` — Yes: operatorId + operatorName -> mes_man_operator.
- `operatorName` — Yes: paired with operatorId.
- `workOrderId` — Yes: workOrderId + workOrderName -> mes_prod_workOrder.
- `workOrderName` — Yes: paired with workOrderId.
- `shiftId` — Yes: shiftId + shiftName -> mes_man_shift.
- `shiftName` — Yes: paired with shiftId.

## G. Validation rules

- Flat top-level scalar fields only; no nested documents or arrays.
- MES IDs are 16-digit strings; external identifiers remain separate.
- Enforce only approved code patterns and uniqueness scopes. Do not invent OPEN scopes or status vocabularies.
- Required ID/display pairs are complete and consistent; optional pairs are both absent or both populated.
- Audit actor pairs are complete; updatedAt >= createdAt.
- Retired reference records remain historically resolvable when established by decision.

## H. Status values

No status field proposed; planned/actual assignment and cancellation semantics need decision.

## I. Relationships

**Outgoing:** Operator and optional Work Order/Shift pairs. Work Center assignment omitted until exact relationship rule is established.  
**Incoming:** Labor Time Entry and workforce staffing reports.  
**Dependencies:** Operator, Work Order and Shift. Work-center-specific labor allocation and Employee vs Operator authorization boundary remain open.

## J. Recommended indexes

- Default unique _id index.
- Candidate operator+start — Staffing; verify query frequency and selectivity before implementation.
- Candidate workOrderId+start — Work Order staffing; verify query frequency and selectivity before implementation.
- Candidate shift+start — Shift roster; verify query frequency and selectivity before implementation.
- Candidate operator+interval — Assignment window; verify query frequency and selectivity before implementation.
- Candidate operator+interval — Conflict detection; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  operatorId: "<operatorId>",
  operatorName: "<operatorName>",
  workOrderId: "<workOrderId>",
  workOrderName: "<workOrderName>",
  shiftId: "<shiftId>",
  shiftName: "<shiftName>",
  assignedStart: <value>,
  assignedEnd: <value>,
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

Assignment vs attendance/time-entry boundary; required Work Order/operation scope; work-center reference; assignment status; interval overlap; operator availability/qualification validation; shift crossing midnight; cancellation history.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: PASS — Operator/Work Order/Shift pairs defined.
- Status vocabulary: OPEN — assignment lifecycle not specified.
- Unresolved dependencies: see Section N; no silent resolution.


## P. Audit and Historical Data

Standard audit fields are flat and paired. Trace identities and inventory/labor history must remain resolvable over their approved retention period; source synchronization and immutability remain OPEN.

## Q. Real-World Manufacturing Exceptions

Preserve orphaned or externally mismatched trace identifiers for reconciliation; do not merge lot, serial, balance, or labor-assignment meanings. Quantity corrections and identity conflicts need controlled workflows.

## R. Missing Field Assessment

Detailed requirements are incomplete. Review with traceability, material control, workforce, production, integration, and database roles. Known gaps: Assignment vs attendance/time-entry boundary; required Work Order/operation scope; work-center reference; assignment status; interval overlap; operator availability/qualification validation; shift crossing midnight; cancellation history.

## S. Missing Collection Assessment

No collection is added. The inventory remains authoritative. If additional identity dimensions, movement/event history, or relationship owners are needed, request inventory review rather than embedding arrays.

## T. Cross-Collection Consistency Check

- Inventory scope/type checked against v1.6.
- Product, Material, Plant, Operator, Work Order, Shift, Location and UOM pairs use project naming rules where defined.
- Lot and Serial are distinct identities; Inventory Balance is a state/balance, not a movement.
- Numeric balance type, identity namespace, and status rules remain OPEN.
- Flat-document convention respected.

## U. Field Completeness Metrics

See Sections E, F, and J for field, reference, and index counts. Detailed requirements are incomplete; no full scenario coverage percentage is claimed. Known gaps: Assignment vs attendance/time-entry boundary; required Work Order/operation scope; work-center reference; assignment status; interval overlap; operator availability/qualification validation; shift crossing midnight; cancellation history.

## V. One Representative MongoDB Document

The sole illustrative flat structure appears in Section L; no sample documents are generated.

## W. Assumptions

- Inventory and approved ownership decisions define scope.
- Repeated relationships are separate flat records.
- ID/name duplication is intentional strategic denormalization.
- APPROVED follows prior owner direction; unresolved questions remain OPEN.

## X. Open Issues

Assignment vs attendance/time-entry boundary; required Work Order/operation scope; work-center reference; assignment status; interval overlap; operator availability/qualification validation; shift crossing midnight; cancellation history.

## Y. Research Sources

Collection-specific public sources and their functional relevance appear under Research basis below; they do not establish project identifiers or lifecycle values.

## Z. Final Design Review

- **Status:** APPROVED per prior Project Owner instruction; not production readiness.
- **Covered:** bounded purpose, candidate fields, flat representation, references, research and known gaps.
- **Remaining:** Section X; source identity and quantity semantics must be resolved before implementation where marked blocking.
## Research basis

- [SAP Digital Manufacturing Schedule Labor 2.0](https://help.sap.com/docs/sap-digital-manufacturing/resource-orchestration/schedule-labor-2-0?locale=en-US&state=PRODUCTION&version=latest) — Operators can be allocated by shift and work center based on demand and qualifications; informs assignment scenarios.
- [SAP Digital Manufacturing Role Templates](https://help.sap.com/docs/sap-digital-manufacturing/operations-guide/role-templates) — Labor scheduling includes shift, skill, availability and workload factors; no schema copied.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


