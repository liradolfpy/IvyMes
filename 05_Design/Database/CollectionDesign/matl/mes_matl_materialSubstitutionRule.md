**FILE TYPE: PROJECT SPECIFIC**

# mes_matl_materialSubstitutionRule — Collection Specification

**Document ID:** MES-COL-MATL-materialSubstitutionRule  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** matl  
**Collection type:** Configuration  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Defines an authorized substitute Material and its applicable scope/effectivity.

## B. Business definition

A material-substitution rule permits a substitute Material to serve for an original Material within a stated scope. It does not make the two Material identities equivalent.

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
| `materialSubstitutionRuleCode` | String | Yes (proposed) | Business identifier for substitution rule. | Pattern/uniqueness OPEN. | No | Candidate code; uniqueness OPEN | Rule administration. | Inventory; SAP alternative parts | Rule selection |
| `originalMaterialId` | String | Yes | Material normally required. | 16-digit ID resolving mes_matl_material. | Yes: originalMaterialId + originalMaterialName -> mes_matl_material | Candidate originalMaterialId + status | Explicitly identify replaced material. | SAP alternative parts; DATA-003 | Substitution resolution |
| `originalMaterialName` | String | Yes | Original material display snapshot. | Paired with originalMaterialId. | Yes: paired with originalMaterialId | No index by default | Readable rule. | DATA-003 | Review |
| `substituteMaterialId` | String | Yes | Material permitted as substitute. | 16-digit ID resolving mes_matl_material; distinct from original. | Yes: substituteMaterialId + substituteMaterialName -> mes_matl_material | Candidate substituteMaterialId | Substitute identity. | SAP alternative parts; DEC-022 | Issue/consumption |
| `substituteMaterialName` | String | Yes | Substitute material display snapshot. | Paired with substituteMaterialId. | Yes: paired with substituteMaterialId | No index by default | Readable selection. | DATA-003 | Substitution UI |
| `productId` | String | Conditional (proposed) | Product scope if substitution is product-specific. | If present paired with productName; applicability open. | Yes: productId + productName -> mes_matl_product | Candidate scope lookup | Limit substitution to product context. | Inventory; DATA-003 | Order-specific rule |
| `productName` | String | Conditional | Product display snapshot. | Both fields present or absent. | Yes: paired with productId | No index by default | Readable scope. | DATA-003 | Rule display |
| `effectiveFrom` | Date | Conditional | Start of rule effectivity. | BSON Date; <= effectiveTo. | No | Candidate material+scope+effectiveFrom | Time-bound authorization. | SAP substitution/effectivity | Planning/issue |
| `effectiveTo` | Date | Conditional | End of rule effectivity. | BSON Date >= effectiveFrom. | No | Candidate effectivity | Retire substitution window. | SAP substitution/effectivity | Historical resolution |
| `status` | String | Yes (proposed) | Rule lifecycle/authorization state. | Values/transitions and approval boundary OPEN. | No | Candidate status filter | Control use of substitute. | Inventory; approval rules OPEN | Substitution authorization |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `originalMaterialId` — Yes: originalMaterialId + originalMaterialName -> mes_matl_material.
- `originalMaterialName` — Yes: paired with originalMaterialId.
- `substituteMaterialId` — Yes: substituteMaterialId + substituteMaterialName -> mes_matl_material.
- `substituteMaterialName` — Yes: paired with substituteMaterialId.
- `productId` — Yes: productId + productName -> mes_matl_product.
- `productName` — Yes: paired with productId.

## G. Validation rules

- Flat top-level scalar fields only; no nested documents or arrays.
- MES IDs are 16-digit strings; external identifiers remain separate.
- Enforce only approved code patterns and uniqueness scopes. Do not invent OPEN scopes or status vocabularies.
- Required ID/display pairs are complete and consistent; optional pairs are both absent or both populated.
- Audit actor pairs are complete; updatedAt >= createdAt.
- Retired reference records remain historically resolvable when established by decision.

## H. Status values

Status must indicate substitution-rule validity/authorization, not material lifecycle; values OPEN.

## I. Relationships

**Outgoing:** Original and substitute Material pairs; optional Product pair.  
**Incoming:** BOM, reservation, issue and consumption workflows may consult rules.  
**Dependencies:** mes_matl_material, optional mes_matl_product. Approval/quantity-equivalence and BOM scope semantics unresolved.

## J. Recommended indexes

- Default unique _id index.
- Candidate code; uniqueness OPEN — Rule selection; verify query frequency and selectivity before implementation.
- Candidate originalMaterialId + status — Substitution resolution; verify query frequency and selectivity before implementation.
- Candidate substituteMaterialId — Issue/consumption; verify query frequency and selectivity before implementation.
- Candidate scope lookup — Order-specific rule; verify query frequency and selectivity before implementation.
- Candidate material+scope+effectiveFrom — Planning/issue; verify query frequency and selectivity before implementation.
- Candidate effectivity — Historical resolution; verify query frequency and selectivity before implementation.
- Candidate status filter — Substitution authorization; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  materialSubstitutionRuleCode: "<materialSubstitutionRuleCode>",
  originalMaterialId: "<originalMaterialId>",
  originalMaterialName: "<originalMaterialName>",
  substituteMaterialId: "<substituteMaterialId>",
  substituteMaterialName: "<substituteMaterialName>",
  productId: "<productId>",
  productName: "<productName>",
  effectiveFrom: <value>,
  effectiveTo: <value>,
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

Rule priority, conflict resolution, product/BOM scope, quantity conversion/equivalence, approval workflow, effective-date overlap, and whether substitute set is one rule per pair or a group.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: PASS — entity references have singular ID/name pairs.
- Status vocabulary: OPEN — approval and rule status semantics unresolved.
- Unresolved dependencies: see Section N; no silent resolution.


## P. Audit and Historical Data

Standard audit fields remain flat and paired. Preserve revision/effectivity and historical references according to approved project rules; retention and immutable-history policies remain OPEN.

## Q. Real-World Manufacturing Exceptions

Do not silently substitute a Product or Material, infer a quantity conversion, or accept ambiguous/overlapping revisions. Record correction/reconciliation only through approved fields and workflows.

## R. Missing Field Assessment

Collection-specific requirement artifacts are incomplete for this wave. Validate the field set with engineering, material control, quality, and database roles. Known gaps: Rule priority, conflict resolution, product/BOM scope, quantity conversion/equivalence, approval workflow, effective-date overlap, and whether substitute set is one rule per pair or a group.

## S. Missing Collection Assessment

No collection is added. Repeated composition lines use mes_matl_bomComponent. Repeated quality characteristic ownership remains OPEN and must be resolved under inventory/change control; do not embed arrays.

## T. Cross-Collection Consistency Check

- Inventory scope/type: checked against v1.6.
- Product vs Material distinction: consistent with DEC-022.
- Flat representation: no embedded component/characteristic arrays.
- Defined entity references use required ID/name pairs and singular field stems.
- Numeric types, effectivity, status, and missing ownership issues remain OPEN where stated.

## U. Field Completeness Metrics

Field counts and requiredness are in Section E; ID/name references in Section F; candidate indexes in Section J. Detailed collection requirements are unavailable, so no complete-scenario rate is claimed. Known gaps: Rule priority, conflict resolution, product/BOM scope, quantity conversion/equivalence, approval workflow, effective-date overlap, and whether substitute set is one rule per pair or a group.

## V. One Representative MongoDB Document

The only illustrative structure appears in Section L. No second example or 10-document set is generated.

## W. Assumptions

- Inventory and current decisions govern scope.
- DATA-001 requires repeated concepts as separate flat records.
- ID/name duplication is intentional strategic denormalization.
- APPROVED follows prior owner instruction; OPEN issues are not approved decisions.

## X. Open Issues

Rule priority, conflict resolution, product/BOM scope, quantity conversion/equivalence, approval workflow, effective-date overlap, and whether substitute set is one rule per pair or a group.

## Y. Research Sources

Collection-specific sources and their relevance are recorded under Research basis below. They support manufacturing scenarios, not project-specific schema or status values.

## Z. Final Design Review

- **Status:** APPROVED per Project Owner direction; not production readiness.
- **Covered:** bounded purpose, flat record proposal, known references, public research, and explicit gaps.
- **Remaining:** Section X; numeric and structural blockers must be resolved before implementation/sample generation.
## Research basis

- [SAP Using Alternative Parts](https://help.sap.com/docs/PRODUCT_ID/48b24fbadbee4e0b9dae0e2b72762e68/fe45da5eb0ee4dc3a9bf1824bd188dfd.html) — Alternative components can be grouped and governed by priority, strategy, and usage probability; used to identify conflict-resolution decisions.
- [SAP Production Order Components](https://help.sap.com/docs/SAP_S4HANA_CLOUD/d35113ee62644d3abee1aaec148291d9/a16470381b5e4d0095ff76f35f6dfc82.html) — Shows alternative item and alternative group concepts in production order components; no values copied.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


