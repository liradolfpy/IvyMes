**FILE TYPE: PROJECT SPECIFIC**

# mes_perf_productionLossEvent — Collection Specification

**Document ID:** MES-COL-PERF-productionLossEvent  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** perf  
**Collection type:** Transaction  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Records one classified production loss fact used in OEE and loss analysis.

## B. Business definition

Production Loss Event identifies one loss interval or quantity/time loss, with source/event linkage and a reason classification. It does not replace downtimeEvent, scrapRecord, or reworkRecord.

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
| `lossType` | String | Yes | Loss category. | Approved cross-domain mapping OPEN. | No | Candidate lossType+lossStart | Loss analysis | SAP OEE Time Model | OEE |
| `lossStart` | Date | Conditional | Start of interval loss, when time-based. | BSON Date; pairing/rules depend on loss grain. | No | Candidate lossStart | Time loss chronology | SAP OEE time elements | Time loss |
| `lossEnd` | Date | Conditional | End of interval loss, when time-based. | BSON Date >= lossStart. | No | Candidate lossEnd | Duration calculation | SAP OEE time elements | Time loss |
| `workOrderId` | String | Conditional | Optional Work Order context. | If present pair with workOrderName. | Yes: workOrderId + workOrderName -> mes_prod_workOrder | Candidate workOrderId+lossStart | Order analysis | DEC-004 | Loss reporting |
| `workOrderName` | String | Conditional | Work Order display snapshot. | Both fields absent or present. | Yes: paired with workOrderId | No index by default | Readable context | DATA-003 | Display |
| `downtimeEventId` | String | Conditional | Optional source downtime interval. | If present pair with downtimeEventName; avoid duplicate time accounting. | Yes: downtimeEventId + downtimeEventName -> mes_prod_downtimeEvent | Candidate downtimeEventId | Source traceability | SAP OEE loss; inventory | Reconciliation |
| `downtimeEventName` | String | Conditional | Downtime display/business-number snapshot. | Both source fields absent or present. | Yes: paired with downtimeEventId | No index by default | Readable source | DATA-003 | Audit |
| `lossDurationSeconds` | Double | Conditional | Duration of time-based loss. | Non-negative BSON Double; DEC-010 applicability OPEN. | No | No index by default | OEE computation | SAP OEE; DEC-010 | Loss aggregation |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `workOrderId` — Yes: workOrderId + workOrderName -> mes_prod_workOrder.
- `workOrderName` — Yes: paired with workOrderId.
- `downtimeEventId` — Yes: downtimeEventId + downtimeEventName -> mes_prod_downtimeEvent.
- `downtimeEventName` — Yes: paired with downtimeEventId.

## G. Validation rules

- Flat top-level scalar fields only; no nested documents or arrays.
- MES IDs are 16-digit strings; external identifiers remain separate.
- Enforce only approved code patterns and uniqueness scopes. Do not invent OPEN scopes or status vocabularies.
- Required ID/display pairs are complete and consistent; optional pairs are both absent or both populated.
- Audit actor pairs are complete; updatedAt >= createdAt.
- Retired reference records remain historically resolvable when established by decision.

## H. Status values

No lifecycle values established; correction/void handling OPEN.

## I. Relationships

**Outgoing:** Optional Work Order and source Downtime Event.  
**Incoming:** OEE results, shift summaries, loss analytics.  
**Dependencies:** Downtime Event, Scrap/Rework records where applicable, controlled classification and OEE time model.

## J. Recommended indexes

- Default unique _id index.
- Candidate lossType+lossStart — OEE; verify query frequency and selectivity before implementation.
- Candidate lossStart — Time loss; verify query frequency and selectivity before implementation.
- Candidate lossEnd — Time loss; verify query frequency and selectivity before implementation.
- Candidate workOrderId+lossStart — Loss reporting; verify query frequency and selectivity before implementation.
- Candidate downtimeEventId — Reconciliation; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  lossType: "<lossType>",
  lossStart: <value>,
  lossEnd: <value>,
  workOrderId: "<workOrderId>",
  workOrderName: "<workOrderName>",
  downtimeEventId: "<downtimeEventId>",
  downtimeEventName: "<downtimeEventName>",
  lossDurationSeconds: <value>,
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

Time-vs-quantity grain; duplicate counting with downtime/scrap/rework; reason classification owner; status/correction mechanism; source-system lineage; numeric type and event timestamp requirements.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: Targets exist; no new classification collection is implied.
- Status vocabulary: N/A — record correction lifecycle OPEN.
- Unresolved dependencies: see Section N; no silent resolution.

## P. Audit and Historical Data

Use the standard audit pairs defined in Section K. Preserve source timestamps and correction lineage where approved; retention, immutability, and correction policy remain unresolved unless stated in the decision log.

## Q. Real-World Manufacturing Exceptions

Consider late/out-of-order input, duplicate integration delivery, incomplete operational context, shift boundaries, retries, and correction/reversal scenarios where applicable. Do not invent fields or encode structured payloads in scalar fields to address them.

## R. Missing Field Assessment

The field inventory is bounded by current requirements, decisions, and the inventory purpose. Remaining gaps are: Time-vs-quantity grain; duplicate counting with downtime/scrap/rework; reason classification owner; status/correction mechanism; source-system lineage; numeric type and event timestamp requirements.. Raise missing operational data through requirements/change control; do not add fields silently.

## S. Missing Collection Assessment

No collection is added by this specification. Repeated concepts require separate flat records with explicit parent references. If a required owner or relationship record is absent from the inventory, record the gap for inventory review.

## T. Cross-Collection Consistency Check

- Collection name/module and inventory purpose checked.
- Flat scalar fields and singular key convention checked.
- System ID, reference-pair, and standard audit conventions checked.
- Relationship targets and status/lifecycle semantics: see Sections H, I, O, and X; unresolved items remain OPEN.

## U. Field Completeness Metrics

- Dictionary fields (including standard audit and system ID): 0
- Entity reference pairs: counted from Section F; audit actor pairs excluded.
- Scenarios and evidence: field-level rows cite project rules or collection-specific research where applicable.
- Requirements coverage cannot be claimed where detailed workflow requirements remain unavailable; see Sections R and X.

## V. One Representative MongoDB Document

The single flat representative structure is in Section L. No sample document set is generated in this design phase.

## W. Assumptions

- Inventory scope, current project context, and approved decisions bound this design.
- ID/display duplication is intentional strategic denormalization.
- APPROVED is the requested specification status; it does not close OPEN issues or establish production readiness.

## X. Open Issues

Time-vs-quantity grain; duplicate counting with downtime/scrap/rework; reason classification owner; status/correction mechanism; source-system lineage; numeric type and event timestamp requirements.

## Y. Research Sources

See Research basis below; evidence supports operational scenarios only and does not approve unresolved project rules.

## Z. Final Design Review

Status label: APPROVED under prior Project Owner instruction. OPEN items remain unresolved and are not approved business decisions. Flat-document, naming, reference, and evidence checks are summarized in Sections O and T.
## Research basis
- [SAP Digital Manufacturing — Manage OEE Time Model Configuration](https://help.sap.com/docs/sap-digital-manufacturing/execution/manage-oee-time-model-configuration?locale=en-US&state=PRODUCTION&version=latest) — OEE classifies scheduled downtime, unscheduled downtime, speed loss, productive time and other time elements, each with assigned reason codes.
- [SAP Digital Manufacturing — Assign OEE Reason Codes](https://help.sap.com/docs/sap-digital-manufacturing/execution/assign-oee-reason-codes) — Reason codes classify reported losses for insight analysis.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


