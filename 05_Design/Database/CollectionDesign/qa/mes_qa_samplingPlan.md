**FILE TYPE: PROJECT SPECIFIC**

# mes_qa_samplingPlan — Collection Specification

**Document ID:** MES-COL-QA-samplingPlan  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** qa  
**Collection type:** Configuration  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Defines reusable sampling rules and their applicable inspection scope.

## B. Business definition

A Sampling Plan determines how an inspection population produces sample-size and acceptance requirements. Repeated lot bands or sampling rules must be separate flat records; the rule grain and parent grouping are unresolved.

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
| `samplingPlanCode` | String | Yes (proposed) | Business identifier for sampling-plan definition. | Pattern/uniqueness OPEN. | No | Candidate code+revision; uniqueness OPEN | Select sampling policy. | Inventory; SAP sampling scheme | Inspection selection |
| `samplingPlanName` | String | Yes | Readable sampling-plan name. | Non-empty. | No | Candidate text search | Quality display. | DATA-003; SAP sampling procedure | Quality review |
| `samplingPlanRevision` | String | Yes (proposed) | Plan revision. | Format/effectivity OPEN. | No | Candidate code+revision | Version control. | Inventory; SAP sampling scheme | Revision selection |
| `samplingMethodCode` | String | Yes (proposed) | Sampling method identifier (e.g. fixed, percentage, standards-based) without adopting a value list. | Allowed taxonomy OPEN. | No | Candidate method filter | Explain how size is determined. | SAP Sampling Procedure | Sample determination |
| `lotQuantityLowerBound` | Numeric BSON type OPEN | Conditional | Lower lot-quantity bound for a separate sampling rule row. | Non-negative; BSON type, unit and interval inclusivity OPEN. | No | Candidate parent+lower bound | Apply size rule by lot band. | SAP Sampling Scheme | Lot-size determination |
| `sampleSize` | Numeric BSON type OPEN | Conditional | Number/amount of physical sample units required. | BSON type, integer/fractional basis and UOM OPEN. | No | No index by default | Determine inspection workload. | SAP Sampling Procedure | Sampling |
| `status` | String | Yes (proposed) | Sampling-rule lifecycle state. | Vocabulary/transitions OPEN. | No | Candidate status filter | Control new use. | Lifecycle rules OPEN | Inspection planning |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

No business-entity references; audit actor pairs still apply.

## G. Validation rules

- Flat top-level scalar fields only; no nested documents or arrays.
- MES IDs are 16-digit strings; external identifiers remain separate.
- Enforce only approved code patterns and uniqueness scopes. Do not invent OPEN scopes or status vocabularies.
- Required ID/display pairs are complete and consistent; optional pairs are both absent or both populated.
- Audit actor pairs are complete; updatedAt >= createdAt.
- Retired reference records remain historically resolvable when established by decision.

## H. Status values

Sampling plan lifecycle; values OPEN and independent from inspection status.

## I. Relationships

**Outgoing:** Sampling rule detail grain and parent identity unresolved.  
**Incoming:** Inspection Plan and Inspection Request may reference sampling plan identity once pair owner is established.  
**Dependencies:** Product/material and inspection-plan scope; inspection plan direction and rule hierarchy OPEN.

## J. Recommended indexes

- Default unique _id index.
- Candidate code+revision; uniqueness OPEN — Inspection selection; verify query frequency and selectivity before implementation.
- Candidate text search — Quality review; verify query frequency and selectivity before implementation.
- Candidate code+revision — Revision selection; verify query frequency and selectivity before implementation.
- Candidate method filter — Sample determination; verify query frequency and selectivity before implementation.
- Candidate parent+lower bound — Lot-size determination; verify query frequency and selectivity before implementation.
- Candidate status filter — Inspection planning; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  samplingPlanCode: "<samplingPlanCode>",
  samplingPlanName: "<samplingPlanName>",
  samplingPlanRevision: "<samplingPlanRevision>",
  samplingMethodCode: "<samplingMethodCode>",
  lotQuantityLowerBound: <value>,
  sampleSize: <value>,
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

BLOCKER: define rule/lot-band record grain and parent identity; sampling method taxonomy; lot quantity/sample size types and units; acceptance/rejection criteria; standard compliance and effective dates; reference-owner direction.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: OPEN — no parent/plan pair finalized.
- Status vocabulary: OPEN — values not approved.
- Unresolved dependencies: see Section N; no silent resolution.


## P. Audit and Historical Data

Standard audit fields are flat and paired. Preserve revisions and effective dates without embedded history lists. Actor-source, retention, immutable revision, and approval evidence rules remain OPEN.

## Q. Real-World Manufacturing Exceptions

Do not infer sample sizes, acceptance thresholds, maintenance frequencies, checklist outcomes, or value vocabularies. Keep invalid or obsolete references identifiable for controlled correction.

## R. Missing Field Assessment

Collection-specific functional requirements are not sufficient to settle all field rules. Review with quality, maintenance, operations, and database roles. Known gaps: BLOCKER: define rule/lot-band record grain and parent identity; sampling method taxonomy; lot quantity/sample size types and units; acceptance/rejection criteria; standard compliance and effective dates; reference-owner direction.

## S. Missing Collection Assessment

No collection is added. Inventory currently lacks a separately named characteristic/checklist-item/rule-band owner. If the same-collection flat row model is not accepted, revise inventory through change control; do not use arrays.

## T. Cross-Collection Consistency Check

- Inventory name/module/type: checked against v1.6.
- Flatness and singular field stems: PASS for defined fields.
- ID/display and audit pairs: PASS for defined references.
- Repeated characteristics, rule bands, and checklist items remain separately addressable; parent strategy OPEN.
- Numeric types, lifecycle values, effectivity, and relationship direction remain OPEN where stated.

## U. Field Completeness Metrics

See Sections E, F, and J for field, reference-pair and index counts. Detailed requirement coverage is not verified; no functional completeness percentage is claimed. Known blockers: BLOCKER: define rule/lot-band record grain and parent identity; sampling method taxonomy; lot quantity/sample size types and units; acceptance/rejection criteria; standard compliance and effective dates; reference-owner direction.

## V. One Representative MongoDB Document

The sole illustrative flat structure appears in Section L. No additional examples or sample data are generated.

## W. Assumptions

- Inventory defines the current collection set and boundaries.
- DATA-001 requires separate flat records for repeated rules and checklist/inspection items.
- ID/name duplication is intentional strategic denormalization.
- APPROVED follows prior owner direction; open issues are not approvals.

## X. Open Issues

BLOCKER: define rule/lot-band record grain and parent identity; sampling method taxonomy; lot quantity/sample size types and units; acceptance/rejection criteria; standard compliance and effective dates; reference-owner direction.

## Y. Research Sources

Collection-specific manufacturing references and the decision context they support are listed under Research basis below. They are not project schema authority.

## Z. Final Design Review

- **Status:** APPROVED per Project Owner direction; not production readiness.
- **Covered:** purpose, candidate field baseline, known references, flat-document constraints, research, and explicit gaps.
- **Remaining:** Section X. Parent/child and numeric-semantic blockers must be resolved before implementation or sample generation.
## Research basis

- [SAP Sampling Procedure](https://help.sap.com/docs/SAP_ERP/90f562dc43f84f5f8ab7e082e47f9a6c/fd55b853dcfcb44ce10000000a174cb4.html) — Sampling procedures define sample-size calculation and inspection-characteristic valuation, including fixed, 100%, and percentage sampling examples.
- [SAP Sampling Scheme](https://help.sap.com/docs/SAP_ERP/90f562dc43f84f5f8ab7e082e47f9a6c/0056b853dcfcb44ce10000000a174cb4.html) — Sampling plans may depend on lot quantity and define sample size and acceptance/rejection criteria; informs rule-grain gaps.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


