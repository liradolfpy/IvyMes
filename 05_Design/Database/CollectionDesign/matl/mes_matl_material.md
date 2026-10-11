**FILE TYPE: PROJECT SPECIFIC**

# mes_matl_material — Collection Specification

**Document ID:** MES-COL-MATL-material  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** matl  
**Collection type:** Master  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection Overview

**Collection:** mes_matl_material
**Module:** Material Management
**Type:** Master
**Primary business entity:** Material
**Purpose:** Maintains the identity and lifecycle of materials used as components, consumables, or inventory-managed items.
**Business definition:** This collection stores the inventory-defined Material identity or reference, owned by Material Management, for the stated purpose. It does not imply additional parentage or field rules beyond current project decisions.
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
| `materialNo` | String | Yes (proposed) | Business/source number for the material. | Pattern and uniqueness scope OPEN outside DEC-013. | No | Candidate materialNo lookup; uniqueness pending | BOM and inventory identification. | Inventory; SAP Manage Materials | BOM, inventory, reservation, consumption |
| `materialName` | String | Yes | Human-readable material name. | Non-empty. | No | Optional materialName search | Readable material identity. | SAP Manage Materials | Operator/planner display |
| `materialDescription` | String | No | Scalar material description. | Plain text. | No | No index by default | Additional detail. | SAP Manage Materials | Material lookup |
| `materialType` | String | No | Material classification from source/project taxonomy. | Vocabulary and source mapping OPEN. | No entity reference | Candidate filter after taxonomy definition | Distinguish material use where requirements establish categories. | SAP Manage Materials documents material types | Inventory/BOM handling |
| `baseUnitOfMeasureId` | String | Yes (proposed) | MES ID of base unit in mes_matl_unitOfMeasure. | 16-digit ID resolving to UOM. | Yes: mes_matl_unitOfMeasure | Non-unique reference index | Controlled unit for material quantities. | DEC-022; SAP Manage Materials | Inventory/BOM/consumption quantities |
| `baseUnitOfMeasureName` | String | Yes (proposed) | Display name paired with baseUnitOfMeasureId. | Required with ID; matching display snapshot. | Yes: ID/display pair | No index by default | Avoid routine display lookup. | DATA-003; SAP Manage Materials | Quantity display |
| `status` | String | Yes (proposed) | Lifecycle state for new material transactions. | Values/default/transitions OPEN. | No | Candidate status filter | Retire while retaining historical use. | Project lifecycle rule OPEN | Material selection |

## F. Field Groups

- **Identity and business code:** _id, materialNo, materialName, baseUnitOfMeasureName
- **Lifecycle:** status
- **Audit:** createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy

## G. ID–Name Reference Pairs

- `baseUnitOfMeasureId` — Yes: mes_matl_unitOfMeasure.
- `baseUnitOfMeasureName` — Yes: ID/display pair.

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

Material status vocabulary/transitions OPEN.

## M. Relationships with Other Collections

**Outgoing:** baseUnitOfMeasureId + baseUnitOfMeasureName → mes_matl_unitOfMeasure.  
**Incoming:** BOM components, inventory, reservations, movement/consumption, WIP, quality and maintenance spare parts.  
**Dependencies:** UOM collection is in same dependency wave; external material source and material type vocabulary OPEN.

## N. External System Integration

External material system-of-record and key crosswalk are OPEN.

## O. Recommended Indexes

- Default unique _id index.
- Candidate materialNo lookup; uniqueness pending — BOM, inventory, reservation, consumption; verify query frequency and selectivity before implementation.
- Optional materialName search — Operator/planner display; verify query frequency and selectivity before implementation.
- Candidate filter after taxonomy definition — Inventory/BOM handling; verify query frequency and selectivity before implementation.
- Non-unique reference index — Inventory/BOM/consumption quantities; verify query frequency and selectivity before implementation.
- Candidate status filter — Material selection; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## P. Audit and Historical Data

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## Q. Real-World Manufacturing Exceptions

- Retired/obsolete reference values must not invalidate historical transactions where project decisions establish historical resolution.
- Rejected or unmapped source records should remain identifiable for correction; exact workflow is OPEN.
- No state transitions, error-code taxonomies or extra fields are invented here.

## R. Missing Field Assessment

No additional field is added without requirements. The key gaps remain: Material number namespace, taxonomy, revision/effectivity, UOM conversion, lot/serial policy, external owner and retirement behavior.

## S. Missing Collection Assessment

No new collection is added by this specification. The current inventory remains the collection-set authority. Any repeated item or missing owner discovered later must be reported for inventory/change control; arrays remain prohibited.

## T. Cross-Collection Consistency Check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: CONDITIONAL — verify UOM reference pair against its specification.
- Status vocabulary: OPEN — no approved material status vocabulary.
- Unresolved dependencies: see Section N; no silent resolution.

## U. Field Completeness Metrics

- Proposed fields total: 14
- Required by convention/approved decision: 8
- Proposed required: 4
- Optional: 2
- Entity reference pairs (excluding audit pairs): 2
- Status/control fields: 1
- Audit fields: 6
- External identifier/locator fields: 0
- Scenarios evaluated: 5
- Scenarios fully established by detailed project requirements: 0 (collection-specific requirements were unavailable)
- Research-backed field rows: 13 (evidence is shown per field; estimates are not a completeness claim)
- Unresolved functional/design gaps: see Sections R and X.

Scenario Coverage Percentage: 0% established from detailed requirements; this is a documentation-evidence metric, not a claim of functional failure.

Field list: _id, createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy, materialNo, materialName, materialDescription, materialType, baseUnitOfMeasureId, baseUnitOfMeasureName, status

## V. One Representative MongoDB Document

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  materialNo: "<materialNo>",
  materialName: "<materialName>",
  materialDescription: "<materialDescription>",
  materialType: "<materialType>",
  baseUnitOfMeasureId: "<baseUnitOfMeasureId>",
  baseUnitOfMeasureName: "<baseUnitOfMeasureName>",
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

Material number namespace, taxonomy, revision/effectivity, UOM conversion, lot/serial policy, external owner and retirement behavior.

## Y. Research Sources

- [SAP Digital Manufacturing Manage Materials](https://help.sap.com/docs/sap-digital-manufacturing/execution/manage-materials) — Describes material as unique manufactured/purchased item processed or consumed on shop floor, with base UOM and BOM/routing integration.

## Z. Final Design Review

Status label: APPROVED under Project Owner instruction. This is not production readiness.

Covered: inventory-defined purpose, identified fields, types, references, research evidence, and flat representation.

Remaining: OPEN items in Section X; no unsupported lifecycle, code, source, or integration rule is treated as resolved.



