**FILE TYPE: PROJECT SPECIFIC**

# mes_maint_asset — Collection Specification

**Document ID:** MES-COL-MAINT-asset  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** maint  
**Collection type:** Master  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Owns maintainable asset/equipment identities, including machines, with classification, location and lifecycle context.

## B. Business definition

Asset is a maintainable technical object owned by Maintenance under DEC-022, distinct from Planning Work Center. A Tool may have a separate associated Asset identity.

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
| `assetCode` | String | Yes (proposed) | Human/source-facing asset identifier. | Pattern/uniqueness scope OPEN. | External/business key distinct from _id | Candidate lookup; uniqueness OPEN | Asset identification. | Inventory; SAP Technical Objects | Maintenance selection |
| `assetName` | String | Yes | Readable asset display name. | Non-empty. | No | Candidate text search | Cross-module display. | DATA-003; SAP technical object docs | Maintenance planning |
| `assetDescription` | String | No | Scalar asset function description. | Plain text only. | No | No index by default | Context. | Inventory | Asset register |
| `plantId` | String | Conditional (proposed) | Plant where asset is maintained/located. | If present paired with plantName; 16-digit ID. | Yes: plantId + plantName -> mes_plan_plant | Candidate plant filter | Site context. | DEC-022; SAP technical objects | Maintenance scope |
| `plantName` | String | Conditional | Plant display snapshot. | Both present or absent. | Yes: paired with plantId | No index by default | Readable context. | DATA-003 | Asset display |
| `assetClassCode` | String | Conditional (proposed) | Code grouping assets by classification. | Taxonomy owner and allowed values OPEN. | Scalar until owner decision | Candidate filter after taxonomy | Classification queries. | Inventory; SAP classification | Maintenance analytics |
| `assetClassName` | String | Conditional | Display label paired to classification code. | Pair semantics and taxonomy owner OPEN. | Classification label; entity status OPEN | No index by default | Readable class. | DATA-003 if entity | Asset display |
| `assetLocationCode` | String | Conditional (proposed) | Location locator/code for installed or stored asset. | Namespace/owner/meaning OPEN; do not assume a location collection. | External or local locator unresolved | Candidate location filter after definition | Find asset location. | Inventory; SAP technical-object location data | Asset location search |
| `assetLocationName` | String | Conditional | Readable location snapshot. | Required only if locationCode denotes an entity; unresolved. | Pair semantics OPEN | No index by default | Readable location. | DATA-003 if entity | Maintenance display |
| `status` | String | Yes (proposed) | Asset lifecycle/availability state. | Values/transitions and distinction from condition OPEN. | No | Candidate status filter | Govern new references. | Lifecycle rules OPEN | Asset selection |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

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

Asset lifecycle must be distinct from current condition or maintenance-due state; values OPEN.

## I. Relationships

**Outgoing:** Plant pair; optional Tool-to-Asset relation unresolved; no work-center link.  
**Incoming:** Maintenance plans/orders, condition, performance, production and analytics may refer to assetId + assetName.  
**Dependencies:** Plant master confirmed. No asset-classification/location master is listed. Tool-Asset cardinality/synchronization OPEN. No Work Center relation.

## J. Recommended indexes

- Default unique _id index.
- Candidate lookup; uniqueness OPEN — Maintenance selection; verify query frequency and selectivity before implementation.
- Candidate text search — Maintenance planning; verify query frequency and selectivity before implementation.
- Candidate plant filter — Maintenance scope; verify query frequency and selectivity before implementation.
- Candidate filter after taxonomy — Maintenance analytics; verify query frequency and selectivity before implementation.
- Candidate location filter after definition — Asset location search; verify query frequency and selectivity before implementation.
- Candidate status filter — Asset selection; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  assetCode: "<assetCode>",
  assetName: "<assetName>",
  assetDescription: "<assetDescription>",
  plantId: "<plantId>",
  plantName: "<plantName>",
  assetClassCode: "<assetClassCode>",
  assetClassName: "<assetClassName>",
  assetLocationCode: "<assetLocationCode>",
  assetLocationName: "<assetLocationName>",
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

Asset code namespace; plant requiredness; location owner/locator semantics; classification taxonomy; technical-object hierarchy; lifecycle vs condition; Tool link cardinality and synchronization; manufacturer/serial requirements.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: OPEN — location/classification ownership unresolved; no work-center relation.
- Status vocabulary: OPEN — vocabulary not approved.
- Unresolved dependencies: see Section N; no silent resolution.


## P. Audit and Historical Data

Standard audit fields are flat: createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity source, retention period, and immutable-history requirements remain OPEN unless an approved project rule states otherwise. Lifecycle or location history must not be embedded as arrays.

## Q. Real-World Manufacturing Exceptions

Handle retired, unmapped, or unavailable references without silently substituting a different identity. Store only scalar exception context already represented in the field dictionary. Detailed correction, retry, and reconciliation behavior remains OPEN where applicable.

## R. Missing Field Assessment

The current project copy does not provide collection-specific functional requirement artifacts for this item. Potential missing fields must be evaluated with operations, planning, engineering, quality, integration, maintenance, material, and database roles before implementation. Known assessment items: Asset code namespace; plant requiredness; location owner/locator semantics; classification taxonomy; technical-object hierarchy; lifecycle vs condition; Tool link cardinality and synchronization; manufacturer/serial requirements.

## S. Missing Collection Assessment

No collection is added by this specification. The Collection Inventory remains authoritative. If an unresolved parent, interval, classification, location, or external-system owner requires another collection, report it for inventory review; do not embed the repeated concept.

## T. Cross-Collection Consistency Check

- Inventory name, module, type, and purpose: checked against Collection Inventory v1.6.
- Flat document and singular field stems: PASS for defined fields.
- Project ID and audit conventions: PASS.
- Entity ID/display pairs: see Section F; unresolved owners/cardinality are OPEN.
- Types: follow current conventions for defined fields.
- Status and business-number rules: remain OPEN where not explicitly decided.
- References and dependencies: see Sections I and N; no foreign-key enforcement is assumed.

## U. Field Completeness Metrics

Metrics are provisional because detailed collection requirements and the full Stage 2 research artifacts are unavailable in this active project copy.

- Proposed fields: see Section E.
- Required / conditional / optional: see Section E; proposed requiredness is not treated as a confirmed business rule.
- ID–Name pairs: see Section F.
- Status/control fields: collection-specific field dictionary.
- Audit fields: 6.
- External fields: see Section E.
- Scenario coverage: no percentage claimed until detailed requirements are linked.
- Research sources: see Section Y.
- Unresolved gaps: Asset code namespace; plant requiredness; location owner/locator semantics; classification taxonomy; technical-object hierarchy; lifecycle vs condition; Tool link cardinality and synchronization; manufacturer/serial requirements.

## V. One Representative MongoDB Document

The single illustrative flat document structure is provided in Section L. No second document or sample-data set is added here.

## W. Assumptions

- The inventory determines collection identity, module, type, and bounded purpose.
- DATA-001 requires separate flat records for repeated concepts.
- DATA-003 ID/display duplication is intentional strategic denormalization.
- APPROVED is the user's requested specification status; OPEN questions remain unapproved and unresolved.

## X. Open Issues

Asset code namespace; plant requiredness; location owner/locator semantics; classification taxonomy; technical-object hierarchy; lifecycle vs condition; Tool link cardinality and synchronization; manufacturer/serial requirements.

## Y. Research Sources

The collection-specific public research references and their supported design context are in the Research basis section immediately below. They are functional benchmarks only and do not establish this project's schema or value lists.

## Z. Final Design Review

- **Status:** APPROVED under the Project Owner's prior instruction; not equivalent to production readiness.
- **Covered:** inventory-bounded purpose, candidate field baseline, flat-document constraint, known references, research context, and explicit unresolved questions.
- **Remaining:** Section X and any unresolved field-level validation or value rules.
- **Readiness:** reviewable collection-design baseline. Do not implement a blocker-marked schema until its semantic blocker is resolved.
## Research basis

- [SAP S/4HANA Process Technical Object](https://help.sap.com/docs/SAP_S4HANA_CLOUD/adbae5bcd5994f159bf2847a11397b61/f51855577f490322e10000000a44147b.html?locale=en-US) — Technical objects include equipment, locations, classification, installation history and status/change history; functional benchmark only.
- [SAP S/4HANA Asset Management Background](https://help.sap.com/doc/e2048712f0ab45e791e6d15ba5e20c68) — Describes equipment and functional locations as separately maintained technical objects with location history.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


