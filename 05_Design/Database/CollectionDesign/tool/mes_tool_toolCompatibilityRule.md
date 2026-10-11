**FILE TYPE: PROJECT SPECIFIC**

# mes_tool_toolCompatibilityRule — Collection Specification

**Document ID:** MES-COL-TOOL-toolCompatibilityRule  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** tool  
**Collection type:** Relationship  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Defines allowed tool/product/operation/planning-context combinations for assignment validation.

## B. Business definition

Tool Compatibility Rule is a flat relationship/policy record describing where a Tool or Tool Class is allowed. Repeated compatibility pairs are stored as separate records.

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
| `toolCompatibilityRuleCode` | String | Yes (proposed) | Business identifier for compatibility rule. | Pattern/uniqueness OPEN. | No | Candidate rule code; uniqueness OPEN | Administer policy. | Inventory; SAP PRT assignments | Rule maintenance |
| `toolId` | String | Conditional (proposed) | Specific Tool permitted by rule. | If present paired with toolName; Tool vs class rule grain OPEN. | Yes: toolId + toolName -> mes_tool_tool | Candidate toolId | Specific-tool rule. | DATA-003; inventory | Assignment validation |
| `toolName` | String | Conditional | Tool display snapshot. | Both fields present or absent. | Yes: paired with toolId | No index by default | Readable rule. | DATA-003 | Review |
| `toolClassId` | String | Conditional (proposed) | Tool class when rule applies to a class. | If present pair with toolClassName; exactly-one target rule OPEN. | Yes: toolClassId + toolClassName -> mes_tool_toolClass | Candidate classId | Class-level compatibility. | Inventory; DATA-003 | Tool selection |
| `toolClassName` | String | Conditional | Tool class display snapshot. | Both class fields present or absent. | Yes: paired with toolClassId | No index by default | Readable policy. | DATA-003 | Review |
| `productId` | String | Conditional (proposed) | Applicable Product identity. | If present paired with productName. | Yes: productId + productName -> mes_matl_product | Candidate productId | Product-specific tool policy. | Inventory; DATA-003 | Manufacturing validation |
| `productName` | String | Conditional | Product display snapshot. | Both fields present or absent. | Yes: paired with productId | No index by default | Readable scope. | DATA-003 | Review |
| `operationId` | String | Conditional (proposed) | Reusable Operation where tool is allowed. | If present paired with operationName. | Yes: operationId + operationName -> mes_prod_operation | Candidate operationId | Process-step compatibility. | DEC-003; inventory | Tool assignment |
| `operationName` | String | Conditional | Operation display snapshot. | Both fields present or absent. | Yes: paired with operationId | No index by default | Readable scope. | DATA-003 | Assignment UI |
| `status` | String | Yes (proposed) | Rule validity state. | Values/priority/conflict behavior OPEN. | No | Candidate status filter | Enable/retire policy. | Inventory; lifecycle rules OPEN | Validation |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `toolId` — Yes: toolId + toolName -> mes_tool_tool.
- `toolName` — Yes: paired with toolId.
- `toolClassId` — Yes: toolClassId + toolClassName -> mes_tool_toolClass.
- `toolClassName` — Yes: paired with toolClassId.
- `productId` — Yes: productId + productName -> mes_matl_product.
- `productName` — Yes: paired with productId.
- `operationId` — Yes: operationId + operationName -> mes_prod_operation.
- `operationName` — Yes: paired with operationId.

## G. Validation rules

- Flat top-level scalar fields only; no nested documents or arrays.
- MES IDs are 16-digit strings; external identifiers remain separate.
- Enforce only approved code patterns and uniqueness scopes. Do not invent OPEN scopes or status vocabularies.
- Required ID/display pairs are complete and consistent; optional pairs are both absent or both populated.
- Audit actor pairs are complete; updatedAt >= createdAt.
- Retired reference records remain historically resolvable when established by decision.

## H. Status values

Compatibility-rule validity, independent from Tool lifecycle; values OPEN.

## I. Relationships

**Outgoing:** Optional Tool or Tool Class, Product, Operation reference pairs; other resource identity not defined.  
**Incoming:** Tool Reservation, usage and production assignment validation.  
**Dependencies:** Tool/Tool Class, Product, reusable Operation. Resource target is unresolved and may not be equated with Work Center or Asset.

## J. Recommended indexes

- Default unique _id index.
- Candidate rule code; uniqueness OPEN — Rule maintenance; verify query frequency and selectivity before implementation.
- Candidate toolId — Assignment validation; verify query frequency and selectivity before implementation.
- Candidate classId — Tool selection; verify query frequency and selectivity before implementation.
- Candidate productId — Manufacturing validation; verify query frequency and selectivity before implementation.
- Candidate operationId — Tool assignment; verify query frequency and selectivity before implementation.
- Candidate status filter — Validation; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  toolCompatibilityRuleCode: "<toolCompatibilityRuleCode>",
  toolId: "<toolId>",
  toolName: "<toolName>",
  toolClassId: "<toolClassId>",
  toolClassName: "<toolClassName>",
  productId: "<productId>",
  productName: "<productName>",
  operationId: "<operationId>",
  operationName: "<operationName>",
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

BLOCKER: specific Tool vs class target; whether one or multiple context refs; Work Center/resource boundary; precedence/conflicts; negative vs positive rules; effectivity; status vocabulary.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: OPEN — resource identity and Tool-vs-class rule grain unresolved.
- Status vocabulary: OPEN — values not approved.
- Unresolved dependencies: see Section N; no silent resolution.


## P. Audit and Historical Data

Standard audit fields are flat and paired. Effective dates, assignment history, and lifecycle records must not be embedded. Retention and actor-source policies remain OPEN.

## Q. Real-World Manufacturing Exceptions

Do not infer workforce training eligibility, Tool availability, compatibility, or capacity from unrelated status fields. Invalid or unresolved targets remain visible for controlled reconciliation.

## R. Missing Field Assessment

Detailed requirements remain incomplete for this design. Review with workforce/tool-room/planning/maintenance and database roles. Known gaps: BLOCKER: specific Tool vs class target; whether one or multiple context refs; Work Center/resource boundary; precedence/conflicts; negative vs positive rules; effectivity; status vocabulary.

## S. Missing Collection Assessment

No collection is added. Capacity target and training role/course owners are unresolved; do not reuse Work Center, Asset, or an external entity without approved identity ownership.

## T. Cross-Collection Consistency Check

- Inventory scope/type checked against v1.6.
- Defined references use singular ID/display pairs; Tool and Asset remain distinct under DEC-022.
- Repeated relationships are flat records.
- Resource-capacity target and certification/training taxonomies remain OPEN where identified.
- No cross-collection status vocabulary or numeric type is inferred.

## U. Field Completeness Metrics

See Sections E, F, and J for candidate fields, references, and indexes. Detailed scenario coverage is not established without collection-specific requirements. Known gaps: BLOCKER: specific Tool vs class target; whether one or multiple context refs; Work Center/resource boundary; precedence/conflicts; negative vs positive rules; effectivity; status vocabulary.

## V. One Representative MongoDB Document

The single illustrative flat structure appears in Section L; no sample set is generated.

## W. Assumptions

- The collection inventory and approved ownership decisions control scope.
- DATA-001 requires separate flat records for repeated relationships.
- ID/name duplication is intentional strategic denormalization.
- APPROVED follows the prior owner instruction; open points remain unapproved.

## X. Open Issues

BLOCKER: specific Tool vs class target; whether one or multiple context refs; Work Center/resource boundary; precedence/conflicts; negative vs positive rules; effectivity; status vocabulary.

## Y. Research Sources

Sources in the Research basis section below support the collection's operational use cases only; no vendor schema or status list is adopted.

## Z. Final Design Review

- **Status:** APPROVED under prior Project Owner instruction; not production readiness.
- **Covered:** bounded purpose, candidate fields, flat relationships, research, and unresolved scope.
- **Remaining:** Section X; capacity/identity taxonomy blockers must be resolved before implementation.
## Research basis

- [SAP Assignment of Production Resources/Tools to Operations](https://help.sap.com/docs/SAP_ERP/bfece09273bd474d82fdd97bae070c25/9902b753128eb44ce10000000a174cb4.html?version=6.17.latest) — Tools can be assigned to routing operations and reused across operations; informs compatibility validation.
- [SAP Digital Manufacturing Tool Status](https://help.sap.com/docs/sap-digital-manufacturing/execution/tool-status) — Tool eligibility/status is distinct from assignment compatibility; no vendor status values adopted.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


