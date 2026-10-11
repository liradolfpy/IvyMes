**FILE TYPE: PROJECT SPECIFIC**

# mes_plan_productionPlan — Collection Specification

**Document ID:** MES-COL-PLAN-productionPlan  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** plan  
**Collection type:** Configuration  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Defines a planning-level production demand/plan for planned product quantities and time scope before execution orders are released.

## B. Business definition

Production Plan is the planning artifact used to organize manufacturing demand. Inventory does not define whether it is a single header or period/product line record; repeated plan lines must be separate flat records.

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
| `productionPlanCode` | String | Yes (proposed) | Business identifier for production plan. | Format and uniqueness scope OPEN. | No | Candidate plan code; uniqueness OPEN | Identify planning baseline. | Inventory; SAP production planning | Demand planning |
| `productionPlanName` | String | Yes | Readable plan name. | Non-empty. | No | Candidate text search | Planner display. | DATA-003 | Planning |
| `productId` | String | Conditional (proposed per flat product line) | Product planned for manufacture. | If present paired with productName. | Yes: productId + productName -> mes_matl_product | Candidate product+period | Identify demand item. | Inventory; DEC-022 | Plan line |
| `productName` | String | Conditional | Product display snapshot. | Both product fields present or absent. | Yes: paired with productId | No index by default | Readable plan. | DATA-003 | Planner display |
| `plannedQuantity` | Numeric BSON type OPEN | Conditional | Planned quantity for a product/period plan row. | Type, UOM, unit conversion and aggregation grain OPEN. | No | No index by default | Quantify planned demand. | Inventory; SAP production planning | Demand planning |
| `plannedStart` | Date | Conditional | Planning window start. | BSON Date; <= plannedEnd. | No | Candidate plan horizon | Time-bucket planning. | Inventory | Plan horizon |
| `plannedEnd` | Date | Conditional | Planning window end. | BSON Date >= plannedStart. | No | Candidate plan horizon | Bound plan horizon. | Inventory | Planning |
| `status` | String | Proposed | Production plan lifecycle/publication state. | Vocabulary and transitions OPEN. | No | Candidate status filter | Control plan use. | Lifecycle rules OPEN | Plan governance |

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

Collection-specific planning lifecycle; values OPEN.

## I. Relationships

**Outgoing:** Optional Product reference; product/time line record grain unresolved.  
**Incoming:** Production Order and Schedule may originate from a plan.  
**Dependencies:** Product; schedule and production order demand relationship semantics OPEN.

## J. Recommended indexes

- Default unique _id index.
- Candidate plan code; uniqueness OPEN — Demand planning; verify query frequency and selectivity before implementation.
- Candidate text search — Planning; verify query frequency and selectivity before implementation.
- Candidate product+period — Plan line; verify query frequency and selectivity before implementation.
- Candidate plan horizon — Plan horizon; verify query frequency and selectivity before implementation.
- Candidate plan horizon — Planning; verify query frequency and selectivity before implementation.
- Candidate status filter — Plan governance; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  productionPlanCode: "<productionPlanCode>",
  productionPlanName: "<productionPlanName>",
  productId: "<productId>",
  productName: "<productName>",
  plannedQuantity: <value>,
  plannedStart: <value>,
  plannedEnd: <value>,
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

BLOCKER: header vs product-period row grain and parent identity; quantity type/UOM; plan source and hierarchy; forecast vs committed demand; plan-to-order relationship/cardinality; status vocabulary; plant scope.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: OPEN — plan line grouping and order linkage unresolved.
- Status vocabulary: OPEN — no approved plan lifecycle list.
- Unresolved dependencies: see Section N; no silent resolution.


## P. Audit and Historical Data

Standard audit fields remain flat and paired. Preserve production-order/work-order lineage, planning versions, schedule publication, and execution history according to approved retention rules; where not specified, retention remains OPEN.

## Q. Real-World Manufacturing Exceptions

Handle partial order splits, holds, cancellations, schedule conflicts, stale input versions, and integration failures without merging production and work-order concepts. Exact correction and reconciliation rules remain OPEN where not covered by decisions.

## R. Missing Field Assessment

Collection-specific requirements are incomplete for some planning and schedule semantics. Review with planning, production control, shop-floor operations, integration, and database roles. Known gaps: BLOCKER: header vs product-period row grain and parent identity; quantity type/UOM; plan source and hierarchy; forecast vs committed demand; plan-to-order relationship/cardinality; status vocabulary; plant scope.

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

See Sections E, F, and J for fields, references, and indexes. Detailed planning requirements are not fully linked; no complete scenario coverage percentage is claimed. Known gaps: BLOCKER: header vs product-period row grain and parent identity; quantity type/UOM; plan source and hierarchy; forecast vs committed demand; plan-to-order relationship/cardinality; status vocabulary; plant scope.

## V. One Representative MongoDB Document

The sole illustrative flat structure appears in Section L. No additional sample documents are created.

## W. Assumptions

- Current inventory and approved decisions are authoritative.
- Repeated items and schedule placements require separate flat records.
- ID/name duplication is intentional strategic denormalization.
- APPROVED follows the Project Owner's prior instruction; OPEN questions remain unapproved.

## X. Open Issues

BLOCKER: header vs product-period row grain and parent identity; quantity type/UOM; plan source and hierarchy; forecast vs committed demand; plan-to-order relationship/cardinality; status vocabulary; plant scope.

## Y. Research Sources

Collection-specific public references and the operational questions they support are recorded in the Research basis section below. They do not override project decisions.

## Z. Final Design Review

- **Status:** APPROVED per prior Project Owner instruction; not production readiness.
- **Covered:** inventory-bounded purpose, core fields, approved decision alignment, relationships, research, and gaps.
- **Remaining:** Section X; schedule-placement and quantity/business semantics must be resolved before implementation where marked blocking.
## Research basis

- [SAP Production Planning and Control](https://help.sap.com/docs/SAP_S4HANA_CLOUD/2bba750d1e124e1ea2a039bb1cd9b6c5/9c6afca5b8714062aaa44cefc430a148.html) — Production planning and scheduling use demand, materials, dates, operations and capacity; informs planning scenarios.
- [SAP pMRP Simulation](https://help.sap.com/docs/SAP_S4HANA_CLOUD/2bba750d1e124e1ea2a039bb1cd9b6c5/f6b2c396fe4449db9573ef7654d7a0b9.html) — Planning simulations use reference data such as work centers/materials/components; used to identify plan/simulation boundary.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


