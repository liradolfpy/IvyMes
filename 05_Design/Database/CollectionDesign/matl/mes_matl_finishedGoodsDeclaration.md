**FILE TYPE: PROJECT SPECIFIC**

# mes_matl_finishedGoodsDeclaration — Collection Specification

**Document ID:** MES-COL-MATL-finishedGoodsDeclaration  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** matl  
**Collection type:** Transaction  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Declares finished-goods output and destination stock context.

## B. Business definition

Bridges completed production to stock; distinct from Production Report and Material Movement.

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
| `workOrderId` | String | Yes | Producing Work Order. | Pair with workOrderName. | Yes: workOrderId + workOrderName -> mes_prod_workOrder | Candidate workOrderId+declaredAt | Production source | DEC-004 | Receipt |
| `workOrderName` | String | Yes | Work Order snapshot. | Paired with workOrderId. | Yes: paired with workOrderId | No index | Readable order | DATA-003 | Display |
| `productId` | String | Yes | Finished Product. | Pair with productName. | Yes: productId + productName -> mes_matl_product | Candidate productId+declaredAt | Output identity | DEC-022 | Inventory |
| `productName` | String | Yes | Product snapshot. | Paired with productId. | Yes: paired with productId | No index | Readable product | DATA-003 | Display |
| `quantity` | Double | Yes | Declared amount. | Positive; UOM and precision OPEN. | No | No index | Receipt quantity | DEC-010 | Stock |
| `unitOfMeasureId` | String | Yes | Quantity unit. | Pair with unitOfMeasureName. | Yes: unitOfMeasureId + unitOfMeasureName -> mes_matl_unitOfMeasure | No index | Quantity meaning | DEC-022 | Posting |
| `unitOfMeasureName` | String | Yes | UOM snapshot. | Paired with unitOfMeasureId. | Yes: paired with unitOfMeasureId | No index | Readable unit | DATA-003 | Display |
| `lotId` | String | Conditional | Output Lot. | Pair with lotName when lot-controlled. | Yes: lotId + lotName -> mes_trace_lot | Candidate lotId | Trace identity | DATA-003 | Genealogy |
| `lotName` | String | Conditional | Lot snapshot. | Paired with lotId. | Yes: paired with lotId | No index | Readable lot | DATA-003 | Display |
| `declaredAt` | Date | Yes | Posting time. | BSON Date. | No | Candidate declaredAt | Chronology | SAP EWM Inventory Management | Reconciliation |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `workOrderId` — Yes: workOrderId + workOrderName -> mes_prod_workOrder.
- `workOrderName` — Yes: paired with workOrderId.
- `productId` — Yes: productId + productName -> mes_matl_product.
- `productName` — Yes: paired with productId.
- `unitOfMeasureId` — Yes: unitOfMeasureId + unitOfMeasureName -> mes_matl_unitOfMeasure.
- `unitOfMeasureName` — Yes: paired with unitOfMeasureId.
- `lotId` — Yes: lotId + lotName -> mes_trace_lot.
- `lotName` — Yes: paired with lotId.

## G. Validation rules

- Flat top-level scalar fields only; no nested documents or arrays.
- MES IDs are 16-digit strings; external identifiers remain separate.
- Enforce only approved code patterns and uniqueness scopes. Do not invent OPEN scopes or status vocabularies.
- Required ID/display pairs are complete and consistent; optional pairs are both absent or both populated.
- Audit actor pairs are complete; updatedAt >= createdAt.
- Retired reference records remain historically resolvable when established by decision.

## H. Status values

No lifecycle values established; reversal semantics OPEN.

## I. Relationships

**Outgoing:** Work Order, Product, UOM, optional Lot and destination location.  
**Incoming:** Inventory, traceability, analytics.  
**Dependencies:** Order, product, trace identity, UOM, receiving location, posting rules.

## J. Recommended indexes

- Default unique _id index.
- Candidate workOrderId+declaredAt — Receipt; verify query frequency and selectivity before implementation.
- Candidate productId+declaredAt — Inventory; verify query frequency and selectivity before implementation.
- Candidate lotId — Genealogy; verify query frequency and selectivity before implementation.
- Candidate declaredAt — Reconciliation; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  workOrderId: "<workOrderId>",
  workOrderName: "<workOrderName>",
  productId: "<productId>",
  productName: "<productName>",
  quantity: <value>,
  unitOfMeasureId: "<unitOfMeasureId>",
  unitOfMeasureName: "<unitOfMeasureName>",
  lotId: "<lotId>",
  lotName: "<lotName>",
  declaredAt: <value>,
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

Business number; relation to report/movement; lot vs serial scope; quality release precondition; location master owner; idempotency.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: Work Order, Product, UOM, Lot exist; location owner unresolved.
- Status vocabulary: N/A.
- Unresolved dependencies: see Section N; no silent resolution.

## P. Audit and Historical Data

The standard audit fields are defined in Section K. Preserve source chronology and corrections under approved project controls; retention, immutability and event-reversal policy remain OPEN where not decided.

## Q. Real-World Manufacturing Exceptions

Consider late and duplicate messages, incomplete source context, retries, overnight shifts, corrections and partial completion when applicable. Do not use nested data or scalar-encoded structured payloads to represent repeated facts.

## R. Missing Field Assessment

The current inventory bounds collection scope. Known field/workflow gaps remain: Business number; relation to report/movement; lot vs serial scope; quality release precondition; location master owner; idempotency.. Raise additional needs through requirements/change control rather than adding fields silently.

## S. Missing Collection Assessment

No collection is added by this specification. Repeated concepts remain separate flat records. Where an owning entity, item identity, or relationship record is absent, retain the gap for inventory review.

## T. Cross-Collection Consistency Check

- Inventory name/module and purpose checked against the current collection list.
- Flat scalar representation, singular key naming, ID/display pairs, and standard audit fields checked.
- Reference targets and lifecycle/type gaps are called out in Sections N, O, and X; unresolved items remain OPEN.

## U. Field Completeness Metrics

- Field names listed in the dictionary: 17, including system ID and audit fields.
- Entity reference pairs are enumerated in Section F; audit pairs are listed in Section K.
- Scenario and source evidence is collection-specific where documented; no unsupported requirement coverage percentage is claimed.

## V. One Representative MongoDB Document

The single illustrative flat structure is in Section L. No sample document set is generated in this phase.

## W. Assumptions

- Current project context, functional inventory, requirements and approved decisions bound this design.
- ID/display duplication is intentional strategic denormalization.
- APPROVED is the requested specification status; OPEN items remain unresolved and this label does not establish production readiness.

## X. Open Issues

Business number; relation to report/movement; lot vs serial scope; quality release precondition; location master owner; idempotency.

## Y. Research Sources

See Research basis below. Sources support manufacturing workflows and scenarios; they do not approve unresolved project rules or transfer vendor schemas.

## Z. Final Design Review

Status label: APPROVED under prior Project Owner instruction. OPEN items remain unresolved. Flatness, reference conventions, inventory coverage and research evidence are summarized in Sections O and T.
## Research basis
- [SAP Digital Manufacturing EWM Inventory Management](https://help.sap.com/docs/SAP_DIGITAL_MANUFACTURING/97c9e9b9fac74be2a023638cd1700b46/ewm-inventory-management?locale=en-US) — Goods receipt creates stock in a destination storage location.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.

