**FILE TYPE: PROJECT SPECIFIC**

# mes_maint_maintenancePlan — Collection Specification

**Document ID:** MES-COL-MAINT-maintenancePlan  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** maint  
**Collection type:** Configuration  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Defines preventive maintenance tasks, interval, and applicable assets.

## B. Business definition

Maintenance Plan schedules maintenance work for one or more maintainable assets or classes and may generate maintenance work orders. Checklist ownership and plan/item grain are unresolved.

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
| `maintenancePlanCode` | String | Yes (proposed) | Business identifier for maintenance plan. | Pattern/uniqueness OPEN. | No | Candidate code+status | Select schedule definition. | Inventory; SAP Maintenance Plan | Preventive maintenance |
| `maintenancePlanName` | String | Yes | Readable plan name. | Non-empty. | No | Candidate text search | Planner/technician display. | DATA-003 | Maintenance planning |
| `assetId` | String | Conditional (proposed) | Applicable Asset identity when plan is asset-specific. | If present paired with assetName; class/group scope unresolved. | Yes: assetId + assetName -> mes_maint_asset | Candidate assetId | Schedule asset work. | DEC-022; SAP Maintenance Plan | Due work generation |
| `assetName` | String | Conditional | Asset display snapshot. | Both fields present or absent. | Yes: paired with assetId | No index by default | Readable scope. | DATA-003 | Maintenance order |
| `maintenanceIntervalValue` | Numeric BSON type OPEN | Yes (proposed) | Interval magnitude for preventive work. | Type, unit, calendar-vs-counter basis OPEN. | No | No index by default | Calculate next due point. | SAP Maintenance Plan | Preventive cycle |
| `maintenanceIntervalUnit` | String | Conditional | Unit/basis for interval value. | Time/counter taxonomy OPEN. | Potential UOM reference; exact unit semantics unresolved. | No index by default | Interpret interval. | Inventory; SAP maintenance cycles | Scheduling |
| `checklistDefinitionId` | String | Conditional (proposed) | Reusable checklist reference if plan assigns one. | If present paired with checklistDefinitionName; direction OPEN. | Yes: checklistDefinitionId + checklistDefinitionName -> mes_maint_checklistDefinition | Candidate checklistDefinitionId | Attach controlled check activities. | Inventory; dependency map open question | Work preparation |
| `checklistDefinitionName` | String | Conditional | Checklist display snapshot. | Both fields present or absent. | Yes: paired with checklistDefinitionId | No index by default | Readable plan. | DATA-003 | Maintenance planning |
| `status` | String | Yes (proposed) | Preventive plan lifecycle/authorization. | Vocabulary and transitions OPEN. | No | Candidate status filter | Control schedule generation. | Lifecycle rules OPEN | Schedule |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `assetId` — Yes: assetId + assetName -> mes_maint_asset.
- `assetName` — Yes: paired with assetId.
- `checklistDefinitionId` — Yes: checklistDefinitionId + checklistDefinitionName -> mes_maint_checklistDefinition.
- `checklistDefinitionName` — Yes: paired with checklistDefinitionId.

## G. Validation rules

- Flat top-level scalar fields only; no nested documents or arrays.
- MES IDs are 16-digit strings; external identifiers remain separate.
- Enforce only approved code patterns and uniqueness scopes. Do not invent OPEN scopes or status vocabularies.
- Required ID/display pairs are complete and consistent; optional pairs are both absent or both populated.
- Audit actor pairs are complete; updatedAt >= createdAt.
- Retired reference records remain historically resolvable when established by decision.

## H. Status values

Plan lifecycle/authorization, independent of generated work-order status; values OPEN.

## I. Relationships

**Outgoing:** Asset and optional Checklist Definition reference pairs.  
**Incoming:** Maintenance schedules and generated maintenance work orders.  
**Dependencies:** mes_maint_asset and possibly checklistDefinition; plan/checklist direction unresolved. Calendar and counter sources not decided.

## J. Recommended indexes

- Default unique _id index.
- Candidate code+status — Preventive maintenance; verify query frequency and selectivity before implementation.
- Candidate text search — Maintenance planning; verify query frequency and selectivity before implementation.
- Candidate assetId — Due work generation; verify query frequency and selectivity before implementation.
- Candidate checklistDefinitionId — Work preparation; verify query frequency and selectivity before implementation.
- Candidate status filter — Schedule; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  maintenancePlanCode: "<maintenancePlanCode>",
  maintenancePlanName: "<maintenancePlanName>",
  assetId: "<assetId>",
  assetName: "<assetName>",
  maintenanceIntervalValue: <value>,
  maintenanceIntervalUnit: "<maintenanceIntervalUnit>",
  checklistDefinitionId: "<checklistDefinitionId>",
  checklistDefinitionName: "<checklistDefinitionName>",
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

Plan vs maintenance-item grain; asset vs asset-class scope; interval type/unit/cycle anchor; checklist direction; calendar/counter sources; due generation and overdue handling; lifecycle vocabulary.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: OPEN — checklist direction and asset scope unresolved.
- Status vocabulary: OPEN — values not approved.
- Unresolved dependencies: see Section N; no silent resolution.


## P. Audit and Historical Data

Standard audit fields are flat and paired. Preserve revisions and effective dates without embedded history lists. Actor-source, retention, immutable revision, and approval evidence rules remain OPEN.

## Q. Real-World Manufacturing Exceptions

Do not infer sample sizes, acceptance thresholds, maintenance frequencies, checklist outcomes, or value vocabularies. Keep invalid or obsolete references identifiable for controlled correction.

## R. Missing Field Assessment

Collection-specific functional requirements are not sufficient to settle all field rules. Review with quality, maintenance, operations, and database roles. Known gaps: Plan vs maintenance-item grain; asset vs asset-class scope; interval type/unit/cycle anchor; checklist direction; calendar/counter sources; due generation and overdue handling; lifecycle vocabulary.

## S. Missing Collection Assessment

No collection is added. Inventory currently lacks a separately named characteristic/checklist-item/rule-band owner. If the same-collection flat row model is not accepted, revise inventory through change control; do not use arrays.

## T. Cross-Collection Consistency Check

- Inventory name/module/type: checked against v1.6.
- Flatness and singular field stems: PASS for defined fields.
- ID/display and audit pairs: PASS for defined references.
- Repeated characteristics, rule bands, and checklist items remain separately addressable; parent strategy OPEN.
- Numeric types, lifecycle values, effectivity, and relationship direction remain OPEN where stated.

## U. Field Completeness Metrics

See Sections E, F, and J for field, reference-pair and index counts. Detailed requirement coverage is not verified; no functional completeness percentage is claimed. Known blockers: Plan vs maintenance-item grain; asset vs asset-class scope; interval type/unit/cycle anchor; checklist direction; calendar/counter sources; due generation and overdue handling; lifecycle vocabulary.

## V. One Representative MongoDB Document

The sole illustrative flat structure appears in Section L. No additional examples or sample data are generated.

## W. Assumptions

- Inventory defines the current collection set and boundaries.
- DATA-001 requires separate flat records for repeated rules and checklist/inspection items.
- ID/name duplication is intentional strategic denormalization.
- APPROVED follows prior owner direction; open issues are not approvals.

## X. Open Issues

Plan vs maintenance-item grain; asset vs asset-class scope; interval type/unit/cycle anchor; checklist direction; calendar/counter sources; due generation and overdue handling; lifecycle vocabulary.

## Y. Research Sources

Collection-specific manufacturing references and the decision context they support are listed under Research basis below. They are not project schema authority.

## Z. Final Design Review

- **Status:** APPROVED per Project Owner direction; not production readiness.
- **Covered:** purpose, candidate field baseline, known references, flat-document constraints, research, and explicit gaps.
- **Remaining:** Section X. Parent/child and numeric-semantic blockers must be resolved before implementation or sample generation.
## Research basis

- [SAP Maintenance Plan](https://help.sap.com/docs/SAP_ERP/11825b10747e4ee4b91ecc1dba612536/617ac253d0a4b54ce10000000a174cb4.html) — Plans describe maintenance/inspection tasks, dates, scope and scheduling cycles for technical objects.
- [SAP Maintenance Task Lists](https://help.sap.com/doc/e2048712f0ab45e791e6d15ba5e20c68) — Maintenance task lists standardize recurring work sequences; functional benchmark only.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


