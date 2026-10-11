**FILE TYPE: PROJECT SPECIFIC**

# mes_maint_conditionObservation — Collection Specification

**Document ID:** MES-COL-MAINT-conditionObservation  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** maint  
**Collection type:** Transaction  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Stores a time-stamped measurement or inspection observation about an Asset condition.

## B. Business definition

Condition Observation is one flat reading/finding for one Asset and one indicator or condition item; repeated measurements are separate records.

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
| `assetId` | String | Yes | Observed technical Asset. | Pair with assetName. | Yes: assetId + assetName -> mes_maint_asset | Candidate assetId+observedAt | Subject identity | DEC-022; SAP measuring points | Condition history |
| `assetName` | String | Yes | Asset display snapshot. | Paired with assetId. | Yes: paired with assetId | No index by default | Readable context | DATA-003 | Display |
| `observedAt` | Date | Yes | Time reading/finding recorded. | BSON Date. | No | Candidate assetId+observedAt | Trend and chronology | SAP measuring points | Condition monitoring |
| `indicatorCode` | String | Yes (proposed) | Measurement point/condition indicator code. | Indicator ownership, units and code registry OPEN. | No | Candidate assetId+indicatorCode+observedAt | Typed measurement | SAP APM asset health | Trend query |
| `indicatorName` | String | Yes (proposed) | Indicator display name snapshot. | Pairing/owner model for indicator is OPEN; do not treat as entity reference yet. | No | No index by default | Readable value | DATA-003 guidance; owner OPEN | Display |
| `numericValue` | Double | Conditional | Numeric indicator value when measurement is numeric. | BSON Double; compatible with DEC-010 only if approved by project numeric rule; unit and precision OPEN. | No | No index by default | Sensor measurement | SAP measuring points; DEC-010 | Trend calculation |
| `textValue` | String | Conditional | Textual inspection finding where not numeric. | Exactly one of numericValue/textValue should be populated; allowable value types OPEN. | No | No index by default | Non-numeric observations | SAP inspection/condition research | Manual inspection |
| `unitCode` | String | Conditional | Measurement unit code. | If numeric reading uses a unit, code semantics and UOM relationship OPEN. | No | No index by default | Interpret quantity | mes_matl_unitOfMeasure; DATA-003 | Interpretation |
| `sourceType` | String | Yes (proposed) | Origin type such as sensor or manual inspection. | Allowed values OPEN. | No | Candidate sourceType+observedAt | Provenance | SAP APM condition monitoring | Ingestion analysis |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

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

No lifecycle status established; data-quality classification OPEN.

## I. Relationships

**Outgoing:** Required Asset.  
**Incoming:** Maintenance plan triggers, asset health summary, analytics.  
**Dependencies:** Asset; indicator definition/registry and unit semantics are not identified in inventory.

## J. Recommended indexes

- Default unique _id index.
- Candidate assetId+observedAt — Condition history; verify query frequency and selectivity before implementation.
- Candidate assetId+observedAt — Condition monitoring; verify query frequency and selectivity before implementation.
- Candidate assetId+indicatorCode+observedAt — Trend query; verify query frequency and selectivity before implementation.
- Candidate sourceType+observedAt — Ingestion analysis; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  assetId: "<assetId>",
  assetName: "<assetName>",
  observedAt: <value>,
  indicatorCode: "<indicatorCode>",
  indicatorName: "<indicatorName>",
  numericValue: <value>,
  textValue: "<textValue>",
  unitCode: "<unitCode>",
  sourceType: "<sourceType>",
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

Indicator master/ownership; numeric vs textual data type rule; repeated checklist/inspection linkage; unit rules; sensor source identity; limits/alarms are not defined; correction/retention and sampling frequency.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: Asset target exists. Indicator and UOM reference owners need requirements/inventory decision.
- Status vocabulary: N/A — no lifecycle status specified.
- Unresolved dependencies: see Section N; no silent resolution.

## P. Audit and Historical Data

Use the standard audit pairs defined in Section K. Preserve source timestamps and correction lineage where approved; retention, immutability, and correction policy remain unresolved unless stated in the decision log.

## Q. Real-World Manufacturing Exceptions

Consider late/out-of-order input, duplicate integration delivery, incomplete operational context, shift boundaries, retries, and correction/reversal scenarios where applicable. Do not invent fields or encode structured payloads in scalar fields to address them.

## R. Missing Field Assessment

The field inventory is bounded by current requirements, decisions, and the inventory purpose. Remaining gaps are: Indicator master/ownership; numeric vs textual data type rule; repeated checklist/inspection linkage; unit rules; sensor source identity; limits/alarms are not defined; correction/retention and sampling frequency.. Raise missing operational data through requirements/change control; do not add fields silently.

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

Indicator master/ownership; numeric vs textual data type rule; repeated checklist/inspection linkage; unit rules; sensor source identity; limits/alarms are not defined; correction/retention and sampling frequency.

## Y. Research Sources

See Research basis below; evidence supports operational scenarios only and does not approve unresolved project rules.

## Z. Final Design Review

Status label: APPROVED under prior Project Owner instruction. OPEN items remain unresolved and are not approved business decisions. Flat-document, naming, reference, and evidence checks are summarized in Sections O and T.
## Research basis
- [SAP S/4HANA — Measuring Points and Counters](https://help.sap.com/docs/SAP_S4HANA_ON-PREMISE/21aead0c98bd4755abdacd91c99e3393/3b6db65334e6b54ce10000000a174cb4.html?locale=en-US&state=PRODUCTION&version=2025.001) — A measured point documents condition/counter readings for a technical object at a particular time.
- [SAP Asset Performance Management — Asset Health Monitoring](https://help.sap.com/docs/sap-apm/8c95dca44fda4d56a17f6494f421758d/100020990539425195769226c12e0333.html) — Asset health monitoring analyzes sensor-collected indicators and trends to identify abnormalities and trigger alerts.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


