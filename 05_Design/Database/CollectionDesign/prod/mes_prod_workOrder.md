**FILE TYPE: PROJECT SPECIFIC**

# mes_prod_workOrder — Collection Specification

**Document ID:** MES-COL-PROD-workOrder  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** prod  
**Collection type:** Transaction  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Represents the MES-managed granular execution task derived from a Production Order, defining how/where specific operations are carried out and tracking shop-floor progress.

## B. Business definition

Work Order is the micro-level execution entity distinct from Production Order under DEC-004. A Production Order may produce multiple Work Orders; Work Orders track operation, workstation, operator, material, time, good/scrap/rework execution context.

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
| `workOrderNo` | String | Yes | Approved business number for MES Work Order. | WO-YYYY-######; globally unique in collection across statuses per DEC-013. | No | Unique workOrderNo | Execution identity. | DEC-013; DEC-004 | Shop-floor task tracking |
| `productionOrderId` | String | Yes | Parent macro Production Order. | 16-digit ID resolving mes_prod_productionOrder. | Yes: productionOrderId + productionOrderName -> mes_prod_productionOrder | Candidate productionOrderId+status | Preserve parent-child order hierarchy. | DEC-004; DATA-003 | Progress rollup |
| `productionOrderName` | String | Yes | Production Order display/business-number snapshot. | Paired with productionOrderId; exact display value convention OPEN. | Yes: paired with productionOrderId | No index by default | Readable parent reference. | DATA-003 | Execution UI |
| `productId` | String | Yes | Product executed by this Work Order. | 16-digit ID resolving mes_matl_product. | Yes: productId + productName -> mes_matl_product | Candidate productId+status | Execution context. | DEC-004; DATA-003 | Production execution |
| `productName` | String | Yes | Product display snapshot. | Paired with productId. | Yes: paired with productId | No index by default | Readable product context. | DATA-003 | Execution display |
| `plantId` | String | Yes (proposed) | Plant where execution occurs. | 16-digit ID paired with plantName. | Yes: plantId + plantName -> mes_plan_plant | Candidate plant+status | Shop-floor scope. | DEC-022; inventory | Dispatch |
| `plantName` | String | Yes (proposed) | Plant display snapshot. | Paired with plantId. | Yes: paired with plantId | No index by default | Readable scope. | DATA-003 | Work list |
| `plannedQuantity` | Double | Yes | Execution quantity assigned to this Work Order. | BSON Double; units, precision and rounding per approved requirements; DEC-010. | No | No index by default | Granular execution workload. | DEC-010; DEC-004 | Execution tracking |
| `status` | String | Yes | Work Order lifecycle state. | Draft, Released, InProgress, OnHold, Completed, Cancelled (DEC-011). | No | Candidate status+plant | Track execution task lifecycle. | DEC-011; DEC-004 | Work order execution |
| `plannedStart` | Date | Conditional | Planned execution start. | BSON Date; <= plannedEnd if both present. | No | Candidate plant+status+plannedStart | Schedule work. | Inventory; SAP Order POD | Work queue |
| `plannedEnd` | Date | Conditional | Planned execution end/due time. | BSON Date >= plannedStart. | No | Candidate plant+status+plannedEnd | Manage due work. | Inventory; SAP Order POD | Work queue |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `productionOrderId` — Yes: productionOrderId + productionOrderName -> mes_prod_productionOrder.
- `productionOrderName` — Yes: paired with productionOrderId.
- `productId` — Yes: productId + productName -> mes_matl_product.
- `productName` — Yes: paired with productId.
- `plantId` — Yes: plantId + plantName -> mes_plan_plant.
- `plantName` — Yes: paired with plantId.

## G. Validation rules

- Flat top-level scalar fields only; no nested documents or arrays.
- MES IDs are 16-digit strings; external identifiers remain separate.
- Enforce only approved code patterns and uniqueness scopes. Do not invent OPEN scopes or status vocabularies.
- Required ID/display pairs are complete and consistent; optional pairs are both absent or both populated.
- Audit actor pairs are complete; updatedAt >= createdAt.
- Retired reference records remain historically resolvable when established by decision.

## H. Status values

Approved collection-specific values per DEC-011. Cross-level transition behavior with Production Order remains OPEN.

## I. Relationships

**Outgoing:** Parent Production Order, Product and Plant pairs; operation-level work is linked through separate Routing Operation, Dispatch Assignment and Operation Execution records.  
**Incoming:** Dispatch Assignment, Operation Execution, production reporting, material, quality, traceability and workforce transactions.  
**Dependencies:** Production Order, Product, Plant; routing/operation and dispatch records provide granular operation execution. Order quantity reconciliation is unresolved.

## J. Recommended indexes

- Default unique _id index.
- Unique workOrderNo — Shop-floor task tracking; verify query frequency and selectivity before implementation.
- Candidate productionOrderId+status — Progress rollup; verify query frequency and selectivity before implementation.
- Candidate productId+status — Production execution; verify query frequency and selectivity before implementation.
- Candidate plant+status — Dispatch; verify query frequency and selectivity before implementation.
- Candidate status+plant — Work order execution; verify query frequency and selectivity before implementation.
- Candidate plant+status+plannedStart — Work queue; verify query frequency and selectivity before implementation.
- Candidate plant+status+plannedEnd — Work queue; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  workOrderNo: "<workOrderNo>",
  productionOrderId: "<productionOrderId>",
  productionOrderName: "<productionOrderName>",
  productId: "<productId>",
  productName: "<productName>",
  plantId: "<plantId>",
  plantName: "<plantName>",
  plannedQuantity: <value>,
  status: "<status>",
  plannedStart: <value>,
  plannedEnd: <value>,
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

WorkOrder parent order cardinality and split quantity; `productionOrderName` display semantics (order number vs display name); order-specific routing snapshot; plant/UOM inheritance; assignments/work-center boundary; quantity type and rounding; status transition rules.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: PASS — Production Order/Product/Plant references use ID/display pairs.
- Status vocabulary: PASS — exact values from DEC-011.
- Unresolved dependencies: see Section N; no silent resolution.


## P. Audit and Historical Data

Standard audit fields remain flat and paired. Preserve production-order/work-order lineage, planning versions, schedule publication, and execution history according to approved retention rules; where not specified, retention remains OPEN.

## Q. Real-World Manufacturing Exceptions

Handle partial order splits, holds, cancellations, schedule conflicts, stale input versions, and integration failures without merging production and work-order concepts. Exact correction and reconciliation rules remain OPEN where not covered by decisions.

## R. Missing Field Assessment

Collection-specific requirements are incomplete for some planning and schedule semantics. Review with planning, production control, shop-floor operations, integration, and database roles. Known gaps: WorkOrder parent order cardinality and split quantity; `productionOrderName` display semantics (order number vs display name); order-specific routing snapshot; plant/UOM inheritance; assignments/work-center boundary; quantity type and rounding; status transition rules.

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

See Sections E, F, and J for fields, references, and indexes. Detailed planning requirements are not fully linked; no complete scenario coverage percentage is claimed. Known gaps: WorkOrder parent order cardinality and split quantity; `productionOrderName` display semantics (order number vs display name); order-specific routing snapshot; plant/UOM inheritance; assignments/work-center boundary; quantity type and rounding; status transition rules.

## V. One Representative MongoDB Document

The sole illustrative flat structure appears in Section L. No additional sample documents are created.

## W. Assumptions

- Current inventory and approved decisions are authoritative.
- Repeated items and schedule placements require separate flat records.
- ID/name duplication is intentional strategic denormalization.
- APPROVED follows the Project Owner's prior instruction; OPEN questions remain unapproved.

## X. Open Issues

WorkOrder parent order cardinality and split quantity; `productionOrderName` display semantics (order number vs display name); order-specific routing snapshot; plant/UOM inheritance; assignments/work-center boundary; quantity type and rounding; status transition rules.

## Y. Research Sources

Collection-specific public references and the operational questions they support are recorded in the Research basis section below. They do not override project decisions.

## Z. Final Design Review

- **Status:** APPROVED per prior Project Owner instruction; not production readiness.
- **Covered:** inventory-bounded purpose, core fields, approved decision alignment, relationships, research, and gaps.
- **Remaining:** Section X; schedule-placement and quantity/business semantics must be resolved before implementation where marked blocking.
## Research basis

- [SAP Digital Manufacturing Order POD](https://help.sap.com/docs/sap-digital-manufacturing/execution/order-pod) — Execution order view supports operation-level starts/completions, quantity confirmations, scrap, materials and work instructions.
- [SAP Digital Manufacturing Resource Orchestration](https://help.sap.com/docs/sap_digital_manufacturing/066baf723b0f40d7bce61590ebcec2ef) — Shop-floor scheduling/dispatch allocates operations and monitors availability; supports separate Work Order execution tasks.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


