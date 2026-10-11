**FILE TYPE: PROJECT SPECIFIC**

# mes_tool_tool — Collection Specification

**Document ID:** MES-COL-TOOL-tool  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** tool  
**Collection type:** Master  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Owns a distinct Tool identity, classification, location, and tool-specific lifecycle; may reference a separate maintainable Asset identity.

## B. Business definition

Tool is a distinct business entity from Asset, even when associated with one, under DEC-022. Tool-specific custody, calibration, usage, and compatibility records reference Tool identity.

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
| `toolCode` | String | Yes (proposed) | Business/tool-room identifier. | Pattern and uniqueness scope OPEN. | No | Candidate toolCode; uniqueness OPEN | Identify tooling. | Inventory; SAP Track Tool Usage | Tool selection |
| `toolName` | String | Yes | Readable tool identity. | Non-empty. | No | Candidate text search | Operator/tool-room display. | DATA-003 | Tool use |
| `toolClassId` | String | Yes (proposed) | Tool classification identity. | 16-digit ID resolving mes_tool_toolClass. | Yes: toolClassId + toolClassName -> mes_tool_toolClass | Candidate toolClassId | Classification and compatibility. | Inventory; DATA-003 | Tool selection |
| `toolClassName` | String | Yes (proposed) | Tool class display snapshot. | Paired with toolClassId. | Yes: paired with toolClassId | No index by default | Readable class. | DATA-003 | Tool list |
| `toolLocationId` | String | Conditional (proposed) | Current/assigned storage location identity. | If present pair with toolLocationName; current custody vs master location unresolved. | Yes: toolLocationId + toolLocationName -> mes_tool_toolLocation | Candidate toolLocationId | Locate tool. | Inventory; SAP Tool Usage | Availability/location |
| `toolLocationName` | String | Conditional | Tool location display snapshot. | Both location fields present or absent. | Yes: paired with toolLocationId | No index by default | Readable location. | DATA-003 | Tool search |
| `assetId` | String | Conditional | Separate maintainable Asset identity when Tool also has an Asset record. | If present paired with assetName; cardinality/sync OPEN. | Yes: assetId + assetName -> mes_maint_asset | Candidate assetId | Bridge distinct tool and asset management. | DEC-022 | Maintenance linkage |
| `assetName` | String | Conditional | Asset display snapshot. | Both asset fields present or absent. | Yes: paired with assetId | No index by default | Readable link. | DATA-003; DEC-022 | Asset relation |
| `status` | String | Yes (proposed) | Tool-specific lifecycle/availability status. | Values and separation from maintenance/calibration/occupancy OPEN. | No | Candidate status filter | Govern tool use. | Inventory; lifecycle rules OPEN | Tool assignment |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `toolClassId` — Yes: toolClassId + toolClassName -> mes_tool_toolClass.
- `toolClassName` — Yes: paired with toolClassId.
- `toolLocationId` — Yes: toolLocationId + toolLocationName -> mes_tool_toolLocation.
- `toolLocationName` — Yes: paired with toolLocationId.
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

Tool-specific lifecycle/eligibility; not asset condition or location occupancy. Vocabulary OPEN.

## I. Relationships

**Outgoing:** Tool Class, optional Tool Location, optional separate Asset pairs.  
**Incoming:** Reservation, movement, life, usage, calibration, maintenance, incident, cost, production and analytics records.  
**Dependencies:** Tool Class/Location; optional mes_maint_asset. Tool remains distinct from asset/machine.

## J. Recommended indexes

- Default unique _id index.
- Candidate toolCode; uniqueness OPEN — Tool selection; verify query frequency and selectivity before implementation.
- Candidate text search — Tool use; verify query frequency and selectivity before implementation.
- Candidate toolClassId — Tool selection; verify query frequency and selectivity before implementation.
- Candidate toolLocationId — Availability/location; verify query frequency and selectivity before implementation.
- Candidate assetId — Maintenance linkage; verify query frequency and selectivity before implementation.
- Candidate status filter — Tool assignment; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  toolCode: "<toolCode>",
  toolName: "<toolName>",
  toolClassId: "<toolClassId>",
  toolClassName: "<toolClassName>",
  toolLocationId: "<toolLocationId>",
  toolLocationName: "<toolLocationName>",
  assetId: "<assetId>",
  assetName: "<assetName>",
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

Tool code uniqueness; tool/asset cardinality and lifecycle sync; current location vs master location; calibration authority; status vocabulary; serial/manufacturer fields; whether class required.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: PASS — all defined references have ID/display pairs; Asset remains optional and distinct.
- Status vocabulary: OPEN — values not approved.
- Unresolved dependencies: see Section N; no silent resolution.


## P. Audit and Historical Data

Standard audit fields are flat and paired. Effective dates, assignment history, and lifecycle records must not be embedded. Retention and actor-source policies remain OPEN.

## Q. Real-World Manufacturing Exceptions

Do not infer workforce training eligibility, Tool availability, compatibility, or capacity from unrelated status fields. Invalid or unresolved targets remain visible for controlled reconciliation.

## R. Missing Field Assessment

Detailed requirements remain incomplete for this design. Review with workforce/tool-room/planning/maintenance and database roles. Known gaps: Tool code uniqueness; tool/asset cardinality and lifecycle sync; current location vs master location; calibration authority; status vocabulary; serial/manufacturer fields; whether class required.

## S. Missing Collection Assessment

No collection is added. Capacity target and training role/course owners are unresolved; do not reuse Work Center, Asset, or an external entity without approved identity ownership.

## T. Cross-Collection Consistency Check

- Inventory scope/type checked against v1.6.
- Defined references use singular ID/display pairs; Tool and Asset remain distinct under DEC-022.
- Repeated relationships are flat records.
- Resource-capacity target and certification/training taxonomies remain OPEN where identified.
- No cross-collection status vocabulary or numeric type is inferred.

## U. Field Completeness Metrics

See Sections E, F, and J for candidate fields, references, and indexes. Detailed scenario coverage is not established without collection-specific requirements. Known gaps: Tool code uniqueness; tool/asset cardinality and lifecycle sync; current location vs master location; calibration authority; status vocabulary; serial/manufacturer fields; whether class required.

## V. One Representative MongoDB Document

The single illustrative flat structure appears in Section L; no sample set is generated.

## W. Assumptions

- The collection inventory and approved ownership decisions control scope.
- DATA-001 requires separate flat records for repeated relationships.
- ID/name duplication is intentional strategic denormalization.
- APPROVED follows the prior owner instruction; open points remain unapproved.

## X. Open Issues

Tool code uniqueness; tool/asset cardinality and lifecycle sync; current location vs master location; calibration authority; status vocabulary; serial/manufacturer fields; whether class required.

## Y. Research Sources

Sources in the Research basis section below support the collection's operational use cases only; no vendor schema or status list is adopted.

## Z. Final Design Review

- **Status:** APPROVED under prior Project Owner instruction; not production readiness.
- **Covered:** bounded purpose, candidate fields, flat relationships, research, and unresolved scope.
- **Remaining:** Section X; capacity/identity taxonomy blockers must be resolved before implementation.
## Research basis

- [SAP S/4HANA Track Tool Usage](https://help.sap.com/docs/SAP_S4HANA_ON-PREMISE/9c4986bda35f4840ae438960ffbef64d/d693fb81e88249569caa6764a5007249.html) — Tracks tool location, availability, claims and history; informs query scenarios.
- [SAP Production Resources and Tools](https://help.sap.com/docs/SAP_S4HANA_ON-PREMISE/79e86803745943368f3beb4a7d0bca2a/f4ddba53422bb54ce10000000a174cb4.html) — Describes tools and other mobile production resources distinct from fixed technical devices; project keeps Tool a distinct identity.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


