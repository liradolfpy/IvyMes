**FILE TYPE: PROJECT SPECIFIC**

# mes_perf_oeeResult — Collection Specification

**Document ID:** MES-COL-PERF-oeeResult  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** perf  
**Collection type:** Reporting  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Stores calculated OEE and component measures for an asset/work center and defined period.

## B. Business definition

OEE Result is a derived snapshot; it is not an operational source event. It must retain the calculation period and source/version lineage.

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
| `workCenterId` | String | Conditional | Work Center aggregation scope. | Pair with workCenterName; no machine/resource identity implied. | Yes: workCenterId + workCenterName -> mes_plan_workCenter | Candidate workCenterId+periodStart | Org-level reporting | DEC-022 | Dashboard |
| `workCenterName` | String | Conditional | Work Center snapshot. | Paired with workCenterId. | Yes: paired with workCenterId | No index | Readable scope | DATA-003 | Display |
| `assetId` | String | Conditional | Asset measurement scope when asset-level. | Pair with assetName. | Yes: assetId + assetName -> mes_maint_asset | Candidate assetId+periodStart | Equipment KPI | DEC-022 | OEE |
| `assetName` | String | Conditional | Asset snapshot. | Paired with assetId. | Yes: paired with assetId | No index | Readable scope | DATA-003 | Display |
| `shiftId` | String | Conditional | Shift aggregation dimension. | Pair with shiftName. | Yes: shiftId + shiftName -> mes_man_shift | Candidate shiftId+periodStart | Shift KPI | Inventory | Comparison |
| `shiftName` | String | Conditional | Shift snapshot. | Paired with shiftId. | Yes: paired with shiftId | No index | Readable shift | DATA-003 | Display |
| `periodStart` | Date | Yes | Calculation window start. | BSON Date < periodEnd. | No | Candidate periodStart+periodEnd | Time window | SAP OEE calculation | Rebuild window |
| `periodEnd` | Date | Yes | Calculation window end. | BSON Date > periodStart. | No | Candidate periodEnd | Time window | SAP OEE calculation | Rebuild window |
| `oeeValue` | OPEN | Yes | Calculated OEE percentage/value. | Numeric BSON type, scale, range and formula version unresolved. | No | No index | Derived KPI | Project numeric rules; SAP OEE | Performance |
| `calculatedAt` | Date | Yes | Calculation timestamp. | BSON Date. | No | Candidate calculatedAt | Refresh lineage | Inventory | Freshness |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `workCenterId` — Yes: workCenterId + workCenterName -> mes_plan_workCenter.
- `workCenterName` — Yes: paired with workCenterId.
- `assetId` — Yes: assetId + assetName -> mes_maint_asset.
- `assetName` — Yes: paired with assetId.
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

No lifecycle status established; calculation validity/version semantics OPEN.

## I. Relationships

**Outgoing:** Optional Work Center, Asset and Shift dimensions.  
**Incoming:** Performance dashboards and Analytics.  
**Dependencies:** Production/Downtime/Loss observations; shift/asset context; OEE formula and rebuild policy.

## J. Recommended indexes

- Default unique _id index.
- Candidate workCenterId+periodStart — Dashboard; verify query frequency and selectivity before implementation.
- Candidate assetId+periodStart — OEE; verify query frequency and selectivity before implementation.
- Candidate shiftId+periodStart — Comparison; verify query frequency and selectivity before implementation.
- Candidate periodStart+periodEnd — Rebuild window; verify query frequency and selectivity before implementation.
- Candidate periodEnd — Rebuild window; verify query frequency and selectivity before implementation.
- Candidate calculatedAt — Freshness; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  workCenterId: "<workCenterId>",
  workCenterName: "<workCenterName>",
  assetId: "<assetId>",
  assetName: "<assetName>",
  shiftId: "<shiftId>",
  shiftName: "<shiftName>",
  periodStart: <value>,
  periodEnd: <value>,
  oeeValue: <value>,
  calculatedAt: <value>,
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

Work Center vs Asset aggregation dimensions; formula/version; numeric type and percent scale; incomplete source events; time zone; recalculation and duplicate period policy; resource identity absent from inventory.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: Work Center, Asset and Shift targets exist; resource/OEE eligibility unresolved.
- Status vocabulary: N/A.
- Unresolved dependencies: see Section N; no silent resolution.

## P. Audit and Historical Data

The standard audit fields are defined in Section K. Preserve source chronology and corrections under approved project controls; retention, immutability and event-reversal policy remain OPEN where not decided.

## Q. Real-World Manufacturing Exceptions

Consider late and duplicate messages, incomplete source context, retries, overnight shifts, corrections and partial completion when applicable. Do not use nested data or scalar-encoded structured payloads to represent repeated facts.

## R. Missing Field Assessment

The current inventory bounds collection scope. Known field/workflow gaps remain: Work Center vs Asset aggregation dimensions; formula/version; numeric type and percent scale; incomplete source events; time zone; recalculation and duplicate period policy; resource identity absent from inventory.. Raise additional needs through requirements/change control rather than adding fields silently.

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

Work Center vs Asset aggregation dimensions; formula/version; numeric type and percent scale; incomplete source events; time zone; recalculation and duplicate period policy; resource identity absent from inventory.

## Y. Research Sources

See Research basis below. Sources support manufacturing workflows and scenarios; they do not approve unresolved project rules or transfer vendor schemas.

## Z. Final Design Review

Status label: APPROVED under prior Project Owner instruction. OPEN items remain unresolved. Flatness, reference conventions, inventory coverage and research evidence are summarized in Sections O and T.
## Research basis
- [SAP Digital Manufacturing OEE Calculations](https://help.sap.com/docs/sap-digital-manufacturing/insights/oee-calculations) — OEE is derived from Availability, Performance and Quality and source operation/downtime data.
- [SAP Digital Manufacturing Work Center Performance Calculation](https://help.sap.com/docs/sap-digital-manufacturing/insights/work-center-performance-kpi) — Work Center performance aggregates eligible parent resource values; project has no Resource identity, so exact aggregation target remains OPEN.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.

