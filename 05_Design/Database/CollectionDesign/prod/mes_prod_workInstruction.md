**FILE TYPE: PROJECT SPECIFIC**

# mes_prod_workInstruction — Collection Specification

**Document ID:** MES-COL-PROD-workInstruction  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** prod  
**Collection type:** Configuration  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Controls revisioned electronic work-instruction definitions and effective status, referencing one registered content version.

## B. Business definition

A Work Instruction is the controlled MES instruction definition/revision. Its content is registered separately in mes_prod_instructionContent; instructionContentLocator is an external scalar locator owned by that registry, not embedded payload.

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
| `workInstructionNo` | String | Yes (proposed) | Business identifier for instruction definition. | Follow approved numbering rule if available; scope OPEN. | No | Candidate code+revision lookup | Identify controlled instruction. | Inventory; DEC-009; business number rules | Instruction selection |
| `workInstructionName` | String | Yes | Readable instruction title. | Non-empty. | No | Candidate text search | Operator display. | DATA-003; SAP Work Instruction attachment | Execution display |
| `workInstructionRevision` | String | Yes (proposed) | Controlled revision identifier. | Revision format/effectivity OPEN. | No | Candidate number+revision | Ensure correct instruction version. | Inventory; DEC-009 | Controlled revision |
| `productId` | String | Conditional (proposed) | Product applicability reference. | If present paired with productName. | Yes: productId + productName -> mes_matl_product | Candidate product filter | Limit applicability. | Inventory; DATA-003 | Product-specific instructions |
| `productName` | String | Conditional | Product display snapshot. | Both fields present or absent. | Yes: paired with productId | No index by default | Readable applicability. | DATA-003 | Instruction selection |
| `operationId` | String | Conditional (proposed) | Reusable operation applicability reference. | If present paired with operationName. | Yes: operationId + operationName -> mes_prod_operation | Candidate operationId | Scope to operation. | Inventory; DEC-003 | Operation execution |
| `operationName` | String | Conditional | Operation display snapshot. | Both fields present or absent. | Yes: paired with operationId | No index by default | Readable applicability. | DATA-003 | Instruction display |
| `instructionContentId` | String | Yes | Registry identity for exact content version. | 16-digit ID resolving mes_prod_instructionContent. | Yes: instructionContentId + instructionContentName -> mes_prod_instructionContent | Candidate content ID | Resolve exact registered content version. | DEC-009; inventory | Retrieve instruction content |
| `instructionContentName` | String | Yes | Display snapshot for registered content version. | Paired with instructionContentId. | Yes: paired with instructionContentId | No index by default | Display without lookup. | DATA-003; DEC-009 | Instruction UI |
| `effectiveFrom` | Date | Conditional | Start of instruction applicability. | BSON Date; effectivity overlap rules OPEN. | No | Candidate product/operation/status/effectiveFrom | Select correct revision. | Inventory; DEC-009 | Instruction selection |
| `effectiveTo` | Date | Conditional | End of instruction applicability. | BSON Date >= effectiveFrom. | No | Candidate effective window | Retire revision cleanly. | Inventory; DEC-009 | Historical resolution |
| `status` | String | Yes | Instruction revision lifecycle. | Use only DEC-009 values where its scope covers this collection; otherwise confirm applicability. | No | Candidate status filter | Control effective instruction usage. | DEC-009; inventory | Operator instruction |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `productId` — Yes: productId + productName -> mes_matl_product.
- `productName` — Yes: paired with productId.
- `operationId` — Yes: operationId + operationName -> mes_prod_operation.
- `operationName` — Yes: paired with operationId.
- `instructionContentId` — Yes: instructionContentId + instructionContentName -> mes_prod_instructionContent.
- `instructionContentName` — Yes: paired with instructionContentId.

## G. Validation rules

- Flat top-level scalar fields only; no nested documents or arrays.
- MES IDs are 16-digit strings; external identifiers remain separate.
- Enforce only approved code patterns and uniqueness scopes. Do not invent OPEN scopes or status vocabularies.
- Required ID/display pairs are complete and consistent; optional pairs are both absent or both populated.
- Audit actor pairs are complete; updatedAt >= createdAt.
- Retired reference records remain historically resolvable when established by decision.

## H. Status values

Collection-specific instruction revision status. Verify DEC-009 applies to Work Instruction status; if not, values remain OPEN.

## I. Relationships

**Outgoing:** Product, Operation, Instruction Content pairs as defined; content locator remains in the registry.  
**Incoming:** Operation execution may record the instruction revision used, according to its specification.  
**Dependencies:** Product and Operation masters; mes_prod_instructionContent registry; approval/effectivity rules.

## J. Recommended indexes

- Default unique _id index.
- Candidate code+revision lookup — Instruction selection; verify query frequency and selectivity before implementation.
- Candidate text search — Execution display; verify query frequency and selectivity before implementation.
- Candidate number+revision — Controlled revision; verify query frequency and selectivity before implementation.
- Candidate product filter — Product-specific instructions; verify query frequency and selectivity before implementation.
- Candidate operationId — Operation execution; verify query frequency and selectivity before implementation.
- Candidate content ID — Retrieve instruction content; verify query frequency and selectivity before implementation.
- Candidate product/operation/status/effectiveFrom — Instruction selection; verify query frequency and selectivity before implementation.
- Candidate effective window — Historical resolution; verify query frequency and selectivity before implementation.
- Candidate status filter — Operator instruction; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  workInstructionNo: "<workInstructionNo>",
  workInstructionName: "<workInstructionName>",
  workInstructionRevision: "<workInstructionRevision>",
  productId: "<productId>",
  productName: "<productName>",
  operationId: "<operationId>",
  operationName: "<operationName>",
  instructionContentId: "<instructionContentId>",
  instructionContentName: "<instructionContentName>",
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

Work instruction number scope; revision/effectivity; approval actor/state; product vs operation applicability/cardinality; DEC-009 status scope; content snapshot/retention and exact version matching.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: PASS — all entity references have ID/display pairs; applicability cardinality remains OPEN.
- Status vocabulary: OPEN — applicability of DEC-009 status values must be confirmed.
- Unresolved dependencies: see Section N; no silent resolution.


## P. Audit and Historical Data

Standard flat audit fields are createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Audit actor source and retention remain OPEN. Revision/effectivity history must be preserved by approved revision rules, not embedded arrays.

## Q. Real-World Manufacturing Exceptions

Address obsolete revisions, missing parent references, and effective-date conflicts without substituting another entity. Exception and correction semantics remain OPEN where not defined.

## R. Missing Field Assessment

Detailed collection-specific requirement artifacts were unavailable in this project batch. Assess proposed fields with engineering, production, material control, quality, integration, and database roles before implementation. Known gaps: Work instruction number scope; revision/effectivity; approval actor/state; product vs operation applicability/cardinality; DEC-009 status scope; content snapshot/retention and exact version matching.

## S. Missing Collection Assessment

No collection is added. Repeated routing steps or BOM components are separate records in the inventory-defined relationship collections. Any further missing owner requires inventory change control; no arrays are introduced.

## T. Cross-Collection Consistency Check

- Inventory identity/module/type: checked against Collection Inventory v1.6.
- Flat documents and singular reference keys: PASS for defined fields.
- Required ID/display pairs and audit pairs: PASS for defined references.
- Reusable Operation vs routing-specific operation step: consistent with DEC-003.
- Product vs Material distinction in BOM header: consistent with DEC-022.
- Effectivity, values, uniqueness and external rules: OPEN where stated.

## U. Field Completeness Metrics

Field, requiredness, reference-pair, type, and index counts are listed in Sections E, F, and J. Detailed requirements are unavailable, so no functional scenario coverage percentage is claimed. Research sources are listed below. Unresolved gaps: Work instruction number scope; revision/effectivity; approval actor/state; product vs operation applicability/cardinality; DEC-009 status scope; content snapshot/retention and exact version matching.

## V. One Representative MongoDB Document

The sole illustrative flat structure is in Section L. No extra sample document or 10-document sample set is generated.

## W. Assumptions

- Inventory and approved ownership decisions bound scope.
- Repeated steps/components use separate flat records under DATA-001.
- ID/name duplication is intentional strategic denormalization.
- APPROVED follows the Project Owner's prior instruction; open items remain unresolved.

## X. Open Issues

Work instruction number scope; revision/effectivity; approval actor/state; product vs operation applicability/cardinality; DEC-009 status scope; content snapshot/retention and exact version matching.

## Y. Research Sources

Collection-specific research sources and the design question each informs are recorded in the Research basis section below. Vendor examples are functional evidence only, not project schema authority.

## Z. Final Design Review

- **Status:** APPROVED under Project Owner instruction; not production readiness.
- **Covered:** inventory-bounded purpose, field baseline, flat structure, references, research, and dependency constraints.
- **Remaining:** Section X and all unresolved revision, effectivity, code, and lifecycle rules.
- **Readiness:** reviewable baseline; implementation must wait on any blocking semantic issue.
## Research basis

- [SAP Digital Manufacturing — Attach Work Instructions to Routing Operation Activity](https://help.sap.com/docs/sap-digital-manufacturing/execution/attaching-work-instructions-or-data-collections-to-routing-operation-activity) — Instructions can be associated with routing operation activities; supports operation-scoped linkage scenario.
- [SAP Digital Manufacturing Work Instructions](https://help.sap.com/docs/sap-digital-manufacturing/execution/work-instructions?locale=en-us) — Documents controlled work-instruction content and usage; no external schema is copied.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


