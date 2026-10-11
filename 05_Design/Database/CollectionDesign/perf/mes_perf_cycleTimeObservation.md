**FILE TYPE: PROJECT SPECIFIC**

# mes_perf_cycleTimeObservation — Collection Specification

**Document ID:** MES-COL-PERF-cycleTimeObservation  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** perf  
**Collection type:** Transaction  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Stores a measured cycle-time observation for a production order operation or other approved observation grain.

## B. Business definition

Cycle Time Observation is a time-based performance fact. Individual observations are retained as separate scalar records; derivation and aggregation belong to defined performance outputs.

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
| `observedAt` | Date | Yes | Observation capture time. | BSON Date. | No | Candidate observedAt | Time-window analysis | SAP cycle-time metric | Trend analysis |
| `assetId` | String | Conditional | Optional machine/equipment context. | If present pair with assetName. | Yes: assetId + assetName -> mes_maint_asset | Candidate assetId+observedAt | Asset-level cycle times | DEC-022; DATA-003 | Performance analysis |
| `assetName` | String | Conditional | Asset display snapshot. | Both fields absent or present. | Yes: paired with assetId | No index by default | Readable context | DATA-003 | Display |
| `routingOperationId` | String | Conditional | Optional routing-step context. | If present pair with routingOperationName. | Yes: routingOperationId + routingOperationName -> mes_prod_routingOperation | Candidate routingOperationId+observedAt | Step-level measurement | DEC-003; SAP cycle-time metric | Routing-step analysis |
| `routingOperationName` | String | Conditional | Routing operation display snapshot. | Both fields absent or present. | Yes: paired with routingOperationId | No index by default | Readable step | DATA-003 | Display |
| `workOrderId` | String | Conditional | Optional Work Order context. | If present pair with workOrderName. | Yes: workOrderId + workOrderName -> mes_prod_workOrder | Candidate workOrderId+observedAt | Order-specific performance | DEC-004 | Work order analysis |
| `workOrderName` | String | Conditional | Work Order display snapshot. | Both fields absent or present. | Yes: paired with workOrderId | No index by default | Readable order | DATA-003 | Display |
| `cycleTimeSeconds` | Double | Yes (proposed) | Observed elapsed duration in seconds. | Non-negative BSON Double; measurement source and rounding OPEN. | No | No index by default | Performance measure | DEC-010 numeric guidance; SAP metric | Cycle-time KPI |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `assetId` — Yes: assetId + assetName -> mes_maint_asset.
- `assetName` — Yes: paired with assetId.
- `routingOperationId` — Yes: routingOperationId + routingOperationName -> mes_prod_routingOperation.
- `routingOperationName` — Yes: paired with routingOperationId.
- `workOrderId` — Yes: workOrderId + workOrderName -> mes_prod_workOrder.
- `workOrderName` — Yes: paired with workOrderId.

## G. Validation rules

- Flat top-level scalar fields only; no nested documents or arrays.
- MES IDs are 16-digit strings; external identifiers remain separate.
- Enforce only approved code patterns and uniqueness scopes. Do not invent OPEN scopes or status vocabularies.
- Required ID/display pairs are complete and consistent; optional pairs are both absent or both populated.
- Audit actor pairs are complete; updatedAt >= createdAt.
- Retired reference records remain historically resolvable when established by decision.

## H. Status values

No status vocabulary established; observation correction policy OPEN.

## I. Relationships

**Outgoing:** Optional Asset, Routing Operation, and Work Order.  
**Incoming:** Performance summaries and Analytics.  
**Dependencies:** OperationExecution for source timing; routing step; optional Asset and Work Order.

## J. Recommended indexes

- Default unique _id index.
- Candidate observedAt — Trend analysis; verify query frequency and selectivity before implementation.
- Candidate assetId+observedAt — Performance analysis; verify query frequency and selectivity before implementation.
- Candidate routingOperationId+observedAt — Routing-step analysis; verify query frequency and selectivity before implementation.
- Candidate workOrderId+observedAt — Work order analysis; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  observedAt: <value>,
  assetId: "<assetId>",
  assetName: "<assetName>",
  routingOperationId: "<routingOperationId>",
  routingOperationName: "<routingOperationName>",
  workOrderId: "<workOrderId>",
  workOrderName: "<workOrderName>",
  cycleTimeSeconds: <value>,
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

Whether observation is per unit, batch, operation execution, or externally measured cycle; field type under DEC-010 scope; measured vs calculated value; quality exclusions; duplicate observations; correlation to operationExecutionId; precision and units.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: Targets exist. Grain and exact source linkage remain OPEN.
- Status vocabulary: N/A — no lifecycle status specified.
- Unresolved dependencies: see Section N; no silent resolution.

## P. Audit and Historical Data

Use the standard audit pairs defined in Section K. Preserve source timestamps and correction lineage where approved; retention, immutability, and correction policy remain unresolved unless stated in the decision log.

## Q. Real-World Manufacturing Exceptions

Consider late/out-of-order input, duplicate integration delivery, incomplete operational context, shift boundaries, retries, and correction/reversal scenarios where applicable. Do not invent fields or encode structured payloads in scalar fields to address them.

## R. Missing Field Assessment

The field inventory is bounded by current requirements, decisions, and the inventory purpose. Remaining gaps are: Whether observation is per unit, batch, operation execution, or externally measured cycle; field type under DEC-010 scope; measured vs calculated value; quality exclusions; duplicate observations; correlation to operationExecutionId; precision and units.. Raise missing operational data through requirements/change control; do not add fields silently.

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

Whether observation is per unit, batch, operation execution, or externally measured cycle; field type under DEC-010 scope; measured vs calculated value; quality exclusions; duplicate observations; correlation to operationExecutionId; precision and units.

## Y. Research Sources

See Research basis below; evidence supports operational scenarios only and does not approve unresolved project rules.

## Z. Final Design Review

Status label: APPROVED under prior Project Owner instruction. OPEN items remain unresolved and are not approved business decisions. Flat-document, naming, reference, and evidence checks are summarized in Sections O and T.
## Research basis
- [SAP Signavio — Manufacturing Cycle Time Metrics](https://help.sap.com/docs/signavio-process-intelligence/accelerators/metrics-for-operate-manufacturing-sap-digital-manufacturing?locale=en-US) — Routing-step cycle time is measured between a step start and completion; WIP time is between step completion and next-step start.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


