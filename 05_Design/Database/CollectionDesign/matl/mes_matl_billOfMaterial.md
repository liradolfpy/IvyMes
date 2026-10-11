**FILE TYPE: PROJECT SPECIFIC**

# mes_matl_billOfMaterial — Collection Specification

**Document ID:** MES-COL-MATL-billOfMaterial  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** matl  
**Collection type:** Configuration  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Defines a revisioned Bill of Material for a Product, including validity and composition scope.

## B. Business definition

A BOM defines product composition. Header records identify the Product; each component is represented as a separate mes_matl_bomComponent record linked to the BOM. Product and Material remain distinct under DEC-022.

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
| `billOfMaterialNo` | String | Yes (proposed) | Business identifier for BOM definition. | Pattern/uniqueness scope OPEN. | No | Candidate BOM no+revision; scope OPEN | Select composition definition. | Inventory; SAP Material BOM | BOM selection |
| `billOfMaterialName` | String | Yes | Readable BOM description/name. | Non-empty. | No | Candidate text search | Engineering/planning display. | DATA-003; SAP Material BOM | Composition display |
| `productId` | String | Yes | Distinct Product whose composition is defined. | 16-digit ID resolving mes_matl_product. | Yes: productId + productName -> mes_matl_product | Candidate productId + status | BOM header identity is Product, not Material. | DEC-022; inventory; SAP BOM definition | Product composition |
| `productName` | String | Yes | Product display snapshot. | Pair consistency with productId. | Yes: paired with productId | No index by default | Avoid display lookup. | DATA-003; DEC-022 | BOM display |
| `billOfMaterialRevision` | String | Yes (proposed) | Revision of product composition definition. | Format/effectivity uniqueness OPEN. | No | Candidate product+revision | Manage engineering changes. | Inventory; SAP BOM validity | Revision selection |
| `effectiveFrom` | Date | Conditional | Start of BOM validity. | BSON Date; <= effectiveTo. | No | Candidate product+status+effectiveFrom | Select valid composition. | SAP BOM validity | Production planning |
| `effectiveTo` | Date | Conditional | End of BOM validity. | BSON Date >= effectiveFrom. | No | Candidate validity window | Historical composition resolution. | SAP BOM validity | Order traceability |
| `status` | String | Yes (proposed) | BOM lifecycle/release state. | Values/transitions OPEN. | No | Candidate status filter | Control selection. | Lifecycle rules OPEN | BOM use |

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

BOM lifecycle values and transitions are OPEN; no shared vocabulary assumed.

## I. Relationships

**Outgoing:** Product pair; composition detail in separate mes_matl_bomComponent records.  
**Incoming:** Planning, production order, material reservation/consumption and genealogy consumers.  
**Dependencies:** mes_matl_product and mes_matl_bomComponent. Component material references belong to detail records. BOM/product revision and effectivity rules OPEN.

## J. Recommended indexes

- Default unique _id index.
- Candidate BOM no+revision; scope OPEN — BOM selection; verify query frequency and selectivity before implementation.
- Candidate text search — Composition display; verify query frequency and selectivity before implementation.
- Candidate productId + status — Product composition; verify query frequency and selectivity before implementation.
- Candidate product+revision — Revision selection; verify query frequency and selectivity before implementation.
- Candidate product+status+effectiveFrom — Production planning; verify query frequency and selectivity before implementation.
- Candidate validity window — Order traceability; verify query frequency and selectivity before implementation.
- Candidate status filter — BOM use; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  billOfMaterialNo: "<billOfMaterialNo>",
  billOfMaterialName: "<billOfMaterialName>",
  productId: "<productId>",
  productName: "<productName>",
  billOfMaterialRevision: "<billOfMaterialRevision>",
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

BOM number/revision uniqueness; alternative BOMs; plant/product applicability; effectivity overlap; quantity/unit ownership; whether status/release workflow; component line ownership and snapshot behavior.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: PASS — Product is referenced by pair; component records separated.
- Status vocabulary: OPEN — vocabulary not approved.
- Unresolved dependencies: see Section N; no silent resolution.


## P. Audit and Historical Data

Standard flat audit fields are createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Audit actor source and retention remain OPEN. Revision/effectivity history must be preserved by approved revision rules, not embedded arrays.

## Q. Real-World Manufacturing Exceptions

Address obsolete revisions, missing parent references, and effective-date conflicts without substituting another entity. Exception and correction semantics remain OPEN where not defined.

## R. Missing Field Assessment

Detailed collection-specific requirement artifacts were unavailable in this project batch. Assess proposed fields with engineering, production, material control, quality, integration, and database roles before implementation. Known gaps: BOM number/revision uniqueness; alternative BOMs; plant/product applicability; effectivity overlap; quantity/unit ownership; whether status/release workflow; component line ownership and snapshot behavior.

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

Field, requiredness, reference-pair, type, and index counts are listed in Sections E, F, and J. Detailed requirements are unavailable, so no functional scenario coverage percentage is claimed. Research sources are listed below. Unresolved gaps: BOM number/revision uniqueness; alternative BOMs; plant/product applicability; effectivity overlap; quantity/unit ownership; whether status/release workflow; component line ownership and snapshot behavior.

## V. One Representative MongoDB Document

The sole illustrative flat structure is in Section L. No extra sample document or 10-document sample set is generated.

## W. Assumptions

- Inventory and approved ownership decisions bound scope.
- Repeated steps/components use separate flat records under DATA-001.
- ID/name duplication is intentional strategic denormalization.
- APPROVED follows the Project Owner's prior instruction; open items remain unresolved.

## X. Open Issues

BOM number/revision uniqueness; alternative BOMs; plant/product applicability; effectivity overlap; quantity/unit ownership; whether status/release workflow; component line ownership and snapshot behavior.

## Y. Research Sources

Collection-specific research sources and the design question each informs are recorded in the Research basis section below. Vendor examples are functional evidence only, not project schema authority.

## Z. Final Design Review

- **Status:** APPROVED under Project Owner instruction; not production readiness.
- **Covered:** inventory-bounded purpose, field baseline, flat structure, references, research, and dependency constraints.
- **Remaining:** Section X and all unresolved revision, effectivity, code, and lifecycle rules.
- **Readiness:** reviewable baseline; implementation must wait on any blocking semantic issue.
## Research basis

- [SAP Material Bill of Material](https://help.sap.com/docs/SAP_S4HANA_ON-PREMISE/9905622a5c1f49ba84e9076fc83a9c2c/96f45c5463890d4ee10000000a423f68.html?locale=en-US&state=PRODUCTION&version=2025.001) — Defines BOM as structured components making up a product with quantity and unit; supports header/detail separation.
- [SAP BOM Items](https://help.sap.com/docs/SAP_S4HANA_ON-PREMISE/5e23dc8fe9be4fd496f8ab556667ea05/ae94d7531a4d414de10000000a174cb4.html) — Describes BOM component quantities relative to BOM header base quantity and unit conversions; project quantity rules remain open.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


