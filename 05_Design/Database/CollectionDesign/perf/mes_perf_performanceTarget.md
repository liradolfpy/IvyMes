**FILE TYPE: PROJECT SPECIFIC**

# mes_perf_performanceTarget — Collection Specification

**Document ID:** MES-COL-PERF-performanceTarget  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** perf  
**Collection type:** Configuration  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Defines target rates or quantities for a product, operation, work center, shift/calendar, or time scope.

## B. Business definition

Performance Target provides an approved comparison basis for actual performance. The target grain, scope combination, unit and versioning are not yet specified by project requirements.

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
| `performanceTargetCode` | String | Yes (proposed) | Business identifier for target definition. | Pattern/uniqueness OPEN. | No | Candidate code+scope | Manage target definitions. | Inventory; SAP Performance KPI | Target lookup |
| `performanceTargetName` | String | Yes | Readable target name. | Non-empty. | No | Candidate text search | Dashboard/planner display. | DATA-003 | Performance review |
| `productId` | String | Conditional (proposed) | Product scope. | If present paired with productName. | Yes: productId + productName -> mes_matl_product | Candidate scope lookup | Product-specific target. | Inventory; SAP Performance KPI | Target comparison |
| `productName` | String | Conditional | Product display snapshot. | Both fields present or absent. | Yes: paired with productId | No index by default | Readable scope. | DATA-003 | Dashboard |
| `operationId` | String | Conditional (proposed) | Operation scope. | If present paired with operationName. | Yes: operationId + operationName -> mes_prod_operation | Candidate scope lookup | Operation-specific target. | Inventory; DEC-003 | Rate comparison |
| `operationName` | String | Conditional | Operation display snapshot. | Both fields present or absent. | Yes: paired with operationId | No index by default | Readable scope. | DATA-003 | Dashboard |
| `workCenterId` | String | Conditional (proposed) | Work-center scope if target is organizationally scoped. | If present paired with workCenterName; not a machine/resource identity. | Yes: workCenterId + workCenterName -> mes_plan_workCenter | Candidate scope lookup | Operational comparison scope. | DEC-022; inventory | Shift/area comparison |
| `workCenterName` | String | Conditional | Work-center display snapshot. | Both fields present or absent. | Yes: paired with workCenterId | No index by default | Readable context. | DATA-003 | Dashboard |
| `targetValue` | Numeric BSON type OPEN | Yes (proposed) | Target quantity/rate value. | Type, units, scale, denominator and rounding OPEN. | No | No index by default | Compare actual vs target. | Inventory; SAP Performance KPI | Target comparison |
| `effectiveFrom` | Date | Conditional | Target applicability start. | BSON Date; scope-overlap rules OPEN. | No | Candidate scope+effectiveFrom | Select valid target. | Inventory | Historical comparison |
| `effectiveTo` | Date | Conditional | Target applicability end. | BSON Date >= effectiveFrom. | No | Candidate effective window | Version target applicability. | Inventory | Target selection |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `productId` — Yes: productId + productName -> mes_matl_product.
- `productName` — Yes: paired with productId.
- `operationId` — Yes: operationId + operationName -> mes_prod_operation.
- `operationName` — Yes: paired with operationId.
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

No status field proposed; effectivity may govern applicability. Approval state unresolved.

## I. Relationships

**Outgoing:** Optional Product, Operation and Work Center pairs; shift/calendar scope not finalized.  
**Incoming:** Performance results and KPI results.  
**Dependencies:** Product, Operation, Work Center; resource/target ownership, shift/calendar scopes and calculation basis OPEN.

## J. Recommended indexes

- Default unique _id index.
- Candidate code+scope — Target lookup; verify query frequency and selectivity before implementation.
- Candidate text search — Performance review; verify query frequency and selectivity before implementation.
- Candidate scope lookup — Target comparison; verify query frequency and selectivity before implementation.
- Candidate scope lookup — Rate comparison; verify query frequency and selectivity before implementation.
- Candidate scope lookup — Shift/area comparison; verify query frequency and selectivity before implementation.
- Candidate scope+effectiveFrom — Historical comparison; verify query frequency and selectivity before implementation.
- Candidate effective window — Target selection; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  performanceTargetCode: "<performanceTargetCode>",
  performanceTargetName: "<performanceTargetName>",
  productId: "<productId>",
  productName: "<productName>",
  operationId: "<operationId>",
  operationName: "<operationName>",
  workCenterId: "<workCenterId>",
  workCenterName: "<workCenterName>",
  targetValue: <value>,
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

Target grain and scope precedence; rate/quantity unit and numeric type; target source vs manual; effectivity/versioning; relation to operation standard rate; work-center is not resource; status/approval.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: OPEN — scope and target dimension are unresolved.
- Status vocabulary: N/A — no status field defined.
- Unresolved dependencies: see Section N; no silent resolution.


## P. Audit and Historical Data

Standard audit fields remain flat and paired. Preserve revisions, effectivity, and historical target/label/transition context according to future approved rules. Retention remains OPEN.

## Q. Real-World Manufacturing Exceptions

Do not infer capacity targets, changeover units, KPI target values, or external label-rendering behavior. Preserve the source context needed for correction only when supported by approved fields.

## R. Missing Field Assessment

Collection-specific requirements are incomplete. Review with planning, production, traceability, performance, and data-architecture roles. Known gaps: Target grain and scope precedence; rate/quantity unit and numeric type; target source vs manual; effectivity/versioning; relation to operation standard rate; work-center is not resource; status/approval.

## S. Missing Collection Assessment

No collection is added. Any repeated transition/label content dimensions must be represented as separate records in an identified owner collection or returned for inventory review; no arrays are permitted.

## T. Cross-Collection Consistency Check

- Inventory identity/module/type: checked against v1.6.
- Defined references use singular ID/name pairs.
- Flat-document convention is respected.
- Numeric types, external ownership, scope precedence, and lifecycle rules remain OPEN where stated.
- Work Center is not used as a resource identity.

## U. Field Completeness Metrics

See Sections E, F, and J for field, relationship, and index baselines. Detailed source requirements are not available; no completeness percentage is claimed. Known gaps: Target grain and scope precedence; rate/quantity unit and numeric type; target source vs manual; effectivity/versioning; relation to operation standard rate; work-center is not resource; status/approval.

## V. One Representative MongoDB Document

The single illustrative flat structure is in Section L. No additional example documents are generated.

## W. Assumptions

- Current inventory and approved ownership decisions determine scope.
- ID/name duplication is intentional strategic denormalization.
- Repeated concepts are separate records.
- APPROVED is the requested status, not closure of open issues.

## X. Open Issues

Target grain and scope precedence; rate/quantity unit and numeric type; target source vs manual; effectivity/versioning; relation to operation standard rate; work-center is not resource; status/approval.

## Y. Research Sources

See Research basis below for collection-specific public sources and the supported operational scenarios. They do not establish project values or schema.

## Z. Final Design Review

- **Status:** APPROVED under the prior Project Owner instruction; not production readiness.
- **Covered:** bounded purpose, candidate field set, references, research, and explicit dependencies.
- **Remaining:** Section X; target/content semantic blockers must be resolved before implementation.
## Research basis

- [SAP Digital Manufacturing Performance KPI](https://help.sap.com/docs/sap-digital-manufacturing/insights/performance-kpi?version=latest) — Performance KPI compares actual production speed to standard production speed and depends on selected resources/material/operation context.
- [SAP Item/Product Changeover Time KPI](https://help.sap.com/docs/PRODUCT_ID/45e4eda3753c458ead20c81f77c55921/d0b1865342bc3d58e10000000a174cb4.html) — Distinguishes planned and actual changeover time; supports target-vs-actual context.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


