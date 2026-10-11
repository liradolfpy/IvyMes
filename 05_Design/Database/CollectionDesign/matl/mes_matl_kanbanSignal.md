**FILE TYPE: PROJECT SPECIFIC**

# mes_matl_kanbanSignal — Collection Specification

**Document ID:** MES-COL-MATL-kanbanSignal  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** matl  
**Collection type:** Transaction  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Records one Kanban replenishment trigger and fulfillment progress for a material/location demand.

## B. Business definition

The signal is a transactional trigger, not a card/container master; repeated triggers are separate records.

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
| `materialId` | String | Yes | Requested Material. | Pair with materialName. | Yes: materialId + materialName -> mes_matl_material | Candidate materialId+status+triggeredAt | Demand identity | DEC-022 | Replenishment |
| `materialName` | String | Yes | Material snapshot. | Paired with materialId. | Yes: paired with materialId | No index | Readable material | DATA-003 | Display |
| `locationId` | String | Yes | Replenishment WIP location. | Pair with locationName. | Yes: locationId + locationName -> mes_matl_wipLocation | Candidate locationId+status | Line-side demand | Inventory | Queue |
| `locationName` | String | Yes | Location snapshot. | Paired with locationId. | Yes: paired with locationId | No index | Readable location | DATA-003 | Display |
| `triggeredAt` | Date | Yes | Signal time. | BSON Date. | No | Candidate status+triggeredAt | Queue ordering | Kanban workflow research | Replenishment |
| `status` | String | Yes | Signal lifecycle. | Vocabulary OPEN. | No | Candidate status+triggeredAt | Fulfillment tracking | Inventory; business rules OPEN | Queue |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `materialId` — Yes: materialId + materialName -> mes_matl_material.
- `materialName` — Yes: paired with materialId.
- `locationId` — Yes: locationId + locationName -> mes_matl_wipLocation.
- `locationName` — Yes: paired with locationId.

## G. Validation rules

- Flat top-level scalar fields only; no nested documents or arrays.
- MES IDs are 16-digit strings; external identifiers remain separate.
- Enforce only approved code patterns and uniqueness scopes. Do not invent OPEN scopes or status vocabularies.
- Required ID/display pairs are complete and consistent; optional pairs are both absent or both populated.
- Audit actor pairs are complete; updatedAt >= createdAt.
- Retired reference records remain historically resolvable when established by decision.

## H. Status values

Kanban lifecycle values OPEN.

## I. Relationships

**Outgoing:** Material, WIP Location; optional fulfillment Material Movement.  
**Incoming:** Replenishment reporting.  
**Dependencies:** Container/card identity owner, quantity/UOM, fulfillment event.

## J. Recommended indexes

- Default unique _id index.
- Candidate materialId+status+triggeredAt — Replenishment; verify query frequency and selectivity before implementation.
- Candidate locationId+status — Queue; verify query frequency and selectivity before implementation.
- Candidate status+triggeredAt — Replenishment; verify query frequency and selectivity before implementation.
- Candidate status+triggeredAt — Queue; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  materialId: "<materialId>",
  materialName: "<materialName>",
  locationId: "<locationId>",
  locationName: "<locationName>",
  triggeredAt: <value>,
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

No card/container collection in inventory; signal quantity/UOM; trigger deduplication; fulfillment/cancel states.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: Material and WIP Location exist; card identity remains open.
- Status vocabulary: OPEN
- Unresolved dependencies: see Section N; no silent resolution.

## P. Audit and Historical Data

The standard audit fields are defined in Section K. Preserve source chronology and corrections under approved project controls; retention, immutability and event-reversal policy remain OPEN where not decided.

## Q. Real-World Manufacturing Exceptions

Consider late and duplicate messages, incomplete source context, retries, overnight shifts, corrections and partial completion when applicable. Do not use nested data or scalar-encoded structured payloads to represent repeated facts.

## R. Missing Field Assessment

The current inventory bounds collection scope. Known field/workflow gaps remain: No card/container collection in inventory; signal quantity/UOM; trigger deduplication; fulfillment/cancel states.. Raise additional needs through requirements/change control rather than adding fields silently.

## S. Missing Collection Assessment

No collection is added by this specification. Repeated concepts remain separate flat records. Where an owning entity, item identity, or relationship record is absent, retain the gap for inventory review.

## T. Cross-Collection Consistency Check

- Inventory name/module and purpose checked against the current collection list.
- Flat scalar representation, singular key naming, ID/display pairs, and standard audit fields checked.
- Reference targets and lifecycle/type gaps are called out in Sections N, O, and X; unresolved items remain OPEN.

## U. Field Completeness Metrics

- Field names listed in the dictionary: 13, including system ID and audit fields.
- Entity reference pairs are enumerated in Section F; audit pairs are listed in Section K.
- Scenario and source evidence is collection-specific where documented; no unsupported requirement coverage percentage is claimed.

## V. One Representative MongoDB Document

The single illustrative flat structure is in Section L. No sample document set is generated in this phase.

## W. Assumptions

- Current project context, functional inventory, requirements and approved decisions bound this design.
- ID/display duplication is intentional strategic denormalization.
- APPROVED is the requested specification status; OPEN items remain unresolved and this label does not establish production readiness.

## X. Open Issues

No card/container collection in inventory; signal quantity/UOM; trigger deduplication; fulfillment/cancel states.

## Y. Research Sources

See Research basis below. Sources support manufacturing workflows and scenarios; they do not approve unresolved project rules or transfer vendor schemas.

## Z. Final Design Review

Status label: APPROVED under prior Project Owner instruction. OPEN items remain unresolved. Flatness, reference conventions, inventory coverage and research evidence are summarized in Sections O and T.
## Research basis
- [SAP S/4HANA Kanban Replenishment](https://help.sap.com/docs/SAP_S4HANA_ON-PREMISE/6b3c3e4b9e9c4f329cae6a7f1a4c7d7a/kanban-replenishment) — Kanban signals initiate and track replenishment.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.

