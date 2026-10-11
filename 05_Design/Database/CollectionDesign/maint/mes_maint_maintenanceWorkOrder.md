**FILE TYPE: PROJECT SPECIFIC**

# mes_maint_maintenanceWorkOrder — Collection Specification

**Document ID:** MES-COL-MAINT-maintenanceWorkOrder  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** maint  
**Collection type:** Transaction  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Tracks maintenance request/order planning and execution against an Asset.

## B. Business definition

Maintenance Work Order is a maintenance-domain job and is distinct from Production Work Order.

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
| `maintenanceWorkOrderNo` | String | Yes | Maintenance job identifier. | Pattern/uniqueness OPEN. | No | Candidate maintenanceWorkOrderNo | Job retrieval | Inventory | Maintenance queue |
| `assetId` | String | Yes | Maintained Asset. | Pair with assetName. | Yes: assetId + assetName -> mes_maint_asset | Candidate assetId+status | Technical object | DEC-022 | Asset history |
| `assetName` | String | Yes | Asset display snapshot. | Paired with assetId. | Yes: paired with assetId | No index | Readable asset | DATA-003 | Display |
| `failureCodeId` | String | Conditional | Failure classification if breakdown-related. | Pair with failureCodeName. | Yes: failureCodeId + failureCodeName -> mes_maint_failureCode | Candidate failureCodeId+createdAt | Failure analytics | Inventory; DATA-003 | Breakdown |
| `failureCodeName` | String | Conditional | Failure code snapshot. | Paired with failureCodeId. | Yes: paired with failureCodeId | No index | Readable cause | DATA-003 | Display |
| `maintenancePlanId` | String | Conditional | Preventive plan reference. | Pair with maintenancePlanName. | Yes: maintenancePlanId + maintenancePlanName -> mes_maint_maintenancePlan | Candidate maintenancePlanId+status | Planned work provenance | Inventory | Preventive work |
| `maintenancePlanName` | String | Conditional | Plan snapshot. | Paired with maintenancePlanId. | Yes: paired with maintenancePlanId | No index | Readable plan | DATA-003 | Display |
| `status` | String | Yes | Maintenance work lifecycle. | Values OPEN. | No | Candidate status+plannedStart | Job state | SAP Maintenance Order | Queue |
| `plannedStart` | Date | Conditional | Planned start. | BSON Date; <= plannedEnd. | No | Candidate status+plannedStart | Scheduling | SAP Maintenance Order | Plan |
| `plannedEnd` | Date | Conditional | Planned end. | BSON Date >= plannedStart. | No | Candidate plannedEnd | Scheduling | SAP Maintenance Order | Plan |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `assetId` — Yes: assetId + assetName -> mes_maint_asset.
- `assetName` — Yes: paired with assetId.
- `failureCodeId` — Yes: failureCodeId + failureCodeName -> mes_maint_failureCode.
- `failureCodeName` — Yes: paired with failureCodeId.
- `maintenancePlanId` — Yes: maintenancePlanId + maintenancePlanName -> mes_maint_maintenancePlan.
- `maintenancePlanName` — Yes: paired with maintenancePlanId.

## G. Validation rules

- Flat top-level scalar fields only; no nested documents or arrays.
- MES IDs are 16-digit strings; external identifiers remain separate.
- Enforce only approved code patterns and uniqueness scopes. Do not invent OPEN scopes or status vocabularies.
- Required ID/display pairs are complete and consistent; optional pairs are both absent or both populated.
- Audit actor pairs are complete; updatedAt >= createdAt.
- Retired reference records remain historically resolvable when established by decision.

## H. Status values

Maintenance-specific lifecycle OPEN; do not reuse production-order values.

## I. Relationships

**Outgoing:** Asset, optional Maintenance Plan and Failure Code; materials and technician ownership remain OPEN.  
**Incoming:** Maintenance Schedule, Checklist Result, Asset history and analytics.  
**Dependencies:** Asset; failure classification; maintenance plan; workforce/material/tool relationships.

## J. Recommended indexes

- Default unique _id index.
- Candidate maintenanceWorkOrderNo — Maintenance queue; verify query frequency and selectivity before implementation.
- Candidate assetId+status — Asset history; verify query frequency and selectivity before implementation.
- Candidate failureCodeId+createdAt — Breakdown; verify query frequency and selectivity before implementation.
- Candidate maintenancePlanId+status — Preventive work; verify query frequency and selectivity before implementation.
- Candidate status+plannedStart — Queue; verify query frequency and selectivity before implementation.
- Candidate status+plannedStart — Plan; verify query frequency and selectivity before implementation.
- Candidate plannedEnd — Plan; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  maintenanceWorkOrderNo: "<maintenanceWorkOrderNo>",
  assetId: "<assetId>",
  assetName: "<assetName>",
  failureCodeId: "<failureCodeId>",
  failureCodeName: "<failureCodeName>",
  maintenancePlanId: "<maintenancePlanId>",
  maintenancePlanName: "<maintenancePlanName>",
  status: "<status>",
  plannedStart: <value>,
  plannedEnd: <value>,
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

Number format; order/operation task decomposition; labor/material repeated-record owners; checklist link; technician ID/name source; status/close rules; connection to asset downtime events.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: Asset, Maintenance Plan and Failure Code targets exist; workforce and operation details OPEN.
- Status vocabulary: OPEN.
- Unresolved dependencies: see Section N; no silent resolution.

## P. Audit and Historical Data

The standard audit fields are defined in Section K. Preserve source chronology and corrections under approved project controls; retention, immutability and event-reversal policy remain OPEN where not decided.

## Q. Real-World Manufacturing Exceptions

Consider late and duplicate messages, incomplete source context, retries, overnight shifts, corrections and partial completion when applicable. Do not use nested data or scalar-encoded structured payloads to represent repeated facts.

## R. Missing Field Assessment

The current inventory bounds collection scope. Known field/workflow gaps remain: Number format; order/operation task decomposition; labor/material repeated-record owners; checklist link; technician ID/name source; status/close rules; connection to asset downtime events.. Raise additional needs through requirements/change control rather than adding fields silently.

## S. Missing Collection Assessment

No collection is added by this specification. Repeated concepts remain separate flat records. Where an owning entity, item identity, or relationship record is absent, retain the gap for inventory review.

## T. Cross-Collection Consistency Check

- Inventory name/module and purpose checked against the current collection list.
- Flat scalar representation, singular key naming, ID/display pairs, and standard audit fields checked.
- Reference targets and lifecycle/type gaps are called out in Sections N, O, and X; unresolved items remain OPEN.

## U. Field Completeness Metrics

- Field names listed in the dictionary: 17, including system ID and audit fields.
- Entity reference pairs are enumerated in Section F; audit pairs are listed in Section K.
- Scenario and source evidence is collection-specific where documented; no unsupported requirement coverage percentage is claimed.

## V. One Representative MongoDB Document

The single illustrative flat structure is in Section L. No sample document set is generated in this phase.

## W. Assumptions

- Current project context, functional inventory, requirements and approved decisions bound this design.
- ID/display duplication is intentional strategic denormalization.
- APPROVED is the requested specification status; OPEN items remain unresolved and this label does not establish production readiness.

## X. Open Issues

Number format; order/operation task decomposition; labor/material repeated-record owners; checklist link; technician ID/name source; status/close rules; connection to asset downtime events.

## Y. Research Sources

See Research basis below. Sources support manufacturing workflows and scenarios; they do not approve unresolved project rules or transfer vendor schemas.

## Z. Final Design Review

Status label: APPROVED under prior Project Owner instruction. OPEN items remain unresolved. Flatness, reference conventions, inventory coverage and research evidence are summarized in Sections O and T.
## Research basis
- [SAP S/4HANA Process Maintenance Order](https://help.sap.com/docs/SAP_S4HANA_ON-PREMISE/e72f747389b340229f7fa343975bfa57/edd8ec56b1f26038e10000000a44147b.html) — Maintenance orders plan/execute work against technical objects and can reference plans, materials, schedules and task operations.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.

