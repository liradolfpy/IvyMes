**FILE TYPE: PROJECT SPECIFIC**

# mes_maint_checklistDefinition — Collection Specification

**Document ID:** MES-COL-MAINT-checklistDefinition  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** maint  
**Collection type:** Configuration  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Defines revisioned maintenance checklist items and acceptance criteria for repeatable maintenance checks.

## B. Business definition

Checklist Definition is reusable maintenance guidance. Each repeated checklist item/criterion must be a separate flat record; inventory does not settle whether the definition header and item rows share one collection or how they are grouped.

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
| `checklistCode` | String | Yes (proposed) | Business identifier for checklist definition. | Pattern/uniqueness OPEN. | No | Candidate code+revision | Select reusable checklist. | Inventory; SAP Maintenance Task Lists | Maintenance planning |
| `checklistName` | String | Yes | Readable checklist name. | Non-empty. | No | Candidate text search | Technician display. | DATA-003 | Work preparation |
| `checklistRevision` | String | Yes (proposed) | Checklist revision. | Format/effectivity OPEN. | No | Candidate code+revision | Control maintenance changes. | Inventory; SAP task list | Revision selection |
| `checklistItemSequence` | Int32 | Proposed per separate item record | Order of one checklist item. | Positive integer; parent uniqueness OPEN. | No | Candidate parent+sequence | Technician execution order. | DATA-001; maintenance task list activities | Checklist execution |
| `checklistItemText` | String | Proposed | One check instruction or acceptance prompt. | Scalar text; no nested criteria list. | No | No index by default | Define repeatable check. | SAP Maintenance Task Lists | Maintenance inspection |
| `assetClassCode` | String | Conditional (proposed) | Asset-class applicability code. | Classification owner/taxonomy OPEN. | No approved class collection listed. | Candidate filter after owner decision | Apply checklist to eligible equipment types. | Inventory; SAP technical-object classification | Checklist assignment |
| `assetClassName` | String | Conditional | Display label for asset class. | Pair/entity semantics OPEN. | Classification owner OPEN. | No index by default | Readable applicability. | DATA-003 if entity | Maintenance planning |
| `acceptanceCriteriaText` | String | Conditional | Scalar pass/fail criteria for one item. | Numeric/qualitative rule model OPEN; no nested structure. | No | No index by default | Define acceptable observation. | Inventory; maintenance checklist practice | Check result |
| `status` | String | Proposed | Checklist definition/item lifecycle. | Vocabulary and record-grain semantics OPEN. | No | Candidate status filter | Control use of definition. | Lifecycle rules OPEN | Checklist selection |

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

Collection/item meaning and lifecycle values OPEN.

## I. Relationships

**Outgoing:** Optional asset classification is scalar pending owner decision; maintenance plan/order relationship direction OPEN.  
**Incoming:** Checklist result/maintenance order consumers.  
**Dependencies:** Maintenance Plan and Asset; dependency map flags checklist ownership direction unresolved.

## J. Recommended indexes

- Default unique _id index.
- Candidate code+revision — Maintenance planning; verify query frequency and selectivity before implementation.
- Candidate text search — Work preparation; verify query frequency and selectivity before implementation.
- Candidate code+revision — Revision selection; verify query frequency and selectivity before implementation.
- Candidate parent+sequence — Checklist execution; verify query frequency and selectivity before implementation.
- Candidate filter after owner decision — Checklist assignment; verify query frequency and selectivity before implementation.
- Candidate status filter — Checklist selection; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  checklistCode: "<checklistCode>",
  checklistName: "<checklistName>",
  checklistRevision: "<checklistRevision>",
  checklistItemSequence: <value>,
  checklistItemText: "<checklistItemText>",
  assetClassCode: "<assetClassCode>",
  assetClassName: "<assetClassName>",
  acceptanceCriteriaText: "<acceptanceCriteriaText>",
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

BLOCKER: checklist header-vs-item record model and parent ID/name; checklistDefinition-to-plan direction; asset-class owner; item types/criteria/evidence; revision, effectivity and approval.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: OPEN — asset class and parent relationships unresolved.
- Status vocabulary: OPEN — vocabulary not approved.
- Unresolved dependencies: see Section N; no silent resolution.


## P. Audit and Historical Data

Standard audit fields are flat and paired. Preserve revisions and effective dates without embedded history lists. Actor-source, retention, immutable revision, and approval evidence rules remain OPEN.

## Q. Real-World Manufacturing Exceptions

Do not infer sample sizes, acceptance thresholds, maintenance frequencies, checklist outcomes, or value vocabularies. Keep invalid or obsolete references identifiable for controlled correction.

## R. Missing Field Assessment

Collection-specific functional requirements are not sufficient to settle all field rules. Review with quality, maintenance, operations, and database roles. Known gaps: BLOCKER: checklist header-vs-item record model and parent ID/name; checklistDefinition-to-plan direction; asset-class owner; item types/criteria/evidence; revision, effectivity and approval.

## S. Missing Collection Assessment

No collection is added. Inventory currently lacks a separately named characteristic/checklist-item/rule-band owner. If the same-collection flat row model is not accepted, revise inventory through change control; do not use arrays.

## T. Cross-Collection Consistency Check

- Inventory name/module/type: checked against v1.6.
- Flatness and singular field stems: PASS for defined fields.
- ID/display and audit pairs: PASS for defined references.
- Repeated characteristics, rule bands, and checklist items remain separately addressable; parent strategy OPEN.
- Numeric types, lifecycle values, effectivity, and relationship direction remain OPEN where stated.

## U. Field Completeness Metrics

See Sections E, F, and J for field, reference-pair and index counts. Detailed requirement coverage is not verified; no functional completeness percentage is claimed. Known blockers: BLOCKER: checklist header-vs-item record model and parent ID/name; checklistDefinition-to-plan direction; asset-class owner; item types/criteria/evidence; revision, effectivity and approval.

## V. One Representative MongoDB Document

The sole illustrative flat structure appears in Section L. No additional examples or sample data are generated.

## W. Assumptions

- Inventory defines the current collection set and boundaries.
- DATA-001 requires separate flat records for repeated rules and checklist/inspection items.
- ID/name duplication is intentional strategic denormalization.
- APPROVED follows prior owner direction; open issues are not approvals.

## X. Open Issues

BLOCKER: checklist header-vs-item record model and parent ID/name; checklistDefinition-to-plan direction; asset-class owner; item types/criteria/evidence; revision, effectivity and approval.

## Y. Research Sources

Collection-specific manufacturing references and the decision context they support are listed under Research basis below. They are not project schema authority.

## Z. Final Design Review

- **Status:** APPROVED per Project Owner direction; not production readiness.
- **Covered:** purpose, candidate field baseline, known references, flat-document constraints, research, and explicit gaps.
- **Remaining:** Section X. Parent/child and numeric-semantic blockers must be resolved before implementation or sample generation.
## Research basis

- [SAP Maintenance Task List](https://help.sap.com/docs/SAP_SUPPLY_CHAIN_MANAGEMENT/d8a0d82aa9c041028502c8c175143205/414bc95360267614e10000000a174cb4.html) — Maintenance check task lists consist of reusable headers and one or more activities; supports repeated item scenarios.
- [SAP Maintenance Plan](https://help.sap.com/docs/SAP_ERP/11825b10747e4ee4b91ecc1dba612536/617ac253d0a4b54ce10000000a174cb4.html) — Maintenance plans apply recurring tasks to technical objects and can reference task lists.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


