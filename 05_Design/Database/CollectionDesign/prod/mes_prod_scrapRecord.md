**FILE TYPE: PROJECT SPECIFIC**

# mes_prod_scrapRecord — Collection Specification

**Document ID:** MES-COL-PROD-scrapRecord  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** prod  
**Collection type:** Transaction  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Records scrap quantity/identity, controlled reason, authorization, and originating execution.

## B. Business definition

Each Scrap Record is a controlled disposition distinct from production output and rework.

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
| `scrapRecordNo` | String | Yes | Approved scrap number. | SC-YYYY-###### globally unique (DEC-013). | No | Unique scrapRecordNo | Audit | DEC-013 | Retrieval |
| `workOrderId` | String | Yes | Originating Work Order. | Pair with workOrderName. | Yes: workOrderId + workOrderName -> mes_prod_workOrder | Candidate workOrderId+createdAt | Production context | DEC-004 | Scrap history |
| `workOrderName` | String | Yes | Work Order snapshot. | Paired with workOrderId. | Yes: paired with workOrderId | No index | Readable context | DATA-003 | Display |
| `scrapReasonId` | String | Yes | Controlled Scrap Reason. | Pair with scrapReasonName. | Yes: scrapReasonId + scrapReasonName -> mes_prod_scrapReason | Candidate scrapReasonId+createdAt | Disposition category | DEC-009 | Loss analysis |
| `scrapReasonName` | String | Yes | Scrap Reason snapshot. | Paired with scrapReasonId. | Yes: paired with scrapReasonId | No index | Readable reason | DATA-003 | Display |
| `quantity` | Double | Conditional | Scrapped quantity when quantity-based. | Non-negative; UOM and precision OPEN. | No | No index | Loss quantity | DEC-010 | Scrap analysis |
| `unitOfMeasureId` | String | Conditional | Quantity unit. | If quantity set pair with unitOfMeasureName. | Yes: unitOfMeasureId + unitOfMeasureName -> mes_matl_unitOfMeasure | No index | Quantity semantics | DEC-022 | Measurement |
| `unitOfMeasureName` | String | Conditional | UOM snapshot. | Paired with unitOfMeasureId. | Yes: paired with unitOfMeasureId | No index | Readable unit | DATA-003 | Display |
| `status` | String | Yes | Scrap disposition lifecycle. | Approved values per DEC-011. | No | Candidate status+createdAt | Authorization/disposition | DEC-011 | Review |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `workOrderId` — Yes: workOrderId + workOrderName -> mes_prod_workOrder.
- `workOrderName` — Yes: paired with workOrderId.
- `scrapReasonId` — Yes: scrapReasonId + scrapReasonName -> mes_prod_scrapReason.
- `scrapReasonName` — Yes: paired with scrapReasonId.
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

Use approved Scrap Record values in DEC-011.

## I. Relationships

**Outgoing:** Work Order, Scrap Reason, optional UOM and trace identity.  
**Incoming:** Traceability and performance.  
**Dependencies:** Scrap Reason; Work Order; trace identity; quantity/UOM and authorization rules.

## J. Recommended indexes

- Default unique _id index.
- Unique scrapRecordNo — Retrieval; verify query frequency and selectivity before implementation.
- Candidate workOrderId+createdAt — Scrap history; verify query frequency and selectivity before implementation.
- Candidate scrapReasonId+createdAt — Loss analysis; verify query frequency and selectivity before implementation.
- Candidate status+createdAt — Review; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  scrapRecordNo: "<scrapRecordNo>",
  workOrderId: "<workOrderId>",
  workOrderName: "<workOrderName>",
  scrapReasonId: "<scrapReasonId>",
  scrapReasonName: "<scrapReasonName>",
  quantity: <value>,
  unitOfMeasureId: "<unitOfMeasureId>",
  unitOfMeasureName: "<unitOfMeasureName>",
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

Serial-vs-lot grain; operation/nonconformance link; quantity requiredness; authorization role; correction/reversal and external posting.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: Targets exist; operation and trace link details OPEN.
- Status vocabulary: PASS — DEC-011.
- Unresolved dependencies: see Section N; no silent resolution.

## P. Audit and Historical Data

The standard audit fields are defined in Section K. Preserve source chronology and corrections under approved project controls; retention, immutability and event-reversal policy remain OPEN where not decided.

## Q. Real-World Manufacturing Exceptions

Consider late and duplicate messages, incomplete source context, retries, overnight shifts, corrections and partial completion when applicable. Do not use nested data or scalar-encoded structured payloads to represent repeated facts.

## R. Missing Field Assessment

The current inventory bounds collection scope. Known field/workflow gaps remain: Serial-vs-lot grain; operation/nonconformance link; quantity requiredness; authorization role; correction/reversal and external posting.. Raise additional needs through requirements/change control rather than adding fields silently.

## S. Missing Collection Assessment

No collection is added by this specification. Repeated concepts remain separate flat records. Where an owning entity, item identity, or relationship record is absent, retain the gap for inventory review.

## T. Cross-Collection Consistency Check

- Inventory name/module and purpose checked against the current collection list.
- Flat scalar representation, singular key naming, ID/display pairs, and standard audit fields checked.
- Reference targets and lifecycle/type gaps are called out in Sections N, O, and X; unresolved items remain OPEN.

## U. Field Completeness Metrics

- Field names listed in the dictionary: 16, including system ID and audit fields.
- Entity reference pairs are enumerated in Section F; audit pairs are listed in Section K.
- Scenario and source evidence is collection-specific where documented; no unsupported requirement coverage percentage is claimed.

## V. One Representative MongoDB Document

The single illustrative flat structure is in Section L. No sample document set is generated in this phase.

## W. Assumptions

- Current project context, functional inventory, requirements and approved decisions bound this design.
- ID/display duplication is intentional strategic denormalization.
- APPROVED is the requested specification status; OPEN items remain unresolved and this label does not establish production readiness.

## X. Open Issues

Serial-vs-lot grain; operation/nonconformance link; quantity requiredness; authorization role; correction/reversal and external posting.

## Y. Research Sources

See Research basis below. Sources support manufacturing workflows and scenarios; they do not approve unresolved project rules or transfer vendor schemas.

## Z. Final Design Review

Status label: APPROVED under prior Project Owner instruction. OPEN items remain unresolved. Flatness, reference conventions, inventory coverage and research evidence are summarized in Sections O and T.
## Research basis
- [SAP Digital Manufacturing Scrap Confirmation](https://help.sap.com/docs/sap-digital-manufacturing/integration-guide/scrap-confirmation-for-production-order) — Scrap confirmations report quantity and consumed components against a production order.
- [SAP OEE Reason Codes](https://help.sap.com/docs/sap-digital-manufacturing/execution/assign-oee-reason-codes) — Reason codes classify reported loss and scrap for operational analysis.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.

