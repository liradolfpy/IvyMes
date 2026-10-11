**FILE TYPE: PROJECT SPECIFIC**

# mes_qa_inspectionPlan — Collection Specification

**Document ID:** MES-COL-QA-inspectionPlan  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** qa  
**Collection type:** Configuration  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Defines revisioned inspection scope, characteristics, applicability, and execution requirements.

## B. Business definition

Inspection Plan controls what is inspected and how. Repeated characteristics must be separate flat records; the current inventory/dependency map flags characteristic ownership and parent-reference strategy as unresolved.

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
| `inspectionPlanCode` | String | Yes (proposed) | Business identifier for plan definition. | Pattern/uniqueness OPEN. | No | Candidate code+revision | Select inspection definition. | Inventory; SAP Inspection Planning | Inspection request creation |
| `inspectionPlanName` | String | Yes | Readable plan name. | Non-empty. | No | Candidate text search | Inspector display. | DATA-003 | Inspection selection |
| `inspectionPlanRevision` | String | Yes (proposed) | Controlled plan revision. | Format/effectivity OPEN. | No | Candidate code+revision | Version control. | Inventory; SAP QM plan | Revision selection |
| `productId` | String | Conditional (proposed) | Product applicability. | If present paired with productName. | Yes: productId + productName -> mes_matl_product | Candidate applicability | Scope inspection. | DATA-003; SAP QM | Product inspection |
| `productName` | String | Conditional | Product display snapshot. | Both fields present or absent. | Yes: paired with productId | No index by default | Readable scope. | DATA-003 | Inspection display |
| `materialId` | String | Conditional (proposed) | Material applicability. | If present paired with materialName; Product/Material scope OPEN. | Yes: materialId + materialName -> mes_matl_material | Candidate applicability | Incoming/material inspection. | DEC-022; inventory | Material inspection |
| `materialName` | String | Conditional | Material display snapshot. | Both fields present or absent. | Yes: paired with materialId | No index by default | Readable scope. | DATA-003 | Inspection display |
| `samplingPlanId` | String | Conditional (proposed) | Sampling Plan reference if that definition is selected. | If present pair with samplingPlanName; ownership direction OPEN. | Yes: samplingPlanId + samplingPlanName -> mes_qa_samplingPlan | Candidate samplingPlanId | Determine sample requirements. | Inventory; SAP QM | Sampling |
| `samplingPlanName` | String | Conditional | Sampling plan display snapshot. | Both fields present or absent. | Yes: paired with samplingPlanId | No index by default | Readable reference. | DATA-003 | Inspection planning |
| `characteristicCode` | String | Proposed per separate characteristic record | Inspection characteristic identity for one flat record. | Parent record grain and uniqueness OPEN; no arrays. | No characteristic master identified. | Candidate parent+sequence/code | Represent repeated criteria separately. | DATA-001; SAP QM characteristics | Results recording |
| `characteristicName` | String | Proposed | Readable inspection characteristic label. | Non-empty; reference ownership OPEN. | No characteristic master identified. | No index by default | Inspector display. | SAP inspection planning | Inspection |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `productId` — Yes: productId + productName -> mes_matl_product.
- `productName` — Yes: paired with productId.
- `materialId` — Yes: materialId + materialName -> mes_matl_material.
- `materialName` — Yes: paired with materialId.
- `samplingPlanId` — Yes: samplingPlanId + samplingPlanName -> mes_qa_samplingPlan.
- `samplingPlanName` — Yes: paired with samplingPlanId.

## G. Validation rules

- Flat top-level scalar fields only; no nested documents or arrays.
- MES IDs are 16-digit strings; external identifiers remain separate.
- Enforce only approved code patterns and uniqueness scopes. Do not invent OPEN scopes or status vocabularies.
- Required ID/display pairs are complete and consistent; optional pairs are both absent or both populated.
- Audit actor pairs are complete; updatedAt >= createdAt.
- Retired reference records remain historically resolvable when established by decision.

## H. Status values

Plan lifecycle vocabulary and transition rules OPEN.

## I. Relationships

**Outgoing:** Optional Product, Material and Sampling Plan pairs; repeated characteristic row grouping unresolved.  
**Incoming:** Inspection Request/Result and quality-hold workflows.  
**Dependencies:** Specification and sampling-plan semantics; operation reference may be relevant but not yet resolved in this baseline.

## J. Recommended indexes

- Default unique _id index.
- Candidate code+revision — Inspection request creation; verify query frequency and selectivity before implementation.
- Candidate text search — Inspection selection; verify query frequency and selectivity before implementation.
- Candidate code+revision — Revision selection; verify query frequency and selectivity before implementation.
- Candidate applicability — Product inspection; verify query frequency and selectivity before implementation.
- Candidate applicability — Material inspection; verify query frequency and selectivity before implementation.
- Candidate samplingPlanId — Sampling; verify query frequency and selectivity before implementation.
- Candidate parent+sequence/code — Results recording; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  inspectionPlanCode: "<inspectionPlanCode>",
  inspectionPlanName: "<inspectionPlanName>",
  inspectionPlanRevision: "<inspectionPlanRevision>",
  productId: "<productId>",
  productName: "<productName>",
  materialId: "<materialId>",
  materialName: "<materialName>",
  samplingPlanId: "<samplingPlanId>",
  samplingPlanName: "<samplingPlanName>",
  characteristicCode: "<characteristicCode>",
  characteristicName: "<characteristicName>",
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

BLOCKER: define characteristic parent/line model; Product vs Material applicability; inspection operation scope; samplingPlan/specification direction; criterion fields/data types; revision/effectivity, status/approval rules.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: OPEN — characteristic owner and dependency direction unresolved.
- Status vocabulary: OPEN — values not approved.
- Unresolved dependencies: see Section N; no silent resolution.


## P. Audit and Historical Data

Standard audit fields are flat and paired. Preserve revisions and effective dates without embedded history lists. Actor-source, retention, immutable revision, and approval evidence rules remain OPEN.

## Q. Real-World Manufacturing Exceptions

Do not infer sample sizes, acceptance thresholds, maintenance frequencies, checklist outcomes, or value vocabularies. Keep invalid or obsolete references identifiable for controlled correction.

## R. Missing Field Assessment

Collection-specific functional requirements are not sufficient to settle all field rules. Review with quality, maintenance, operations, and database roles. Known gaps: BLOCKER: define characteristic parent/line model; Product vs Material applicability; inspection operation scope; samplingPlan/specification direction; criterion fields/data types; revision/effectivity, status/approval rules.

## S. Missing Collection Assessment

No collection is added. Inventory currently lacks a separately named characteristic/checklist-item/rule-band owner. If the same-collection flat row model is not accepted, revise inventory through change control; do not use arrays.

## T. Cross-Collection Consistency Check

- Inventory name/module/type: checked against v1.6.
- Flatness and singular field stems: PASS for defined fields.
- ID/display and audit pairs: PASS for defined references.
- Repeated characteristics, rule bands, and checklist items remain separately addressable; parent strategy OPEN.
- Numeric types, lifecycle values, effectivity, and relationship direction remain OPEN where stated.

## U. Field Completeness Metrics

See Sections E, F, and J for field, reference-pair and index counts. Detailed requirement coverage is not verified; no functional completeness percentage is claimed. Known blockers: BLOCKER: define characteristic parent/line model; Product vs Material applicability; inspection operation scope; samplingPlan/specification direction; criterion fields/data types; revision/effectivity, status/approval rules.

## V. One Representative MongoDB Document

The sole illustrative flat structure appears in Section L. No additional examples or sample data are generated.

## W. Assumptions

- Inventory defines the current collection set and boundaries.
- DATA-001 requires separate flat records for repeated rules and checklist/inspection items.
- ID/name duplication is intentional strategic denormalization.
- APPROVED follows prior owner direction; open issues are not approvals.

## X. Open Issues

BLOCKER: define characteristic parent/line model; Product vs Material applicability; inspection operation scope; samplingPlan/specification direction; criterion fields/data types; revision/effectivity, status/approval rules.

## Y. Research Sources

Collection-specific manufacturing references and the decision context they support are listed under Research basis below. They are not project schema authority.

## Z. Final Design Review

- **Status:** APPROVED per Project Owner direction; not production readiness.
- **Covered:** purpose, candidate field baseline, known references, flat-document constraints, research, and explicit gaps.
- **Remaining:** Section X. Parent/child and numeric-semantic blockers must be resolved before implementation or sample generation.
## Research basis

- [SAP Inspection Planning with Inspection Plans](https://help.sap.com/docs/SAP_ERP/250374f0514e4e0f9057066374265eba/f65cbd534f22b44ce10000000a174cb4.html?locale=en-US&state=PRODUCTION&version=6.17.latest) — Plans define inspection operations and characteristics and can reference methods, sampling procedures, work centers, and materials.
- [SAP Inspection Characteristics](https://help.sap.com/docs/SAP_S4HANA_ON-PREMISE/0533a65ea373484ab21123dfa482ec37/f711b753128eb44ce10000000a174cb4.html) — Individual characteristics and methods describe inspection content and process.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


