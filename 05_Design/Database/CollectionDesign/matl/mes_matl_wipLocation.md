**FILE TYPE: PROJECT SPECIFIC**

# mes_matl_wipLocation — Collection Specification

**Document ID:** MES-COL-MATL-wipLocation  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** matl  
**Collection type:** Master  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Defines controlled locations/areas where work-in-process can be tracked during manufacturing.

## B. Business definition

WIP Location is a material-flow locator for in-process inventory, distinct from machine/work center, material balance, and movement transaction.

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
| `wipLocationCode` | String | Yes (proposed) | Identifier for WIP tracking location. | Pattern/uniqueness OPEN. | No | Candidate lookup; uniqueness OPEN | Locate WIP. | Inventory; SAP WIP storage | WIP movement |
| `wipLocationName` | String | Yes | Readable WIP location name. | Non-empty. | No | Candidate text search | Operator/controller display. | DATA-003 | WIP tracking |
| `plantId` | String | Yes (proposed) | Plant containing WIP location. | 16-digit ID paired with plantName. | Yes: plantId + plantName -> mes_plan_plant | Candidate plant filter | Organizational scope. | DEC-022; SAP WIP storage | Material tracking |
| `plantName` | String | Yes (proposed) | Plant display snapshot. | Paired with plantId. | Yes: paired with plantId | No index by default | Avoid display lookup. | DATA-003 | Location display |
| `workCenterId` | String | Conditional (proposed) | Work-center context for WIP staging if approved. | If present paired with workCenterName; never implies machine/resource relation. | Yes: workCenterId + workCenterName -> mes_plan_workCenter | Candidate operational filter | Staging context. | Inventory; DEC-022 | WIP staging |
| `workCenterName` | String | Conditional | Work-center display snapshot. | Both fields present or absent. | Yes: paired with workCenterId | No index by default | Readable context. | DATA-003 | Staging display |
| `status` | String | Yes (proposed) | WIP-location lifecycle/availability. | Vocabulary and movement restrictions OPEN. | No | Candidate status filter | Prevent use of unavailable location. | Lifecycle rules OPEN | Location selection |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `plantId` — Yes: plantId + plantName -> mes_plan_plant.
- `plantName` — Yes: paired with plantId.
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

Location lifecycle/availability, not stock status or inventory hold. Vocabulary OPEN.

## I. Relationships

**Outgoing:** Plant pair and conditional work-center pair.  
**Incoming:** Inventory balance, WIP/material movement, reservation, production execution and traceability consumers.  
**Dependencies:** Plant confirmed. Work-center context is not a machine/resource relation. Location hierarchy and WMS ownership OPEN.

## J. Recommended indexes

- Default unique _id index.
- Candidate lookup; uniqueness OPEN — WIP movement; verify query frequency and selectivity before implementation.
- Candidate text search — WIP tracking; verify query frequency and selectivity before implementation.
- Candidate plant filter — Material tracking; verify query frequency and selectivity before implementation.
- Candidate operational filter — WIP staging; verify query frequency and selectivity before implementation.
- Candidate status filter — Location selection; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  wipLocationCode: "<wipLocationCode>",
  wipLocationName: "<wipLocationName>",
  plantId: "<plantId>",
  plantName: "<plantName>",
  workCenterId: "<workCenterId>",
  workCenterName: "<workCenterName>",
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

Code namespace; WMS/storage master; work-center scope; hierarchy/type/capacity; locator vs entity semantics; status values.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: PASS — defined plant/work-center pairs use required ID/name convention.
- Status vocabulary: OPEN — values not approved.
- Unresolved dependencies: see Section N; no silent resolution.


## P. Audit and Historical Data

Standard audit fields are flat: createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity source, retention period, and immutable-history requirements remain OPEN unless an approved project rule states otherwise. Lifecycle or location history must not be embedded as arrays.

## Q. Real-World Manufacturing Exceptions

Handle retired, unmapped, or unavailable references without silently substituting a different identity. Store only scalar exception context already represented in the field dictionary. Detailed correction, retry, and reconciliation behavior remains OPEN where applicable.

## R. Missing Field Assessment

The current project copy does not provide collection-specific functional requirement artifacts for this item. Potential missing fields must be evaluated with operations, planning, engineering, quality, integration, maintenance, material, and database roles before implementation. Known assessment items: Code namespace; WMS/storage master; work-center scope; hierarchy/type/capacity; locator vs entity semantics; status values.

## S. Missing Collection Assessment

No collection is added by this specification. The Collection Inventory remains authoritative. If an unresolved parent, interval, classification, location, or external-system owner requires another collection, report it for inventory review; do not embed the repeated concept.

## T. Cross-Collection Consistency Check

- Inventory name, module, type, and purpose: checked against Collection Inventory v1.6.
- Flat document and singular field stems: PASS for defined fields.
- Project ID and audit conventions: PASS.
- Entity ID/display pairs: see Section F; unresolved owners/cardinality are OPEN.
- Types: follow current conventions for defined fields.
- Status and business-number rules: remain OPEN where not explicitly decided.
- References and dependencies: see Sections I and N; no foreign-key enforcement is assumed.

## U. Field Completeness Metrics

Metrics are provisional because detailed collection requirements and the full Stage 2 research artifacts are unavailable in this active project copy.

- Proposed fields: see Section E.
- Required / conditional / optional: see Section E; proposed requiredness is not treated as a confirmed business rule.
- ID–Name pairs: see Section F.
- Status/control fields: collection-specific field dictionary.
- Audit fields: 6.
- External fields: see Section E.
- Scenario coverage: no percentage claimed until detailed requirements are linked.
- Research sources: see Section Y.
- Unresolved gaps: Code namespace; WMS/storage master; work-center scope; hierarchy/type/capacity; locator vs entity semantics; status values.

## V. One Representative MongoDB Document

The single illustrative flat document structure is provided in Section L. No second document or sample-data set is added here.

## W. Assumptions

- The inventory determines collection identity, module, type, and bounded purpose.
- DATA-001 requires separate flat records for repeated concepts.
- DATA-003 ID/display duplication is intentional strategic denormalization.
- APPROVED is the user's requested specification status; OPEN questions remain unapproved and unresolved.

## X. Open Issues

Code namespace; WMS/storage master; work-center scope; hierarchy/type/capacity; locator vs entity semantics; status values.

## Y. Research Sources

The collection-specific public research references and their supported design context are in the Research basis section immediately below. They are functional benchmarks only and do not establish this project's schema or value lists.

## Z. Final Design Review

- **Status:** APPROVED under the Project Owner's prior instruction; not equivalent to production readiness.
- **Covered:** inventory-bounded purpose, candidate field baseline, flat-document constraint, known references, research context, and explicit unresolved questions.
- **Remaining:** Section X and any unresolved field-level validation or value rules.
- **Readiness:** reviewable collection-design baseline. Do not implement a blocker-marked schema until its semantic blocker is resolved.
## Research basis

- [SAP Digital Manufacturing — EWM WIP Storage](https://help.sap.com/docs/SAP_DIGITAL_MANUFACTURING/execution/afb2cef56b034488beb7f0828853d5e4.html) — Describes temporary WIP storage when next operation is delayed or shop-floor space is insufficient.
- [SAP S/4HANA Storage Location](https://help.sap.com/docs/SAP_S4HANA_CLOUD/f86dc2eb1f8b48c880a7607213104b27/a914c453f57eb44ce10000000a174cb4.html) — Defines a storage location as an organizational unit that differentiates product stocks within a plant; domain benchmark only.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


