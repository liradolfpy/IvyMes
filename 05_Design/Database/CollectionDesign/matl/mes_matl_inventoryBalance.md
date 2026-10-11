**FILE TYPE: PROJECT SPECIFIC**

# mes_matl_inventoryBalance — Collection Specification

**Document ID:** MES-COL-MATL-inventoryBalance  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** matl  
**Collection type:** Transaction  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Represents the current quantity balance for a Material at a defined plant/location and optional lot/serial scope.

## B. Business definition

Inventory Balance is a balance state/read model, not a material movement transaction. Source-of-truth, refresh, and balance grain are not resolved by the inventory.

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
| `materialId` | String | Yes | Material whose stock is represented. | 16-digit ID paired with materialName. | Yes: materialId + materialName -> mes_matl_material | Candidate plant/location/material | Inventory identity. | DATA-003; inventory | Stock inquiry |
| `materialName` | String | Yes | Material display snapshot. | Paired with materialId. | Yes: paired with materialId | No index by default | Readable balance. | DATA-003 | Stock display |
| `plantId` | String | Yes (proposed) | Plant scope of balance. | Paired with plantName. | Yes: plantId + plantName -> mes_plan_plant | Candidate plant+location+material | Site stock query. | DEC-022 | Material availability |
| `plantName` | String | Yes (proposed) | Plant display snapshot. | Paired with plantId. | Yes: paired with plantId | No index by default | Readable scope. | DATA-003 | Inventory display |
| `wipLocationId` | String | Conditional (proposed) | WIP location identity if balance is location-specific. | If present pair with wipLocationName. | Yes: wipLocationId + wipLocationName -> mes_matl_wipLocation | Candidate location+material | Location-level balance. | Inventory; SAP WIP storage | WIP inquiry |
| `wipLocationName` | String | Conditional | WIP location display snapshot. | Both fields present or absent. | Yes: paired with wipLocationId | No index by default | Readable location. | DATA-003 | Inventory display |
| `onHandQuantity` | Numeric BSON type OPEN | Yes (proposed) | Current physical on-hand quantity. | Type, scale, UOM, sign, and reconciliation semantics OPEN. | No | No index by default | Availability and stock view. | Inventory; inventory management mode | Material availability |
| `unitOfMeasureId` | String | Yes (proposed) | Unit for balance quantity. | Paired with unitOfMeasureName. | Yes: unitOfMeasureId + unitOfMeasureName -> mes_matl_unitOfMeasure | Candidate UOM | Interpret quantity. | DEC-022; DATA-003 | Stock display |
| `unitOfMeasureName` | String | Yes (proposed) | UOM display snapshot. | Paired with unitOfMeasureId. | Yes: paired with unitOfMeasureId | No index by default | Avoid lookup. | DATA-003 | Quantity display |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `materialId` — Yes: materialId + materialName -> mes_matl_material.
- `materialName` — Yes: paired with materialId.
- `plantId` — Yes: plantId + plantName -> mes_plan_plant.
- `plantName` — Yes: paired with plantId.
- `wipLocationId` — Yes: wipLocationId + wipLocationName -> mes_matl_wipLocation.
- `wipLocationName` — Yes: paired with wipLocationId.
- `unitOfMeasureId` — Yes: unitOfMeasureId + unitOfMeasureName -> mes_matl_unitOfMeasure.
- `unitOfMeasureName` — Yes: paired with unitOfMeasureId.

## G. Validation rules

- Flat top-level scalar fields only; no nested documents or arrays.
- MES IDs are 16-digit strings; external identifiers remain separate.
- Enforce only approved code patterns and uniqueness scopes. Do not invent OPEN scopes or status vocabularies.
- Required ID/display pairs are complete and consistent; optional pairs are both absent or both populated.
- Audit actor pairs are complete; updatedAt >= createdAt.
- Retired reference records remain historically resolvable when established by decision.

## H. Status values

No status field proposed; stock condition/hold should be represented by approved hold records or distinct balance categories.

## I. Relationships

**Outgoing:** Material, Plant, optional WIP Location, and UOM pairs.  
**Incoming:** Material reservation, movement, consumption, production and inventory views.  
**Dependencies:** Material, Plant, UOM and optional WIP Location. Inventory source of truth may be an ERP/WMS system.

## J. Recommended indexes

- Default unique _id index.
- Candidate plant/location/material — Stock inquiry; verify query frequency and selectivity before implementation.
- Candidate plant+location+material — Material availability; verify query frequency and selectivity before implementation.
- Candidate location+material — WIP inquiry; verify query frequency and selectivity before implementation.
- Candidate UOM — Stock display; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  materialId: "<materialId>",
  materialName: "<materialName>",
  plantId: "<plantId>",
  plantName: "<plantName>",
  wipLocationId: "<wipLocationId>",
  wipLocationName: "<wipLocationName>",
  onHandQuantity: <value>,
  unitOfMeasureId: "<unitOfMeasureId>",
  unitOfMeasureName: "<unitOfMeasureName>",
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

Balance grain; source of truth; lot/serial dimension; available vs on-hand/reserved quantities; numeric BSON type and unit; synchronization/refresh; reconciliation and concurrency; status/hold separation.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: PASS — Material/Plant/UOM and optional location use ID/name pairs.
- Status vocabulary: N/A — no lifecycle field defined.
- Unresolved dependencies: see Section N; no silent resolution.


## P. Audit and Historical Data

Standard audit fields are flat and paired. Trace identities and inventory/labor history must remain resolvable over their approved retention period; source synchronization and immutability remain OPEN.

## Q. Real-World Manufacturing Exceptions

Preserve orphaned or externally mismatched trace identifiers for reconciliation; do not merge lot, serial, balance, or labor-assignment meanings. Quantity corrections and identity conflicts need controlled workflows.

## R. Missing Field Assessment

Detailed requirements are incomplete. Review with traceability, material control, workforce, production, integration, and database roles. Known gaps: Balance grain; source of truth; lot/serial dimension; available vs on-hand/reserved quantities; numeric BSON type and unit; synchronization/refresh; reconciliation and concurrency; status/hold separation.

## S. Missing Collection Assessment

No collection is added. The inventory remains authoritative. If additional identity dimensions, movement/event history, or relationship owners are needed, request inventory review rather than embedding arrays.

## T. Cross-Collection Consistency Check

- Inventory scope/type checked against v1.6.
- Product, Material, Plant, Operator, Work Order, Shift, Location and UOM pairs use project naming rules where defined.
- Lot and Serial are distinct identities; Inventory Balance is a state/balance, not a movement.
- Numeric balance type, identity namespace, and status rules remain OPEN.
- Flat-document convention respected.

## U. Field Completeness Metrics

See Sections E, F, and J for field, reference, and index counts. Detailed requirements are incomplete; no full scenario coverage percentage is claimed. Known gaps: Balance grain; source of truth; lot/serial dimension; available vs on-hand/reserved quantities; numeric BSON type and unit; synchronization/refresh; reconciliation and concurrency; status/hold separation.

## V. One Representative MongoDB Document

The sole illustrative flat structure appears in Section L; no sample documents are generated.

## W. Assumptions

- Inventory and approved ownership decisions define scope.
- Repeated relationships are separate flat records.
- ID/name duplication is intentional strategic denormalization.
- APPROVED follows prior owner direction; unresolved questions remain OPEN.

## X. Open Issues

Balance grain; source of truth; lot/serial dimension; available vs on-hand/reserved quantities; numeric BSON type and unit; synchronization/refresh; reconciliation and concurrency; status/hold separation.

## Y. Research Sources

Collection-specific public sources and their functional relevance appear under Research basis below; they do not establish project identifiers or lifecycle values.

## Z. Final Design Review

- **Status:** APPROVED per prior Project Owner instruction; not production readiness.
- **Covered:** bounded purpose, candidate fields, flat representation, references, research and known gaps.
- **Remaining:** Section X; source identity and quantity semantics must be resolved before implementation where marked blocking.
## Research basis

- [SAP Digital Manufacturing Inventory Management Mode](https://help.sap.com/docs/sap-digital-manufacturing/execution/inventory-management-mode) — Goods movement events and inventory IDs connect external stock movements to MES inventory/genealogy.
- [SAP Digital Manufacturing EWM WIP Storage](https://help.sap.com/docs/SAP_DIGITAL_MANUFACTURING/execution/afb2cef56b034488beb7f0828853d5e4.html) — WIP may be temporarily stored and managed as an inventory flow separate from production process execution.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


