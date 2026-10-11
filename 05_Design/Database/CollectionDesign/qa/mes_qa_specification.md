**FILE TYPE: PROJECT SPECIFIC**

# mes_qa_specification — Collection Specification

**Document ID:** MES-COL-QA-specification  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** qa  
**Collection type:** Configuration  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Controls revisioned product/process specification and tolerance definitions for inspection and conformance decisions.

## B. Business definition

A Quality Specification stores a controlled requirement applicable to Product/Material or process context. The inventory does not identify a separate repeated characteristic entity owner; per DATA-001 each characteristic requirement needs a separate record or an approved owner decision.

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
| `specificationCode` | String | Yes (proposed) | Business identifier for quality specification. | Pattern/uniqueness scope OPEN. | No | Candidate code+revision; uniqueness OPEN | Select controlled requirements. | Inventory; SAP Inspection Planning | Quality plan selection |
| `specificationName` | String | Yes | Readable specification name. | Non-empty. | No | Candidate text search | Quality display. | DATA-003; SAP inspection planning | Inspector selection |
| `specificationRevision` | String | Yes (proposed) | Revision identifier. | Format/effectivity uniqueness OPEN. | No | Candidate code+revision | Version control. | Inventory; SAP inspection planning | Revision selection |
| `productId` | String | Conditional (proposed) | Product applicability. | If present paired with productName. | Yes: productId + productName -> mes_matl_product | Candidate product filter | Product-specific criteria. | DATA-003; SAP QM | Inspection applicability |
| `productName` | String | Conditional | Product display snapshot. | Both fields present or absent. | Yes: paired with productId | No index by default | Readable scope. | DATA-003 | Quality display |
| `materialId` | String | Conditional (proposed) | Material applicability when requirements target a Material identity. | If present paired with materialName; Product-vs-Material scope rules OPEN. | Yes: materialId + materialName -> mes_matl_material | Candidate material filter | Material-specific criteria. | Inventory; DEC-022 | Incoming/material inspection |
| `materialName` | String | Conditional | Material display snapshot. | Both fields present or absent. | Yes: paired with materialId | No index by default | Readable scope. | DATA-003 | Quality display |
| `characteristicCode` | String | Yes (proposed per flat characteristic row) | Identifier for one inspection characteristic requirement row. | Characteristic identity/sequence uniqueness OPEN; no arrays. | No separate characteristic master identified. | Candidate parent+characteristicCode | Represent a characteristic as a separate flat record. | DATA-001; SAP inspection characteristics | Measurement/acceptance |
| `characteristicName` | String | Yes | Display name for characteristic row. | Non-empty. | No separate characteristic master identified. | No index by default | Inspector display. | SAP Inspection Planning | Inspection |
| `lowerLimit` | Numeric BSON type OPEN | Conditional | Lower acceptance bound for quantitative characteristic. | Numeric type, unit, precision, and inclusive rules OPEN. | No | No index by default | Conformance decision. | Inventory; SAP QM | Measurement evaluation |
| `upperLimit` | Numeric BSON type OPEN | Conditional | Upper acceptance bound. | Numeric type, unit, precision, and inclusive rules OPEN. | No | No index by default | Conformance decision. | Inventory; SAP QM | Measurement evaluation |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `productId` — Yes: productId + productName -> mes_matl_product.
- `productName` — Yes: paired with productId.
- `materialId` — Yes: materialId + materialName -> mes_matl_material.
- `materialName` — Yes: paired with materialId.

## G. Validation rules

- Flat top-level scalar fields only; no nested documents or arrays.
- MES IDs are 16-digit strings; external identifiers remain separate.
- Enforce only approved code patterns and uniqueness scopes. Do not invent OPEN scopes or status vocabularies.
- Required ID/display pairs are complete and consistent; optional pairs are both absent or both populated.
- Audit actor pairs are complete; updatedAt >= createdAt.
- Retired reference records remain historically resolvable when established by decision.

## H. Status values

Lifecycle/effectivity vocabulary OPEN.

## I. Relationships

**Outgoing:** Optional Product/Material pairs. Repeated characteristic record grain and parent identity are unresolved.  
**Incoming:** Inspection plan/request/results and deviation workflows.  
**Dependencies:** Product, Material and inspection plans. Dependency map flags repeated inspection characteristics as owner/parent unresolved.

## J. Recommended indexes

- Default unique _id index.
- Candidate code+revision; uniqueness OPEN — Quality plan selection; verify query frequency and selectivity before implementation.
- Candidate text search — Inspector selection; verify query frequency and selectivity before implementation.
- Candidate code+revision — Revision selection; verify query frequency and selectivity before implementation.
- Candidate product filter — Inspection applicability; verify query frequency and selectivity before implementation.
- Candidate material filter — Incoming/material inspection; verify query frequency and selectivity before implementation.
- Candidate parent+characteristicCode — Measurement/acceptance; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  specificationCode: "<specificationCode>",
  specificationName: "<specificationName>",
  specificationRevision: "<specificationRevision>",
  productId: "<productId>",
  productName: "<productName>",
  materialId: "<materialId>",
  materialName: "<materialName>",
  characteristicCode: "<characteristicCode>",
  characteristicName: "<characteristicName>",
  lowerLimit: <value>,
  upperLimit: <value>,
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

BLOCKER: identify parent grouping record/identity for separate characteristic rows; define applicability to Product vs Material; numeric type/precision/unit and tolerance inclusivity; revisions/effectivity; qualitative values; approval and status rules.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: OPEN — repeated characteristic parent/child owner unresolved.
- Status vocabulary: OPEN — values not approved.
- Unresolved dependencies: see Section N; no silent resolution.


## P. Audit and Historical Data

Standard audit fields remain flat and paired. Preserve revision/effectivity and historical references according to approved project rules; retention and immutable-history policies remain OPEN.

## Q. Real-World Manufacturing Exceptions

Do not silently substitute a Product or Material, infer a quantity conversion, or accept ambiguous/overlapping revisions. Record correction/reconciliation only through approved fields and workflows.

## R. Missing Field Assessment

Collection-specific requirement artifacts are incomplete for this wave. Validate the field set with engineering, material control, quality, and database roles. Known gaps: BLOCKER: identify parent grouping record/identity for separate characteristic rows; define applicability to Product vs Material; numeric type/precision/unit and tolerance inclusivity; revisions/effectivity; qualitative values; approval and status rules.

## S. Missing Collection Assessment

No collection is added. Repeated composition lines use mes_matl_bomComponent. Repeated quality characteristic ownership remains OPEN and must be resolved under inventory/change control; do not embed arrays.

## T. Cross-Collection Consistency Check

- Inventory scope/type: checked against v1.6.
- Product vs Material distinction: consistent with DEC-022.
- Flat representation: no embedded component/characteristic arrays.
- Defined entity references use required ID/name pairs and singular field stems.
- Numeric types, effectivity, status, and missing ownership issues remain OPEN where stated.

## U. Field Completeness Metrics

Field counts and requiredness are in Section E; ID/name references in Section F; candidate indexes in Section J. Detailed collection requirements are unavailable, so no complete-scenario rate is claimed. Known gaps: BLOCKER: identify parent grouping record/identity for separate characteristic rows; define applicability to Product vs Material; numeric type/precision/unit and tolerance inclusivity; revisions/effectivity; qualitative values; approval and status rules.

## V. One Representative MongoDB Document

The only illustrative structure appears in Section L. No second example or 10-document set is generated.

## W. Assumptions

- Inventory and current decisions govern scope.
- DATA-001 requires repeated concepts as separate flat records.
- ID/name duplication is intentional strategic denormalization.
- APPROVED follows prior owner instruction; OPEN issues are not approved decisions.

## X. Open Issues

BLOCKER: identify parent grouping record/identity for separate characteristic rows; define applicability to Product vs Material; numeric type/precision/unit and tolerance inclusivity; revisions/effectivity; qualitative values; approval and status rules.

## Y. Research Sources

Collection-specific sources and their relevance are recorded under Research basis below. They support manufacturing scenarios, not project-specific schema or status values.

## Z. Final Design Review

- **Status:** APPROVED per Project Owner direction; not production readiness.
- **Covered:** bounded purpose, flat record proposal, known references, public research, and explicit gaps.
- **Remaining:** Section X; numeric and structural blockers must be resolved before implementation/sample generation.
## Research basis

- [SAP Inspection Planning with Inspection Plans](https://help.sap.com/docs/SAP_ERP/250374f0514e4e0f9057066374265eba/f65cbd534f22b44ce10000000a174cb4.html?locale=en-US&state=PRODUCTION&version=6.17.latest) — Inspection plans use multiple operations and characteristics with quantitative/qualitative requirements; repeated criteria need explicit modeling.
- [SAP Inspection Characteristics and Sampling](https://help.sap.com/docs/SAP_S4HANA_ON-PREMISE/0533a65ea373484ab21123dfa482ec37/f711b753128eb44ce10000000a174cb4.html) — Individual characteristics and methods describe the inspection; supports separate characteristic requirements.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


