**FILE TYPE: PROJECT SPECIFIC**

# mes_trace_lot — Collection Specification

**Document ID:** MES-COL-TRACE-lot  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** trace  
**Collection type:** Master  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Owns a traceable lot/batch identity for a quantity of a Product or Material and supports genealogy/recall queries.

## B. Business definition

Lot is a traceability identity for a grouped quantity, separate from inventory balance and movement. A lot may represent produced product or component material; applicability owner and identity namespace are unresolved.

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
| `lotNumber` | String | Yes (proposed) | Business/source lot identifier. | Pattern, source namespace and uniqueness scope OPEN. | External/business number distinct from _id. | Candidate unique source+lotNumber | Trace batch identity. | Inventory; SAP Batch Management | Forward/reverse trace |
| `lotName` | String | Conditional | Human-readable lot label if distinct from lotNumber. | Requiredness and semantics OPEN. | No | Candidate text search | Readable trace display. | DATA-003 naming convention | Traceability search |
| `materialId` | String | Conditional (proposed) | Material identity for a component-material lot. | If present pair with materialName; product/material applicability OPEN. | Yes: materialId + materialName -> mes_matl_material | Candidate material+lot | Material genealogy. | DEC-022; SAP batch management | Incoming lot trace |
| `materialName` | String | Conditional | Material display snapshot. | Both fields present or absent. | Yes: paired with materialId | No index by default | Readable identity. | DATA-003 | Trace display |
| `productId` | String | Conditional (proposed) | Product identity for a produced product lot. | If present paired with productName; may be mutually exclusive with material pair, rule OPEN. | Yes: productId + productName -> mes_matl_product | Candidate product+lot | Finished/subassembly trace. | DEC-022; SAP batch trace | Recall |
| `productName` | String | Conditional | Product display snapshot. | Both product fields present or absent. | Yes: paired with productId | No index by default | Readable trace. | DATA-003 | Trace display |
| `status` | String | Proposed | Lot identity lifecycle/traceability state. | Values and meaning OPEN; do not conflate with inventory availability. | No | Candidate status filter | Control new use while preserving trace. | Lifecycle rules OPEN | Lot search |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `materialId` — Yes: materialId + materialName -> mes_matl_material.
- `materialName` — Yes: paired with materialId.
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

Lot identity lifecycle only; inventory stock status is separate. Vocabulary OPEN.

## I. Relationships

**Outgoing:** Conditional Material or Product identity pair; exclusivity and source namespace unresolved.  
**Incoming:** Serial Number, inventory, genealogy links, as-built records, movements, recalls and certificates.  
**Dependencies:** Material/Product; lot number source may be ERP/WMS; no external ownership chosen.

## J. Recommended indexes

- Default unique _id index.
- Candidate unique source+lotNumber — Forward/reverse trace; verify query frequency and selectivity before implementation.
- Candidate text search — Traceability search; verify query frequency and selectivity before implementation.
- Candidate material+lot — Incoming lot trace; verify query frequency and selectivity before implementation.
- Candidate product+lot — Recall; verify query frequency and selectivity before implementation.
- Candidate status filter — Lot search; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  lotNumber: "<lotNumber>",
  lotName: "<lotName>",
  materialId: "<materialId>",
  materialName: "<materialName>",
  productId: "<productId>",
  productName: "<productName>",
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

BLOCKER: lot vs batch semantics; Product/Material applicability and mutual exclusivity; source namespace/uniqueness; required display name; quantity and unit owner; lifecycle/status; lot-to-order lineage.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: OPEN — lot object type and source owner unresolved.
- Status vocabulary: OPEN — values not approved.
- Unresolved dependencies: see Section N; no silent resolution.


## P. Audit and Historical Data

Standard audit fields are flat and paired. Trace identities and inventory/labor history must remain resolvable over their approved retention period; source synchronization and immutability remain OPEN.

## Q. Real-World Manufacturing Exceptions

Preserve orphaned or externally mismatched trace identifiers for reconciliation; do not merge lot, serial, balance, or labor-assignment meanings. Quantity corrections and identity conflicts need controlled workflows.

## R. Missing Field Assessment

Detailed requirements are incomplete. Review with traceability, material control, workforce, production, integration, and database roles. Known gaps: BLOCKER: lot vs batch semantics; Product/Material applicability and mutual exclusivity; source namespace/uniqueness; required display name; quantity and unit owner; lifecycle/status; lot-to-order lineage.

## S. Missing Collection Assessment

No collection is added. The inventory remains authoritative. If additional identity dimensions, movement/event history, or relationship owners are needed, request inventory review rather than embedding arrays.

## T. Cross-Collection Consistency Check

- Inventory scope/type checked against v1.6.
- Product, Material, Plant, Operator, Work Order, Shift, Location and UOM pairs use project naming rules where defined.
- Lot and Serial are distinct identities; Inventory Balance is a state/balance, not a movement.
- Numeric balance type, identity namespace, and status rules remain OPEN.
- Flat-document convention respected.

## U. Field Completeness Metrics

See Sections E, F, and J for field, reference, and index counts. Detailed requirements are incomplete; no full scenario coverage percentage is claimed. Known gaps: BLOCKER: lot vs batch semantics; Product/Material applicability and mutual exclusivity; source namespace/uniqueness; required display name; quantity and unit owner; lifecycle/status; lot-to-order lineage.

## V. One Representative MongoDB Document

The sole illustrative flat structure appears in Section L; no sample documents are generated.

## W. Assumptions

- Inventory and approved ownership decisions define scope.
- Repeated relationships are separate flat records.
- ID/name duplication is intentional strategic denormalization.
- APPROVED follows prior owner direction; unresolved questions remain OPEN.

## X. Open Issues

BLOCKER: lot vs batch semantics; Product/Material applicability and mutual exclusivity; source namespace/uniqueness; required display name; quantity and unit owner; lifecycle/status; lot-to-order lineage.

## Y. Research Sources

Collection-specific public sources and their functional relevance appear under Research basis below; they do not establish project identifiers or lifecycle values.

## Z. Final Design Review

- **Status:** APPROVED per prior Project Owner instruction; not production readiness.
- **Covered:** bounded purpose, candidate fields, flat representation, references, research and known gaps.
- **Remaining:** Section X; source identity and quantity semantics must be resolved before implementation where marked blocking.
## Research basis

- [SAP Digital Manufacturing Batch Management](https://help.sap.com/docs/sap-digital-manufacturing/execution/batch-management) — Batch identity supports traceability, recalls, inventory quantity distinction and expiry-related scenarios.
- [SAP Business One Forward/Reverse Batch and Serial Trace](https://help.sap.com/docs/SAP_BUSINESS_ONE/f19b844f41b74a8c8ca26f4d501e032b/ff563868d10f488c97fa0e6cc90d7717.html) — Forward and reverse searches trace batch/serial through inventory documents and quantities.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


