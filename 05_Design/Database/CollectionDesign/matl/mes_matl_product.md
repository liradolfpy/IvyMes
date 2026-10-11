**FILE TYPE: PROJECT SPECIFIC**

# mes_matl_product — Collection Specification

**Document ID:** MES-COL-MATL-product  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** matl  
**Collection type:** Master  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection Overview

**Collection:** mes_matl_product
**Module:** Material Management
**Type:** Master
**Primary business entity:** Product/Part
**Purpose:** Maintains a distinct product/part identity, revision, and lifecycle for manufactured or defined offerings used by production and traceability.
**Business definition:** This collection stores the inventory-defined Product/Part identity or reference, owned by Material Management, for the stated purpose. It does not imply additional parentage or field rules beyond current project decisions.
## B. Industry Research Summary

Research benchmarks support the business purpose and use cases; they do not define this MES's field rules. See sources in Section Y. Research coverage is limited to publicly documented functionality; no vendor internal schema was used.

## C. Real-World Manufacturing Scenarios

- Normal: use this collection as the inventory-defined identity/reference anchor.
- Lifecycle: create, update, retire and resolve historical references according to approved values where available.
- Integration/exception: preserve external identity separately where the dictionary defines it; unresolved provider rules remain OPEN.
- Detailed scenario evidence and coverage limits are in Section D.

## D. Functional Coverage Matrix

| Manufacturing scenario | Required information | Supporting fields | Covered? | Gap |
|---|---|---|---|---|
| Routine master/reference use | Stable identity, display, classification/lifecycle as applicable | See field dictionary | Partial | Inventory-level scope; detailed functional requirements are absent. |
| Retirement and historical reference | Preserve old references and prevent inappropriate new use | status where defined | Partial / OPEN | Values/transition rules unresolved except where an approved decision defines them. |
| External integration | Source identity and reconciliation where required | External fields only where defined | Partial / OPEN | Source ownership and mappings remain unresolved. |
| Audit | Who created/updated and when | Standard audit fields | Covered at field level | Actor identity source and retention policy remain OPEN. |
| Multi-plant use | Scope by plant where applicable | No plant field unless included in dictionary | OPEN | No plant-scoping rule is inferred for this master. |

## E. Complete Field Dictionary

| Field | BSON type | Requiredness | Business meaning | Validation | Reference | Index guidance | Reason | Evidence | Scenario |
|---|---|---|---|---|---|---|---|---|---|
| `_id` | String | Yes | Immutable globally unique 16-digit MES ID. | Exactly 16 digits; globally unique. | No | Default unique _id index | Primary identity | Project_Context DATA-003 | All references |
| `createdAt` | Date | Yes | UTC creation time | BSON Date | No | No index by default | Audit | KeyNamingConvention | Creation |
| `createdById` | String | Yes | 16-digit creator user/process ID | Pair with createdBy | User/process source OPEN | No index by default | Attribution | DATA-003 | Creation |
| `createdBy` | String | Yes | Creator display value paired with createdById | Non-empty, matching ID | User/process display pair | No index by default | Readable attribution | KeyNamingConvention | Creation |
| `updatedAt` | Date | Yes | UTC last update time | BSON Date >= createdAt | No | No index by default | Audit | KeyNamingConvention | Updates |
| `updatedById` | String | Yes | 16-digit updater user/process ID | Pair with updatedBy | User/process source OPEN | No index by default | Attribution | DATA-003 | Updates |
| `updatedBy` | String | Yes | Updater display value paired with updatedById | Non-empty, matching ID | User/process display pair | No index by default | Readable attribution | KeyNamingConvention | Updates |
| `productNo` | String | Yes (proposed) | Business number for product lineage. | Pattern and uniqueness scope OPEN outside DEC-013. | No | Candidate productNo lookup; uniqueness pending | Product identification in production/integration. | Inventory; SAP Product Master | Order and product selection |
| `productName` | String | Yes | Human-readable product/part name. | Non-empty. | No | Optional productName search | Product display. | Inventory; SAP Product Master | Shop-floor and reporting |
| `productDescription` | String | No | Scalar product description. | Plain text. | No | No index by default | Additional product context. | Inventory | Administration |
| `productRevision` | String | Yes (proposed) | Revision identifier for the product definition. | Format, effectivity and uniqueness OPEN. | No | Candidate compound productNo+productRevision | Revision-aware manufacturing and traceability. | Inventory specifies revision; SAP master versions | BOM/routing/order traceability |
| `status` | String | Yes (proposed) | Lifecycle state for product revision selection. | Vocabulary/default/transitions OPEN. | No | Candidate status filter | Lifecycle control. | Project lifecycle rule OPEN | Production selection |

## F. Field Groups

- **Identity and business code:** _id, productNo, productName, productRevision
- **Lifecycle:** status
- **Audit:** createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy

## G. ID–Name Reference Pairs

No business-entity references; audit actor pairs still apply.

## H. System IDs and Business Numbers

- MES `_id`: 16-digit String, globally unique.
- Business/source identifier fields: see field dictionary. Patterns and uniqueness are OPEN except operationCode and scrapReasonCode.
- External identifiers are distinct from MES IDs.

## I. Data Types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## J. Required, Optional and Conditional Fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## K. Validation Rules

- Flat top-level scalar fields only; no nested documents or arrays.
- MES IDs are 16-digit strings; external identifiers remain separate.
- Enforce only approved code patterns and uniqueness scopes. Do not invent OPEN scopes or status vocabularies.
- Required ID/display pairs are complete and consistent; optional pairs are both absent or both populated.
- Audit actor pairs are complete; updatedAt >= createdAt.
- Retired reference records remain historically resolvable when established by decision.

## L. Status Values and State Transitions

Product lifecycle vocabulary and transitions OPEN.

## M. Relationships with Other Collections

**Outgoing:** No direct Product→Material reference; BOM records define composition.  
**Incoming:** BOM, routing, production orders/work orders, lot/serial, quality, planning, traceability and analytics.  
**Dependencies:** Product/Material distinction is approved; BOM composition and revision/effectivity rules apply.

## N. External System Integration

External product system-of-record and key crosswalk are OPEN.

## O. Recommended Indexes

- Default unique _id index.
- Candidate productNo lookup; uniqueness pending — Order and product selection; verify query frequency and selectivity before implementation.
- Optional productName search — Shop-floor and reporting; verify query frequency and selectivity before implementation.
- Candidate compound productNo+productRevision — BOM/routing/order traceability; verify query frequency and selectivity before implementation.
- Candidate status filter — Production selection; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## P. Audit and Historical Data

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## Q. Real-World Manufacturing Exceptions

- Retired/obsolete reference values must not invalidate historical transactions where project decisions establish historical resolution.
- Rejected or unmapped source records should remain identifiable for correction; exact workflow is OPEN.
- No state transitions, error-code taxonomies or extra fields are invented here.

## R. Missing Field Assessment

No additional field is added without requirements. The key gaps remain: Product number namespace, revision format/effectivity, lifecycle states, ERP identity mapping, plant applicability, and composition rules. Product and Material IDs must not be equated.

## S. Missing Collection Assessment

No new collection is added by this specification. The current inventory remains the collection-set authority. Any repeated item or missing owner discovered later must be reported for inventory/change control; arrays remain prohibited.

## T. Cross-Collection Consistency Check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: PASS — no direct Material identity reference.
- Status vocabulary: OPEN — no approved product status vocabulary.
- Unresolved dependencies: see Section N; no silent resolution.

## U. Field Completeness Metrics

- Proposed fields total: 12
- Required by convention/approved decision: 8
- Proposed required: 3
- Optional: 1
- Entity reference pairs (excluding audit pairs): 0
- Status/control fields: 1
- Audit fields: 6
- External identifier/locator fields: 0
- Scenarios evaluated: 5
- Scenarios fully established by detailed project requirements: 0 (collection-specific requirements were unavailable)
- Research-backed field rows: 11 (evidence is shown per field; estimates are not a completeness claim)
- Unresolved functional/design gaps: see Sections R and X.

Scenario Coverage Percentage: 0% established from detailed requirements; this is a documentation-evidence metric, not a claim of functional failure.

Field list: _id, createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy, productNo, productName, productDescription, productRevision, status

## V. One Representative MongoDB Document

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  productNo: "<productNo>",
  productName: "<productName>",
  productDescription: "<productDescription>",
  productRevision: "<productRevision>",
  status: "<status>",
  createdAt: ISODate("2026-06-01T08:00:00.000+0000"),
  createdById: "1000000000000002",
  createdBy: "MES Configuration Service",
  updatedAt: ISODate("2026-06-01T08:00:00.000+0000"),
  updatedById: "1000000000000002",
  updatedBy: "MES Configuration Service"
}

## W. Assumptions

- The current inventory purpose and module owner bound this design.
- ID/display duplication is intentional strategic denormalization; copied-name synchronization policy remains OPEN unless decided.
- APPROVED is the requested document status; it does not close OPEN business rules.

## X. Open Issues

Product number namespace, revision format/effectivity, lifecycle states, ERP identity mapping, plant applicability, and composition rules. Product and Material IDs must not be equated.

## Y. Research Sources

- [SAP Product Master](https://help.sap.com/docs/SAP_S4HANA_ON-PREMISE/4dd8cb7b1c484b4b93af84d00f60fdb8/b724ba53422bb54ce10000000a174cb4.html) — Describes product master as central logistics data used in production planning; project DEC-022 keeps Product and Material distinct.
- [SAP Digital Manufacturing Manage Materials](https://help.sap.com/docs/sap-digital-manufacturing/execution/manage-materials) — Shows manufacturing masters can include version, routing/BOM and production settings; not a direct schema template.

## Z. Final Design Review

Status label: APPROVED under Project Owner instruction. This is not production readiness.

Covered: inventory-defined purpose, identified fields, types, references, research evidence, and flat representation.

Remaining: OPEN items in Section X; no unsupported lifecycle, code, source, or integration rule is treated as resolved.



