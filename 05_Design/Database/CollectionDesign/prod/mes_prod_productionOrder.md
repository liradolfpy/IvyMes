**FILE TYPE: PROJECT SPECIFIC**

# mes_prod_productionOrder — Collection Specification

**Document ID:** MES-COL-PROD-productionOrder  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** prod  
**Collection type:** Transaction  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Represents the ERP-originated or integrated macro-level authorization to produce a Product quantity by a required date and tracks overall batch progress.

## B. Business definition

Production Order is distinct from Work Order under DEC-004: it answers what/how much/by when at batch level; it is normally created by ERP and passed to MES. Work Orders are granular MES execution tasks derived from it.

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
| `productionOrderNo` | String | Yes | Approved human-readable Production Order number. | PO-YYYY-######; globally unique within collection across statuses per DEC-013. | External ERP number remains separate if source ID differs. | Unique productionOrderNo | Business-facing order identity. | DEC-013 | Order traceability |
| `externalProductionOrderId` | String | Conditional | Source-system order identifier if distinct from productionOrderNo. | Source namespace required; never use as _id. | External identifier. | Candidate source+external ID | ERP reconciliation. | DEC-004; DATA-002 | Integration |
| `productId` | String | Yes | Product authorized for manufacture. | 16-digit ID resolving mes_matl_product. | Yes: productId + productName -> mes_matl_product | Candidate productId+requiredDate | Identify output product. | DEC-004; DEC-022 | Order execution |
| `productName` | String | Yes | Product display snapshot. | Paired with productId. | Yes: paired with productId | No index by default | Avoid lookup. | DATA-003 | Order display |
| `plantId` | String | Yes (proposed) | Plant where order is planned/executed. | 16-digit ID paired with plantName. | Yes: plantId + plantName -> mes_plan_plant | Candidate plant+status+requiredDate | Operational site scope. | DEC-022; SAP order API | Shop-floor execution |
| `plantName` | String | Yes (proposed) | Plant display snapshot. | Paired with plantId. | Yes: paired with plantId | No index by default | Readable location. | DATA-003 | Order display |
| `plannedQuantity` | Double | Yes | Total authorized output quantity. | BSON Double; quantity unit, precision and rounding per approved requirements; no decimal constructor (DEC-010). | No | No index by default | Macro-level production authorization. | DEC-010; DEC-004 | Order progress |
| `unitOfMeasureId` | String | Yes (proposed) | Unit of the planned quantity. | 16-digit ID resolving mes_matl_unitOfMeasure. | Yes: unitOfMeasureId + unitOfMeasureName -> mes_matl_unitOfMeasure | Candidate UOM | Interpret order quantity. | DEC-022; DATA-003 | Quantity reporting |
| `unitOfMeasureName` | String | Yes (proposed) | Unit display snapshot. | Paired with unitOfMeasureId. | Yes: paired with unitOfMeasureId | No index by default | Readable quantity. | DATA-003 | Order display |
| `requiredDate` | Date | Yes (proposed) | Date production order is required by. | BSON Date; business timezone semantics OPEN. | No | Candidate plant+status+requiredDate | Macro planning horizon. | DEC-004; SAP order definition | Due-date tracking |
| `status` | String | Yes | Production Order lifecycle state. | PendingRelease, Released, OnHold, Cancelled, Completed (DEC-011). | No | Candidate status+requiredDate | Track batch-level lifecycle. | DEC-011; DEC-004 | Order progression |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `productId` — Yes: productId + productName -> mes_matl_product.
- `productName` — Yes: paired with productId.
- `plantId` — Yes: plantId + plantName -> mes_plan_plant.
- `plantName` — Yes: paired with plantId.
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

Approved collection-specific values per DEC-011. State transitions and rules for parent status vs work-order states remain open under DEC-004.

## I. Relationships

**Outgoing:** Product, Plant, UOM pairs; source ERP ID is external scalar.  
**Incoming:** Work Orders, dispatch, operation execution, production reports, traceability, materials, quality and analytics may reference the macro order.  
**Dependencies:** Product, Plant, UOM, external ERP source; Work Order is a separate child execution entity.

## J. Recommended indexes

- Default unique _id index.
- Unique productionOrderNo — Order traceability; verify query frequency and selectivity before implementation.
- Candidate source+external ID — Integration; verify query frequency and selectivity before implementation.
- Candidate productId+requiredDate — Order execution; verify query frequency and selectivity before implementation.
- Candidate plant+status+requiredDate — Shop-floor execution; verify query frequency and selectivity before implementation.
- Candidate UOM — Quantity reporting; verify query frequency and selectivity before implementation.
- Candidate plant+status+requiredDate — Due-date tracking; verify query frequency and selectivity before implementation.
- Candidate status+requiredDate — Order progression; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  productionOrderNo: "<productionOrderNo>",
  externalProductionOrderId: "<externalProductionOrderId>",
  productId: "<productId>",
  productName: "<productName>",
  plantId: "<plantId>",
  plantName: "<plantName>",
  plannedQuantity: <value>,
  unitOfMeasureId: "<unitOfMeasureId>",
  unitOfMeasureName: "<unitOfMeasureName>",
  requiredDate: <value>,
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

External source ownership/identity; required UOM and plant rules; requiredDate instant vs due-day; partial splits and aggregate progress calculation; parent/child order quantity reconciliation; cancellation/hold semantics.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: PASS — Product, Plant and UOM use required ID/name pairs.
- Status vocabulary: PASS — exact values from DEC-011.
- Unresolved dependencies: see Section N; no silent resolution.


## P. Audit and Historical Data

Standard audit fields remain flat and paired. Preserve production-order/work-order lineage, planning versions, schedule publication, and execution history according to approved retention rules; where not specified, retention remains OPEN.

## Q. Real-World Manufacturing Exceptions

Handle partial order splits, holds, cancellations, schedule conflicts, stale input versions, and integration failures without merging production and work-order concepts. Exact correction and reconciliation rules remain OPEN where not covered by decisions.

## R. Missing Field Assessment

Collection-specific requirements are incomplete for some planning and schedule semantics. Review with planning, production control, shop-floor operations, integration, and database roles. Known gaps: External source ownership/identity; required UOM and plant rules; requiredDate instant vs due-day; partial splits and aggregate progress calculation; parent/child order quantity reconciliation; cancellation/hold semantics.

## S. Missing Collection Assessment

No collection is added. Repeated schedule placements/operation assignments are not represented as arrays; the inventory currently lacks an identified flat placement owner. Raise that gap for inventory review if schedule details are required.

## T. Cross-Collection Consistency Check

- Inventory scope/module/type checked against v1.6.
- Production Order and Work Order remain distinct per DEC-004.
- Approved PROD number patterns and lifecycle values follow DEC-013/DEC-011 where applicable.
- DEC-010 numeric types are used for the specified PROD plannedQuantity fields only.
- Entity references use singular ID/name pairs; unresolved plan/schedule relationships remain OPEN.
- Flat-document rule respected.

## U. Field Completeness Metrics

See Sections E, F, and J for fields, references, and indexes. Detailed planning requirements are not fully linked; no complete scenario coverage percentage is claimed. Known gaps: External source ownership/identity; required UOM and plant rules; requiredDate instant vs due-day; partial splits and aggregate progress calculation; parent/child order quantity reconciliation; cancellation/hold semantics.

## V. One Representative MongoDB Document

The sole illustrative flat structure appears in Section L. No additional sample documents are created.

## W. Assumptions

- Current inventory and approved decisions are authoritative.
- Repeated items and schedule placements require separate flat records.
- ID/name duplication is intentional strategic denormalization.
- APPROVED follows the Project Owner's prior instruction; OPEN questions remain unapproved.

## X. Open Issues

External source ownership/identity; required UOM and plant rules; requiredDate instant vs due-day; partial splits and aggregate progress calculation; parent/child order quantity reconciliation; cancellation/hold semantics.

## Y. Research Sources

Collection-specific public references and the operational questions they support are recorded in the Research basis section below. They do not override project decisions.

## Z. Final Design Review

- **Status:** APPROVED per prior Project Owner instruction; not production readiness.
- **Covered:** inventory-bounded purpose, core fields, approved decision alignment, relationships, research, and gaps.
- **Remaining:** Section X; schedule-placement and quantity/business semantics must be resolved before implementation where marked blocking.
## Research basis

- [SAP Business ByDesign Production Order Execution](https://help.sap.com/docs/SAP_BUSINESS_BYDESIGN/2754875d2d2a403f95e58a41a9c7d6de/2ced5aba722d1014bb07e3be53a8d13b.html) — Production order authorizes a defined quantity within a time and release creates production lot/tasks; supports DEC-004 boundary as external context.
- [SAP Digital Manufacturing Order API](https://help.sap.com/docs/sap-digital-manufacturing/apis/order) — Order details include material, plant, dates, required quantity and operation sequence; lifecycle endpoints include release/complete/discard.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


