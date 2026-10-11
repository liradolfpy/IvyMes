**FILE TYPE: PROJECT SPECIFIC**

# Collection Specification — `mes_matl_unitOfMeasure`

**Specification status:** CURRENT — updated under GEN-001 on 2026-10-11
**Design scope:** Current collection design baseline. Open standards, conversion, and ownership questions remain unresolved.  
**Version:** 1.0  
**Module:** Material Management (`matl`)  
**Collection type:** Reference  
**Inventory authority:** `05_Design/Database/Collection_Inventory.md` (CURRENT)

## A. Collection overview

`mes_matl_unitOfMeasure` provides a controlled MES identity and display value for one unit used by material, production, inventory, quality, and measurement records. It defines a unit identity only. It does not encode material-specific conversions, packaging ratios, or unit dimensions through arrays or nested conversion tables.

**Business definition:** A unit of measure (UOM) is a standardized or project-defined label describing the measurement basis for a quantity, such as piece, kilogram, or second. A unit’s code and name identify it; conversion factors require explicit context and are not assumed to be universal.

## B. Research summary

| Source | Documented capability / standard | Design implication |
|---|---|---|
| SAP Digital Manufacturing, *Unit of Measure* ([source](https://help.sap.com/docs/sap-digital-manufacturing/aa23dac9b58b48c6abda61fc704fb180/c7a911c2bb4d47dbb38e39d4b908c051.html?locale=en-US&state=PRODUCTION&version=latest)) | SAP documents standard and custom units of measure for business objects. | Keep code/name identity as reference data and allow governed custom units only after project policy defines them. |
| SAP Digital Manufacturing, *Manage Materials* ([source](https://help.sap.com/docs/sap-digital-manufacturing/execution/manage-materials)) | Material master carries a base UOM; alternate UOMs and numerator/denominator conversions are associated with a particular material and can be imported from ERP. | Do not place conversion factors in the global UOM master. Material-specific conversion belongs to material/UOM relationship design, not this collection. |
| SAP S/4HANA Help, *Material Master Unit of Measure Setup* ([source](https://help.sap.com/docs/SAP_S4HANA_ON-PREMISE/0f4ab800d01c4366b0c9aaff06a64320/9c69e50c986844b292ffc962ce65fb6f.html)) | Describes numerator/denominator conversion between alternate and base units in the material master. | Explicitly keep context-dependent conversion out of a unit identity record. |

**Standards applicability:** UCUM or ISO/IEC 80000 may be candidates for interoperable unit codes and quantities, but this project has not selected a unit catalog or coding standard. No standard is declared mandatory by this specification. ISA-95 material models are relevant to exchanging material/quantity context but do not select this project’s UOM vocabulary. No compliance claim is made.

**Research limitation:** Vendor documentation presents user-facing objects, not database layouts. Project Context now records general MES settings, but measurement dimensions, SI/UCUM/ERP code authority, precision, rounding, and conversion handling remain open.

## C. Scenarios and lifecycle

Applies to production quantity, material stock/consumption, inspection measurements, weight/length/volume, cycle-time, and energy records. Normal operation uses one unambiguous unit per scalar quantity. Exceptions include material-specific alternate unit, conversion changes, imported nonstandard codes, retired units still referenced historically, and incompatible dimensions.

Create when the controlled unit catalog is provisioned. Rename only through governance; code identity must remain stable. A unit referenced by history remains resolvable after retirement. Lifecycle vocabulary and conversion/rounding policy remain open.

## D. Functional coverage matrix

| Persona / need | Coverage | Boundary |
|---|---|---|
| Operator | Readable unit beside entered or displayed quantity. | Field-entry validation belongs to transaction/UI requirements. |
| Material controller | Resolve base and alternate unit identities. | Conversion ratios are material-specific and not in this collection. |
| Quality engineer | Identify measurement unit consistently. | Characteristic dimension/tolerance compatibility belongs to QA specs. |
| Planner / management | Compare quantity units where compatible. | Cross-unit aggregation requires current conversion service/rules. |
| Integration engineer | Maintain code/name mapping. | External code system and mapping are unresolved. |

## E. Complete field dictionary

| Field | BSON type | Requiredness | Meaning / validation |
|---|---|---|---|
| `_id` | String | Required | Immutable custom 16-digit MES ID. |
| `uomCode` | String | Required | Stable unit code; exact authority and code system open. |
| `uomName` | String | Required | Human-readable singular display name, e.g. “piece” or “kilogram”; controlled language policy open. |
| `uomSymbol` | String | Optional | Display symbol if defined, e.g. `kg`; uniqueness and locale handling open. |
| `description` | String | Optional | Plain-text usage definition. |
| `physicalDimension` | String | Optional | Broad dimension label only when governed vocabulary is defined; do not use as a conversion factor. Current inclusion is contingent on a defined dimension catalog. |
| `status` | String | Required | UOM availability/lifecycle state; allowed values and semantics open. |
| `createdAt` | BSON Date | Required | UTC creation timestamp. |
| `createdById` | String | Required | Creator MES ID. |
| `createdBy` | String | Required | Creator display name. |
| `updatedAt` | BSON Date | Optional | Update time, conditional full update audit group. |
| `updatedById` | String | Optional | Updater MES ID. |
| `updatedBy` | String | Optional | Updater display name. |

**Field decision note:** `physicalDimension` is included to support detection of incompatible quantities only if a dimension vocabulary is defined and documented. If not defined, omit it before implementation; it is not a conversion definition.

## F. Groups, references, identifiers

Identity: `uomCode`, `uomName`, optional symbol/description/dimension. Lifecycle: `status`. Audit: standard fields. No outgoing entity references. Quantity-bearing collections should reference UOM with singular `uomId` + `uomName` (or the project's current display form); confirm exact key through cross-collection review.

`uomCode` is a controlled code, not an `_id` or sequential business number. Code system and uniqueness are open; recommend unique code within the chosen catalog. External aliases must not be conflated with MES identity.

## G. Data types and requiredness

String and BSON Date only. Required: `_id`, `uomCode`, `uomName`, `status`, creation audit. Optional: symbol, description, dimension, update audit group. Numeric quantities and conversion factors do not belong in this master.

## H. Validation rules

1. `_id` is immutable unique 16-digit string.
2. Code/name are nonblank, trimmed strings.
3. Code uniqueness must follow the selected code-system scope.
4. A referenced unit must exist and be eligible for new transactions; retired units remain resolvable historically.
5. No conversion may be inferred from two unit labels alone.
6. If `physicalDimension` is retained, enforce its vocabulary and compatibility rules only after the rules are defined and documented.
7. Audit fields are paired and update fields are all-or-none.

## I. Status values and transitions

Required `status` means whether the unit may be selected for new records while remaining resolvable for historical records. Allowed values, meanings, transitions, and retirement behavior are **OPEN**. No universal lifecycle vocabulary is imposed.

## J. Relationships

Referenced by material/product, BOM quantities, production execution, inventory, material movement/consumption, inspection results/specifications, cycle-time, energy, and other quantity/measurement records. It references no other MES business entity.

## K. External systems

ERP material masters may supply base/alternate UOMs and conversion ratios. SAP documentation confirms context-specific conversion data may travel with the material. This schema does not select SAP or any other integration. A future mapping must record source code separately and define precedence without changing MES `_id`.

## L. Recommended indexes

| Index | Unique? | Query supported | Rationale / cost |
|---|---:|---|---|
| `{ _id: 1 }` | Yes | Direct reference lookup | Mandatory identity. |
| `{ uomCode: 1 }` | Proposed unique; code authority confirmation required | Code resolution / duplicate detection | Standard master lookup; defer enforcement until code system and case normalization are defined. |
| `{ uomName: 1 }` | No | Selection/search | Convenient low-volume lookup; names may not be unique across languages. |
| `{ physicalDimension: 1, status: 1 }` | No, conditional | Filter compatible units | Only useful if the dimension catalog is defined; do not create otherwise. |

## M. Audit and history

Standard creation audit required and update group optional. No separate history collection is current. UOM code must not be reassigned after retirement absent an explicit decision; old quantity records keep their original unit reference/snapshot.

## N. Missing-field assessment

| Gap | Scenario / why | Target | Priority | Evidence / disposition |
|---|---|---|---|---|
| Material-specific conversion | Alternate packaging or stock unit differs from base unit | Material-UOM relationship or material spec | High | SAP Manage Materials describes alternate UOM numerator/denominator scoped to material. Not part of unit identity. |
| Code system/aliases | ERP and MES use differing codes | Mapping convention or integration entity | High | SAP supports standard/custom units; project authority remains unselected. |
| Precision/rounding | Fractional consumption or measurement | Quantity field requirements/service | High | No Decimal constructors; Double rule applies only to current fields. No UOM-specific numeric rule set. |
| Localization | Multi-language display names | Locale/content owner TBD | Medium | Vendor docs support multilingual material description; this project has no localization requirement. |

## O. Missing-collection assessment

No new collection created. The inventory already includes material/BOM and quantity transaction collections; review whether a material-UOM relationship is required to store alternate units without arrays. Do not add until inventory review confirms no existing collection can own the repeated relation.

## P. Cross-collection consistency check

Use one consistent unit reference pair in all dependent collections, ideally `uomId` + `uomName`; do not vary with plural keys. Keep numeric BSON types consistent with DATA-002 and field-specific current requirements. Unit codes are never system IDs.

## Q. Completeness metrics

| Metric | Result |
|---|---:|
| Proposed fields | 13 |
| Required / optional / conditional fields | 7 / 5 / 1 |
| ID–Name business reference pairs | 0 outgoing; 1 standard audit pair plus optional update pair |
| Status/control fields | 1 |
| Audit fields | 6 maximum |
| External integration fields | 0 |
| Applicable scenario families evaluated / covered | 7 / 5 (71.4%) |
| Unresolved functional gaps | 4 |
| Research-backed field groups | unit identity; material-specific conversion boundary |
| Project-specific inferred field groups | controlled code, dimension placeholder, lifecycle, audit |

## R. Representative MongoDB document

```javascript
{
  "_id": "3000293847561029",
  "uomCode": "KGM",
  "uomName": "kilogram",
  "uomSymbol": "kg",
  "description": "Unit of mass",
  "physicalDimension": "<dimension vocabulary pending rule definition>",
  "status": "<value OPEN — value not defined>",
  "createdAt": ISODate("2026-10-08T09:30:00.000+0000"),
  "createdById": "5566778899001122",
  "createdBy": "MES Master Data Service"
}
```

## S. Assumptions

- Inventory assigns UOM ownership to Material Management.
- One record represents one unit identity, not a conversion relation.
- MES system ID is distinct from an industry/ERP code.
- No conversion, numeric precision, or rounding rule is inferred.

## T. Open issues

1. Select the authoritative code catalog (e.g. ERP, UCUM, another current catalog, or governed custom set).
2. Define and document code uniqueness, normalization, aliases, language, and symbol behavior.
3. Decide whether `physicalDimension` is retained and define its controlled vocabulary.
4. Define material-specific alternate-unit relation ownership and conversion rules.
5. Define quantity precision, rounding, and conversion service boundaries.
6. Define and document lifecycle vocabulary and historical reference policy.

## U. Final design review

**Status:** CURRENT under GEN-001, updated 2026-10-11. Schema records a controlled unit identity and deliberately keeps conversion contextual. Dimension vocabulary and code governance remain open; do not deploy those constraints until defined and documented.

**Research sources:** [SAP DMC Unit of Measure](https://help.sap.com/docs/sap-digital-manufacturing/aa23dac9b58b48c6abda61fc704fb180/c7a911c2bb4d47dbb38e39d4b908c051.html); [SAP DMC Manage Materials](https://help.sap.com/docs/sap-digital-manufacturing/execution/manage-materials); [SAP S/4HANA Material Master UOM Setup](https://help.sap.com/docs/SAP_S4HANA_ON-PREMISE/0f4ab800d01c4366b0c9aaff06a64320/9c69e50c986844b292ffc962ce65fb6f.html).


Documentation update — 2026-10-11: Apply GEN-001 / DEC-018; remove separate documentation sign-off gates, retain open requirements and operational controls, and use current artifact status. See CHG-022.
