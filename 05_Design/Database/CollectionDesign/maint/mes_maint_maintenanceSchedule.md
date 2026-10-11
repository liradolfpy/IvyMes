**FILE TYPE: PROJECT SPECIFIC**

# mes_maint_maintenanceSchedule — Collection Specification

**Document ID:** MES-COL-MAINT-maintenanceSchedule  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** maint  
**Collection type:** Transaction  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Schedules planned maintenance work in time windows for an Asset.

## B. Business definition

Schedule record links a maintenance order and asset to planned dates; it does not redefine order execution.

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
| `maintenanceWorkOrderId` | String | Yes | Scheduled maintenance order. | Pair with maintenanceWorkOrderName. | Yes: maintenanceWorkOrderId + maintenanceWorkOrderName -> mes_maint_maintenanceWorkOrder | Candidate maintenanceWorkOrderId+plannedStart | Scheduled job | Inventory; DATA-003 | Calendar |
| `maintenanceWorkOrderName` | String | Yes | Order snapshot. | Paired with maintenanceWorkOrderId. | Yes: paired with maintenanceWorkOrderId | No index | Readable job | DATA-003 | Display |
| `assetId` | String | Yes | Target Asset. | Pair with assetName. | Yes: assetId + assetName -> mes_maint_asset | Candidate assetId+plannedStart | Availability planning | DEC-022 | Asset calendar |
| `assetName` | String | Yes | Asset snapshot. | Paired with assetId. | Yes: paired with assetId | No index | Readable asset | DATA-003 | Display |
| `plannedStart` | Date | Yes | Scheduled start. | BSON Date; <= plannedEnd. | No | Candidate assetId+plannedStart | Time scheduling | SAP Planning Maintenance Work | Schedule |
| `plannedEnd` | Date | Yes | Scheduled end. | BSON Date >= plannedStart. | No | Candidate plannedEnd | Capacity window | SAP Planning Maintenance Work | Schedule |
| `status` | String | Yes | Schedule state. | Values OPEN. | No | Candidate status+plannedStart | Schedule control | Inventory | Planner |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `maintenanceWorkOrderId` — Yes: maintenanceWorkOrderId + maintenanceWorkOrderName -> mes_maint_maintenanceWorkOrder.
- `maintenanceWorkOrderName` — Yes: paired with maintenanceWorkOrderId.
- `assetId` — Yes: assetId + assetName -> mes_maint_asset.
- `assetName` — Yes: paired with assetId.

## G. Validation rules

- Flat top-level scalar fields only; no nested documents or arrays.
- MES IDs are 16-digit strings; external identifiers remain separate.
- Enforce only approved code patterns and uniqueness scopes. Do not invent OPEN scopes or status vocabularies.
- Required ID/display pairs are complete and consistent; optional pairs are both absent or both populated.
- Audit actor pairs are complete; updatedAt >= createdAt.
- Retired reference records remain historically resolvable when established by decision.

## H. Status values

Schedule state vocabulary OPEN.

## I. Relationships

**Outgoing:** Maintenance Work Order, Asset; optional Calendar/Employee references unresolved.  
**Incoming:** Maintenance execution.  
**Dependencies:** Maintenance Work Order; Asset availability; calendar and workforce availability.

## J. Recommended indexes

- Default unique _id index.
- Candidate maintenanceWorkOrderId+plannedStart — Calendar; verify query frequency and selectivity before implementation.
- Candidate assetId+plannedStart — Asset calendar; verify query frequency and selectivity before implementation.
- Candidate assetId+plannedStart — Schedule; verify query frequency and selectivity before implementation.
- Candidate plannedEnd — Schedule; verify query frequency and selectivity before implementation.
- Candidate status+plannedStart — Planner; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  maintenanceWorkOrderId: "<maintenanceWorkOrderId>",
  maintenanceWorkOrderName: "<maintenanceWorkOrderName>",
  assetId: "<assetId>",
  assetName: "<assetName>",
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

Whether separate from order planned dates; calendar/shift mapping; reschedule history; overlapping work constraints; technician identity; status values.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: Maintenance Work Order and Asset exist; calendar source open.
- Status vocabulary: OPEN.
- Unresolved dependencies: see Section N; no silent resolution.

## P. Audit and Historical Data

The standard audit fields are defined in Section K. Preserve source chronology and corrections under approved project controls; retention, immutability and event-reversal policy remain OPEN where not decided.

## Q. Real-World Manufacturing Exceptions

Consider late and duplicate messages, incomplete source context, retries, overnight shifts, corrections and partial completion when applicable. Do not use nested data or scalar-encoded structured payloads to represent repeated facts.

## R. Missing Field Assessment

The current inventory bounds collection scope. Known field/workflow gaps remain: Whether separate from order planned dates; calendar/shift mapping; reschedule history; overlapping work constraints; technician identity; status values.. Raise additional needs through requirements/change control rather than adding fields silently.

## S. Missing Collection Assessment

No collection is added by this specification. Repeated concepts remain separate flat records. Where an owning entity, item identity, or relationship record is absent, retain the gap for inventory review.

## T. Cross-Collection Consistency Check

- Inventory name/module and purpose checked against the current collection list.
- Flat scalar representation, singular key naming, ID/display pairs, and standard audit fields checked.
- Reference targets and lifecycle/type gaps are called out in Sections N, O, and X; unresolved items remain OPEN.

## U. Field Completeness Metrics

- Field names listed in the dictionary: 14, including system ID and audit fields.
- Entity reference pairs are enumerated in Section F; audit pairs are listed in Section K.
- Scenario and source evidence is collection-specific where documented; no unsupported requirement coverage percentage is claimed.

## V. One Representative MongoDB Document

The single illustrative flat structure is in Section L. No sample document set is generated in this phase.

## W. Assumptions

- Current project context, functional inventory, requirements and approved decisions bound this design.
- ID/display duplication is intentional strategic denormalization.
- APPROVED is the requested specification status; OPEN items remain unresolved and this label does not establish production readiness.

## X. Open Issues

Whether separate from order planned dates; calendar/shift mapping; reschedule history; overlapping work constraints; technician identity; status values.

## Y. Research Sources

See Research basis below. Sources support manufacturing workflows and scenarios; they do not approve unresolved project rules or transfer vendor schemas.

## Z. Final Design Review

Status label: APPROVED under prior Project Owner instruction. OPEN items remain unresolved. Flatness, reference conventions, inventory coverage and research evidence are summarized in Sections O and T.
## Research basis
- [SAP Planning Maintenance Work for Assets](https://help.sap.com/docs/SAP_S4HANA_ON-PREMISE/e72f747389b340229f7fa343975bfa57/41acaa2e02354a10a50dc102f60f8471.html) — Maintenance resource scheduling aligns maintenance orders and asset availability within planned windows.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.

