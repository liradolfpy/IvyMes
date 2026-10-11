**FILE TYPE: PROJECT SPECIFIC**

# mes_prod_routing — Collection Specification

**Document ID:** MES-COL-PROD-routing  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** prod  
**Collection type:** Configuration  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Defines a revisioned production routing identifying the ordered process definition applicable to a product/context.

## B. Business definition

Routing is a reusable manufacturing process definition. Repeated operations are separate mes_prod_routingOperation records linked by routingId/routingName and an ordered step sequence; no embedded operation array.

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
| `routingCode` | String | Yes (proposed) | Business identifier for routing family. | Follow DEC-013 where applicable; uniqueness/effectivity scope OPEN. | No | Candidate routingCode; scope per DEC-013 | Select process definition. | Inventory; DEC-013; SAP Routing Integration | Routing selection |
| `routingName` | String | Yes | Readable routing display name. | Non-empty. | No | Candidate text search | Planning/operator display. | DATA-003 | Process display |
| `routingRevision` | String | Yes (proposed) | Revision/version identity for routing definition. | Revision scheme and uniqueness OPEN. | No | Candidate code+revision compound index | Control process changes. | Inventory; SAP routing version concepts | Revision selection |
| `productId` | String | Yes (proposed) | Product whose manufacturing process this routing defines. | 16-digit ID resolving mes_matl_product. | Yes: productId + productName -> mes_matl_product | Candidate productId | Bind process to product identity. | Inventory; DEC-022; SAP Routing Integration | Order routing selection |
| `productName` | String | Yes (proposed) | Product display snapshot paired with productId. | Pair consistency at write time. | Yes: paired with productId | No index by default | Avoid routine display lookup. | DATA-003 | Routing display |
| `effectiveFrom` | Date | Conditional | Start of routing applicability. | BSON Date; <= effectiveTo if supplied. | No | Candidate product+status+effectiveFrom | Effectivity selection. | Inventory; SAP routing integration | Date-based selection |
| `effectiveTo` | Date | Conditional | End of routing applicability. | BSON Date; >= effectiveFrom. | No | Candidate effective window | Close prior revision. | Inventory; SAP routing versioning | Historical selection |
| `status` | String | Yes (proposed) | Routing lifecycle/release state. | Vocabulary/transitions OPEN. | No | Candidate status filter | Control routing selection. | DEC-011 only for listed PROD collections; routing list OPEN | Process selection |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

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

Routing lifecycle values are OPEN for this collection unless directly approved elsewhere.

## I. Relationships

**Outgoing:** Product reference pair; repeated steps via mes_prod_routingOperation, not embedded.  
**Incoming:** Work order, dispatch, execution, scheduling, and work-instruction consumers.  
**Dependencies:** Product master; routingOperation steps; routing effectivity and revision policy unresolved.

## J. Recommended indexes

- Default unique _id index.
- Candidate routingCode; scope per DEC-013 — Routing selection; verify query frequency and selectivity before implementation.
- Candidate text search — Process display; verify query frequency and selectivity before implementation.
- Candidate code+revision compound index — Revision selection; verify query frequency and selectivity before implementation.
- Candidate productId — Order routing selection; verify query frequency and selectivity before implementation.
- Candidate product+status+effectiveFrom — Date-based selection; verify query frequency and selectivity before implementation.
- Candidate effective window — Historical selection; verify query frequency and selectivity before implementation.
- Candidate status filter — Process selection; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  routingCode: "<routingCode>",
  routingName: "<routingName>",
  routingRevision: "<routingRevision>",
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

Routing business-number scope; revision/effectivity overlap; release/approval boundary; alternate paths/branching representation; relationship to order snapshot/version; work-center/resource ownership constraints.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: PASS — Product pair defined; routingOperation parent is the repeated separate-record pattern.
- Status vocabulary: OPEN — routing-specific lifecycle vocabulary unresolved.
- Unresolved dependencies: see Section N; no silent resolution.


## P. Audit and Historical Data

Standard flat audit fields are createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Audit actor source and retention remain OPEN. Revision/effectivity history must be preserved by approved revision rules, not embedded arrays.

## Q. Real-World Manufacturing Exceptions

Address obsolete revisions, missing parent references, and effective-date conflicts without substituting another entity. Exception and correction semantics remain OPEN where not defined.

## R. Missing Field Assessment

Detailed collection-specific requirement artifacts were unavailable in this project batch. Assess proposed fields with engineering, production, material control, quality, integration, and database roles before implementation. Known gaps: Routing business-number scope; revision/effectivity overlap; release/approval boundary; alternate paths/branching representation; relationship to order snapshot/version; work-center/resource ownership constraints.

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

Field, requiredness, reference-pair, type, and index counts are listed in Sections E, F, and J. Detailed requirements are unavailable, so no functional scenario coverage percentage is claimed. Research sources are listed below. Unresolved gaps: Routing business-number scope; revision/effectivity overlap; release/approval boundary; alternate paths/branching representation; relationship to order snapshot/version; work-center/resource ownership constraints.

## V. One Representative MongoDB Document

The sole illustrative flat structure is in Section L. No extra sample document or 10-document sample set is generated.

## W. Assumptions

- Inventory and approved ownership decisions bound scope.
- Repeated steps/components use separate flat records under DATA-001.
- ID/name duplication is intentional strategic denormalization.
- APPROVED follows the Project Owner's prior instruction; open items remain unresolved.

## X. Open Issues

Routing business-number scope; revision/effectivity overlap; release/approval boundary; alternate paths/branching representation; relationship to order snapshot/version; work-center/resource ownership constraints.

## Y. Research Sources

Collection-specific research sources and the design question each informs are recorded in the Research basis section below. Vendor examples are functional evidence only, not project schema authority.

## Z. Final Design Review

- **Status:** APPROVED under Project Owner instruction; not production readiness.
- **Covered:** inventory-bounded purpose, field baseline, flat structure, references, research, and dependency constraints.
- **Remaining:** Section X and all unresolved revision, effectivity, code, and lifecycle rules.
- **Readiness:** reviewable baseline; implementation must wait on any blocking semantic issue.
## Research basis

- [SAP Digital Manufacturing Routing Integration](https://help.sap.com/docs/sap-digital-manufacturing/integration-guide/routing-integration?ai=true) — Routing defines process steps and their sequence for a product and is used as a template for production/planned orders.
- [SAP ME Routing](https://help.sap.com/docs/SAP_MANUFACTURING_EXECUTION/e1adc70af32241619335c8768a892edb/9cf01debaa804b4c9f59c6d5f9c8a5d0.html) — Routing is a sequence of operation steps; tabular structures represent steps separately, supporting a flat step-record approach.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


