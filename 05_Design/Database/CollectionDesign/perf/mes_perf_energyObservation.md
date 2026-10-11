**FILE TYPE: PROJECT SPECIFIC**

# mes_perf_energyObservation — Collection Specification

**Document ID:** MES-COL-PERF-energyObservation  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** perf  
**Collection type:** Transaction  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Stores one scalar energy measurement or interval aggregate associated with a plant and optionally an Asset or production context.

## B. Business definition

Energy Observation is a measured sustainability/performance fact sourced from a meter, sensor, or integration. It is not a configuration or derived KPI result.

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
| `observedAt` | Date | Yes | Measurement time or interval end. | BSON Date; interval semantics OPEN. | No | Candidate plantId+observedAt | Time-series query | SAP Energy Monitoring | Energy trend |
| `plantId` | String | Yes | Plant measurement scope. | Pair with plantName. | Yes: plantId + plantName -> mes_plan_plant | Candidate plantId+observedAt | Plant aggregation | DEC-022; SAP Energy Monitoring | Plant reporting |
| `plantName` | String | Yes | Plant display snapshot. | Paired with plantId. | Yes: paired with plantId | No index by default | Readable context | DATA-003 | Display |
| `assetId` | String | Conditional | Optional meter-host equipment Asset. | If present pair with assetName. | Yes: assetId + assetName -> mes_maint_asset | Candidate assetId+observedAt | Equipment analysis | DEC-022 | Energy by asset |
| `assetName` | String | Conditional | Asset display snapshot. | Both fields absent or present. | Yes: paired with assetId | No index by default | Readable context | DATA-003 | Display |
| `energyValue` | Double | Yes | Energy quantity measured for the observation. | Non-negative BSON Double; unit required via energyUnitCode; DEC-010 numeric guidance. | No | No index by default | Measured consumption | SAP Energy Monitoring; DEC-010 | Energy aggregation |
| `energyUnitCode` | String | Yes | Unit of energy measurement. | Controlled code and conversion semantics OPEN; UOM collection applicability to energy dimension must be confirmed. | No | Candidate energyUnitCode+observedAt | Interpret measurement | SAP Energy Monitoring | Normalization of metrics |
| `sourceSystem` | String | Yes (proposed) | Meter/source system label. | Source ownership/catalog OPEN. | No | Candidate sourceSystem+externalReadingId | Provenance | SAP Energy Monitoring | Integration |
| `externalReadingId` | String | No | External reading key. | Unique within source when present; uniqueness scope OPEN. | No | Candidate sourceSystem+externalReadingId | Deduplication | Integration research | Replay safety |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `plantId` — Yes: plantId + plantName -> mes_plan_plant.
- `plantName` — Yes: paired with plantId.
- `assetId` — Yes: assetId + assetName -> mes_maint_asset.
- `assetName` — Yes: paired with assetId.

## G. Validation rules

- Flat top-level scalar fields only; no nested documents or arrays.
- MES IDs are 16-digit strings; external identifiers remain separate.
- Enforce only approved code patterns and uniqueness scopes. Do not invent OPEN scopes or status vocabularies.
- Required ID/display pairs are complete and consistent; optional pairs are both absent or both populated.
- Audit actor pairs are complete; updatedAt >= createdAt.
- Retired reference records remain historically resolvable when established by decision.

## H. Status values

No lifecycle status established; quality/reversal handling OPEN.

## I. Relationships

**Outgoing:** Plant and optional Asset.  
**Incoming:** Energy KPI, production-order energy analysis, dashboards.  
**Dependencies:** Plant; optional Asset; meter/source ownership; unit semantics; production event/order association if required.

## J. Recommended indexes

- Default unique _id index.
- Candidate plantId+observedAt — Energy trend; verify query frequency and selectivity before implementation.
- Candidate plantId+observedAt — Plant reporting; verify query frequency and selectivity before implementation.
- Candidate assetId+observedAt — Energy by asset; verify query frequency and selectivity before implementation.
- Candidate energyUnitCode+observedAt — Normalization of metrics; verify query frequency and selectivity before implementation.
- Candidate sourceSystem+externalReadingId — Integration; verify query frequency and selectivity before implementation.
- Candidate sourceSystem+externalReadingId — Replay safety; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  observedAt: <value>,
  plantId: "<plantId>",
  plantName: "<plantName>",
  assetId: "<assetId>",
  assetName: "<assetName>",
  energyValue: <value>,
  energyUnitCode: "<energyUnitCode>",
  sourceSystem: "<sourceSystem>",
  externalReadingId: "<externalReadingId>",
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

Instant reading vs interval consumption; cumulative counter vs delta; unit code and conversion; meter identity has no owner; optional workOrder/product link; external ID dedup scope; retain raw readings vs aggregates; numeric precision under Double.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: Plant and Asset targets exist. UOM energy-dimension applicability unresolved; no meter entity is inventoried.
- Status vocabulary: N/A — no lifecycle status specified.
- Unresolved dependencies: see Section N; no silent resolution.

## P. Audit and Historical Data

Use the standard audit pairs defined in Section K. Preserve source timestamps and correction lineage where approved; retention, immutability, and correction policy remain unresolved unless stated in the decision log.

## Q. Real-World Manufacturing Exceptions

Consider late/out-of-order input, duplicate integration delivery, incomplete operational context, shift boundaries, retries, and correction/reversal scenarios where applicable. Do not invent fields or encode structured payloads in scalar fields to address them.

## R. Missing Field Assessment

The field inventory is bounded by current requirements, decisions, and the inventory purpose. Remaining gaps are: Instant reading vs interval consumption; cumulative counter vs delta; unit code and conversion; meter identity has no owner; optional workOrder/product link; external ID dedup scope; retain raw readings vs aggregates; numeric precision under Double.. Raise missing operational data through requirements/change control; do not add fields silently.

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

Instant reading vs interval consumption; cumulative counter vs delta; unit code and conversion; meter identity has no owner; optional workOrder/product link; external ID dedup scope; retain raw readings vs aggregates; numeric precision under Double.

## Y. Research Sources

See Research basis below; evidence supports operational scenarios only and does not approve unresolved project rules.

## Z. Final Design Review

Status label: APPROVED under prior Project Owner instruction. OPEN items remain unresolved and are not approved business decisions. Flat-document, naming, reference, and evidence checks are summarized in Sections O and T.
## Research basis
- [SAP MII — Energy Monitoring and Analysis](https://help.sap.com/docs/SAP_MANUFACTURING_INTEGRATION_AND_INTELLIGENCE/d70c3ac3566b41dd896cd7cecc94e14a/d7887c56df14763de10000000a4450e5.html) — Energy data is collected from points/nodes and analyzed across plants, work centers, equipment, machines, with aggregation and filtering.
- [SAP Digital Manufacturing — OEE Calculations](https://help.sap.com/docs/sap-digital-manufacturing/insights/oee-calculations) — Operational KPIs depend on time and production context; energy remains a separate source measurement here.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


