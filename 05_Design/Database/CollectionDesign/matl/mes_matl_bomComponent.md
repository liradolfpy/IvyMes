**FILE TYPE: PROJECT SPECIFIC**

# mes_matl_bomComponent — Collection Specification

**Document ID:** MES-COL-MATL-bomComponent  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** matl  
**Collection type:** Relationship  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Represents one material component line in one Bill of Material as a separate flat record.

## B. Business definition

BOM Component links a parent BOM to one Material identity and captures line-specific sequence/quantity attributes. Product remains the BOM header identity; component identity is Material.

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
| `billOfMaterialId` | String | Yes | Parent BOM identity. | 16-digit ID resolving mes_matl_billOfMaterial. | Yes: billOfMaterialId + billOfMaterialName -> mes_matl_billOfMaterial | Candidate parent ID + componentSequence | Flat parent-child grouping. | DATA-001; inventory | BOM expansion |
| `billOfMaterialName` | String | Yes | BOM display snapshot. | Paired with billOfMaterialId. | Yes: paired with billOfMaterialId | No index by default | Read without lookup. | DATA-003 | Component display |
| `componentSequence` | Int32 | Yes (proposed) | Position/order of component line within BOM revision. | Positive integer; uniqueness scope OPEN. | No | Candidate unique BOM+sequence after scope approval | Deterministic order for flat rows. | SAP BOM item number/order | BOM display/explosion |
| `materialId` | String | Yes | Component Material identity. | 16-digit ID resolving mes_matl_material; not Product. | Yes: materialId + materialName -> mes_matl_material | Candidate materialId | Material component identity. | DEC-022; DATA-003 | Material demand |
| `materialName` | String | Yes | Material display snapshot. | Paired with materialId. | Yes: paired with materialId | No index by default | Avoid routine lookup. | DATA-003 | BOM display |
| `componentQuantity` | Numeric BSON type OPEN | Yes (proposed) | Required component amount for BOM basis quantity. | Non-negative; exact precision/type and scaling OPEN; no Decimal constructor. | No | No index by default | Material planning/consumption basis. | SAP BOM Items; unit conventions OPEN | Material planning |
| `unitOfMeasureId` | String | Yes (proposed) | Unit for component quantity. | 16-digit ID resolving mes_matl_unitOfMeasure. | Yes: unitOfMeasureId + unitOfMeasureName -> mes_matl_unitOfMeasure | Candidate unitOfMeasureId | Interpret quantity. | DATA-003; DEC-022 | Quantity interpretation |
| `unitOfMeasureName` | String | Yes (proposed) | Unit display snapshot. | Paired with unitOfMeasureId. | Yes: paired with unitOfMeasureId | No index by default | Avoid lookup. | DATA-003 | BOM display |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `billOfMaterialId` — Yes: billOfMaterialId + billOfMaterialName -> mes_matl_billOfMaterial.
- `billOfMaterialName` — Yes: paired with billOfMaterialId.
- `materialId` — Yes: materialId + materialName -> mes_matl_material.
- `materialName` — Yes: paired with materialId.
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

No status field is proposed for individual component lines unless BOM lifecycle semantics require it.

## I. Relationships

**Outgoing:** BOM, Material, Unit of Measure ID/name pairs.  
**Incoming:** Material reservation/consumption and genealogy may use component detail references.  
**Dependencies:** mes_matl_billOfMaterial, mes_matl_material, mes_matl_unitOfMeasure. Component record ordering and quantity type/precision unresolved.

## J. Recommended indexes

- Default unique _id index.
- Candidate parent ID + componentSequence — BOM expansion; verify query frequency and selectivity before implementation.
- Candidate unique BOM+sequence after scope approval — BOM display/explosion; verify query frequency and selectivity before implementation.
- Candidate materialId — Material demand; verify query frequency and selectivity before implementation.
- Candidate unitOfMeasureId — Quantity interpretation; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  billOfMaterialId: "<billOfMaterialId>",
  billOfMaterialName: "<billOfMaterialName>",
  componentSequence: <value>,
  materialId: "<materialId>",
  materialName: "<materialName>",
  componentQuantity: <value>,
  unitOfMeasureId: "<unitOfMeasureId>",
  unitOfMeasureName: "<unitOfMeasureName>",
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

Quantity BSON type/precision, BOM basis quantity, sequence uniqueness, effectivity inheritance, alternative/substitute group representation, line revision/history, and unit conversion rule. Numeric type is blocking for final sample generation.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: PASS — all known references use ID/display pairs.
- Status vocabulary: N/A — no status field defined.
- Unresolved dependencies: see Section N; no silent resolution.


## P. Audit and Historical Data

Standard audit fields remain flat and paired. Preserve revision/effectivity and historical references according to approved project rules; retention and immutable-history policies remain OPEN.

## Q. Real-World Manufacturing Exceptions

Do not silently substitute a Product or Material, infer a quantity conversion, or accept ambiguous/overlapping revisions. Record correction/reconciliation only through approved fields and workflows.

## R. Missing Field Assessment

Collection-specific requirement artifacts are incomplete for this wave. Validate the field set with engineering, material control, quality, and database roles. Known gaps: Quantity BSON type/precision, BOM basis quantity, sequence uniqueness, effectivity inheritance, alternative/substitute group representation, line revision/history, and unit conversion rule. Numeric type is blocking for final sample generation.

## S. Missing Collection Assessment

No collection is added. Repeated composition lines use mes_matl_bomComponent. Repeated quality characteristic ownership remains OPEN and must be resolved under inventory/change control; do not embed arrays.

## T. Cross-Collection Consistency Check

- Inventory scope/type: checked against v1.6.
- Product vs Material distinction: consistent with DEC-022.
- Flat representation: no embedded component/characteristic arrays.
- Defined entity references use required ID/name pairs and singular field stems.
- Numeric types, effectivity, status, and missing ownership issues remain OPEN where stated.

## U. Field Completeness Metrics

Field counts and requiredness are in Section E; ID/name references in Section F; candidate indexes in Section J. Detailed collection requirements are unavailable, so no complete-scenario rate is claimed. Known gaps: Quantity BSON type/precision, BOM basis quantity, sequence uniqueness, effectivity inheritance, alternative/substitute group representation, line revision/history, and unit conversion rule. Numeric type is blocking for final sample generation.

## V. One Representative MongoDB Document

The only illustrative structure appears in Section L. No second example or 10-document set is generated.

## W. Assumptions

- Inventory and current decisions govern scope.
- DATA-001 requires repeated concepts as separate flat records.
- ID/name duplication is intentional strategic denormalization.
- APPROVED follows prior owner instruction; OPEN issues are not approved decisions.

## X. Open Issues

Quantity BSON type/precision, BOM basis quantity, sequence uniqueness, effectivity inheritance, alternative/substitute group representation, line revision/history, and unit conversion rule. Numeric type is blocking for final sample generation.

## Y. Research Sources

Collection-specific sources and their relevance are recorded under Research basis below. They support manufacturing scenarios, not project-specific schema or status values.

## Z. Final Design Review

- **Status:** APPROVED per Project Owner direction; not production readiness.
- **Covered:** bounded purpose, flat record proposal, known references, public research, and explicit gaps.
- **Remaining:** Section X; numeric and structural blockers must be resolved before implementation/sample generation.
## Research basis

- [SAP BOM Items](https://help.sap.com/docs/SAP_S4HANA_ON-PREMISE/5e23dc8fe9be4fd496f8ab556667ea05/ae94d7531a4d414de10000000a174cb4.html) — BOM item quantities are expressed relative to header base quantity and UOM; conversion behavior is material-master dependent.
- [SAP Material Bill of Material](https://help.sap.com/docs/SAP_S4HANA_ON-PREMISE/9905622a5c1f49ba84e9076fc83a9c2c/96f45c5463890d4ee10000000a423f68.html?locale=en-US&state=PRODUCTION&version=2025.001) — BOM is a structured product composition listing component quantity and unit; supports separate component records.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


