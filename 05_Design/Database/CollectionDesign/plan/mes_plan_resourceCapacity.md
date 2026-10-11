**FILE TYPE: PROJECT SPECIFIC**

# mes_plan_resourceCapacity — Collection Specification

**Document ID:** MES-COL-PLAN-resourceCapacity  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** plan  
**Collection type:** Configuration  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Defines available capacity for a separately identified planning-capacity target and effective time scope.

## B. Business definition

Resource Capacity records a capacity quantity/time window against a capacity target. Inventory explicitly forbids inferring Work Center as machine/resource; no capacity-target collection is currently listed.

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
| `capacityTargetType` | String | Yes (proposed) | Discriminator for capacity target domain. | Allowed types and ownership OPEN. | No | Candidate target type + target ID | Avoid conflating target entities. | Inventory; DEC-022 | Capacity lookup |
| `capacityTargetId` | String | Yes (proposed) | Identity of capacity target. | 16-digit MES ID only if a target owner collection is selected; owner OPEN. | Target ID/name pair owner unresolved. | Candidate target pair | Anchor capacity. | Inventory explicitly calls target separate | Schedule capacity |
| `capacityTargetName` | String | Yes (proposed) | Target display snapshot paired with target ID. | Owner and exact entity pair OPEN. | Target pair unresolved. | No index by default | Read capacity without lookup. | DATA-003 if target is entity | Scheduling display |
| `availableCapacity` | Numeric BSON type OPEN | Yes (proposed) | Capacity amount available in effective period. | Quantity/time basis, BSON type, unit and precision OPEN. | No | No index by default | Calculate feasible schedule. | Inventory; SAP capacity maintenance | Planning |
| `unitOfMeasureId` | String | Conditional | Capacity unit if quantity-based. | If used pair with unitOfMeasureName; time-unit domain may differ. | Yes: unitOfMeasureId + unitOfMeasureName -> mes_matl_unitOfMeasure | Candidate unitOfMeasureId | Interpret capacity amount. | DEC-022; DATA-003 | Capacity calculation |
| `unitOfMeasureName` | String | Conditional | Unit display snapshot. | Both UOM fields present or absent. | Yes: paired with unitOfMeasureId | No index by default | Readable capacity. | DATA-003 | Plan display |
| `effectiveFrom` | Date | Yes (proposed) | Capacity validity start. | BSON Date; <= effectiveTo. | No | Candidate target+effectiveFrom | Time-window capacity. | Inventory; SAP available capacity | Schedule feasibility |
| `effectiveTo` | Date | Conditional | Capacity validity end. | BSON Date >= effectiveFrom. | No | Candidate target+effectiveTo | Bound schedule window. | Inventory; SAP capacity | Planning |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `unitOfMeasureId` — Yes: unitOfMeasureId + unitOfMeasureName -> mes_matl_unitOfMeasure.
- `unitOfMeasureName` — Yes: paired with unitOfMeasureId.

## G. Validation rules

- Flat top-level scalar fields only; no nested documents or arrays.
- MES IDs are 16-digit strings; external identifiers remain separate.
- Enforce only approved code patterns and uniqueness scopes. Do not invent OPEN scopes or status vocabularies.
- Required ID/display pairs are complete and consistent; optional pairs are both absent or both populated.
- Audit actor pairs are complete; updatedAt >= createdAt.
- Retired reference records remain historically resolvable when established by decision.

## H. Status values

No lifecycle status proposed; capacity effectivity governs temporal validity unless decision requires otherwise.

## I. Relationships

**Outgoing:** Capacity target pair unresolved; optional UOM pair; calendar/shift scope not finalized.  
**Incoming:** Schedule and simulation collections.  
**Dependencies:** Blocking unknown capacity target identity. Work Center is not machine/resource. Calendar/shift association and unit convention remain OPEN.

## J. Recommended indexes

- Default unique _id index.
- Candidate target type + target ID — Capacity lookup; verify query frequency and selectivity before implementation.
- Candidate target pair — Schedule capacity; verify query frequency and selectivity before implementation.
- Candidate unitOfMeasureId — Capacity calculation; verify query frequency and selectivity before implementation.
- Candidate target+effectiveFrom — Schedule feasibility; verify query frequency and selectivity before implementation.
- Candidate target+effectiveTo — Planning; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  capacityTargetType: "<capacityTargetType>",
  capacityTargetId: "<capacityTargetId>",
  capacityTargetName: "<capacityTargetName>",
  availableCapacity: <value>,
  unitOfMeasureId: "<unitOfMeasureId>",
  unitOfMeasureName: "<unitOfMeasureName>",
  effectiveFrom: <value>,
  effectiveTo: <value>,
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

BLOCKER: target owner/type and ID/name pair; capacity dimension (labor/equipment/line/other); data type/unit; calendar/shift relationship; downtime exclusions; effective-window overlap and capacity aggregation.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: BLOCKED — target owner and reference cannot be finalized.
- Status vocabulary: N/A — no status field defined.
- Unresolved dependencies: see Section N; no silent resolution.


## P. Audit and Historical Data

Standard audit fields are flat and paired. Effective dates, assignment history, and lifecycle records must not be embedded. Retention and actor-source policies remain OPEN.

## Q. Real-World Manufacturing Exceptions

Do not infer workforce training eligibility, Tool availability, compatibility, or capacity from unrelated status fields. Invalid or unresolved targets remain visible for controlled reconciliation.

## R. Missing Field Assessment

Detailed requirements remain incomplete for this design. Review with workforce/tool-room/planning/maintenance and database roles. Known gaps: BLOCKER: target owner/type and ID/name pair; capacity dimension (labor/equipment/line/other); data type/unit; calendar/shift relationship; downtime exclusions; effective-window overlap and capacity aggregation.

## S. Missing Collection Assessment

No collection is added. Capacity target and training role/course owners are unresolved; do not reuse Work Center, Asset, or an external entity without approved identity ownership.

## T. Cross-Collection Consistency Check

- Inventory scope/type checked against v1.6.
- Defined references use singular ID/display pairs; Tool and Asset remain distinct under DEC-022.
- Repeated relationships are flat records.
- Resource-capacity target and certification/training taxonomies remain OPEN where identified.
- No cross-collection status vocabulary or numeric type is inferred.

## U. Field Completeness Metrics

See Sections E, F, and J for candidate fields, references, and indexes. Detailed scenario coverage is not established without collection-specific requirements. Known gaps: BLOCKER: target owner/type and ID/name pair; capacity dimension (labor/equipment/line/other); data type/unit; calendar/shift relationship; downtime exclusions; effective-window overlap and capacity aggregation.

## V. One Representative MongoDB Document

The single illustrative flat structure appears in Section L; no sample set is generated.

## W. Assumptions

- The collection inventory and approved ownership decisions control scope.
- DATA-001 requires separate flat records for repeated relationships.
- ID/name duplication is intentional strategic denormalization.
- APPROVED follows the prior owner instruction; open points remain unapproved.

## X. Open Issues

BLOCKER: target owner/type and ID/name pair; capacity dimension (labor/equipment/line/other); data type/unit; calendar/shift relationship; downtime exclusions; effective-window overlap and capacity aggregation.

## Y. Research Sources

Sources in the Research basis section below support the collection's operational use cases only; no vendor schema or status list is adopted.

## Z. Final Design Review

- **Status:** APPROVED under prior Project Owner instruction; not production readiness.
- **Covered:** bounded purpose, candidate fields, flat relationships, research, and unresolved scope.
- **Remaining:** Section X; capacity/identity taxonomy blockers must be resolved before implementation.
## Research basis

- [SAP Capacity Data Maintenance](https://help.sap.com/docs/SAP_S4HANA_ON-PREMISE/21aead0c98bd4755abdacd91c99e3393/4982bf53f106b44ce10000000a174cb4.html?locale=en-US&state=PRODUCTION&version=2025.001) — Capacity data is time-based or quantity-based and supports capacity planning/scheduling; informs unresolved target dimension.
- [SAP Defining Available Capacity](https://help.sap.com/docs/SAP_S4HANA_ON-PREMISE/f899ce30af9044299d573ea30b533f1c/3f34c95360267614e10000000a174cb4.html) — Available production capacity is calculated across periods/shifts and resource data; no resource model adopted.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


