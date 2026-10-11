**FILE TYPE: PROJECT SPECIFIC**

# mes_tool_toolClass — Collection Specification

**Document ID:** MES-COL-TOOL-toolClass  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** tool  
**Collection type:** Reference  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection Overview

**Collection:** mes_tool_toolClass
**Module:** Tool Management
**Type:** Reference
**Primary business entity:** Tool Class
**Purpose:** Defines tool categories and applicability attributes.
**Business definition:** This collection stores the inventory-defined Tool Class identity or reference, owned by Tool Management, for the stated purpose. It does not imply additional parentage or field rules beyond current project decisions.
## B. Industry Research Summary

Research benchmarks support the business purpose and use cases; they do not define this MES's field rules. See sources in Section Y. Research coverage is limited to publicly documented functionality; no vendor internal schema was used.

## C. Real-World Manufacturing Scenarios

- Normal: use this collection as the inventory-defined identity/reference anchor.
- Lifecycle: create, update, retire and resolve historical references according to approved values where available.
- Integration/exception: preserve external identity separately where the dictionary defines it; unresolved provider rules remain OPEN.
- Detailed scenario evidence and coverage limits are in Section D.

## D. Functional Coverage Matrix

| Manufacturing scenario | Required information | Supporting fields | Covered? | Gap |
|---|---|---|---|---|
| Routine master/reference use | Stable identity, display, classification/lifecycle as applicable | See field dictionary | Partial | Inventory-level scope; detailed functional requirements are absent. |
| Retirement and historical reference | Preserve old references and prevent inappropriate new use | status where defined | Partial / OPEN | Values/transition rules unresolved except where an approved decision defines them. |
| External integration | Source identity and reconciliation where required | External fields only where defined | Partial / OPEN | Source ownership and mappings remain unresolved. |
| Audit | Who created/updated and when | Standard audit fields | Covered at field level | Actor identity source and retention policy remain OPEN. |
| Multi-plant use | Scope by plant where applicable | No plant field unless included in dictionary | OPEN | No plant-scoping rule is inferred for this master. |

## E. Complete Field Dictionary

| Field | BSON type | Requiredness | Business meaning | Validation | Reference | Index guidance | Reason | Evidence | Scenario |
|---|---|---|---|---|---|---|---|---|---|
| `_id` | String | Yes | Immutable globally unique 16-digit MES ID. | Exactly 16 digits; globally unique. | No | Default unique _id index | Primary identity | Project_Context DATA-003 | All references |
| `createdAt` | Date | Yes | UTC creation time | BSON Date | No | No index by default | Audit | KeyNamingConvention | Creation |
| `createdById` | String | Yes | 16-digit creator user/process ID | Pair with createdBy | User/process source OPEN | No index by default | Attribution | DATA-003 | Creation |
| `createdBy` | String | Yes | Creator display value paired with createdById | Non-empty, matching ID | User/process display pair | No index by default | Readable attribution | KeyNamingConvention | Creation |
| `updatedAt` | Date | Yes | UTC last update time | BSON Date >= createdAt | No | No index by default | Audit | KeyNamingConvention | Updates |
| `updatedById` | String | Yes | 16-digit updater user/process ID | Pair with updatedBy | User/process source OPEN | No index by default | Attribution | DATA-003 | Updates |
| `updatedBy` | String | Yes | Updater display value paired with updatedById | Non-empty, matching ID | User/process display pair | No index by default | Readable attribution | KeyNamingConvention | Updates |
| `toolClassCode` | String | Yes (proposed) | Stable code for the tool class. | Pattern and uniqueness scope OPEN. | No | Candidate code lookup; uniqueness OPEN | Consistent Tool classification. | Inventory; SAP PRT classification | Tool registration/compatibility |
| `toolClassName` | String | Yes | Human-readable class name. | Non-empty. | No | Optional search | Tool-class display. | SAP PRT class documentation | Tool selection |
| `toolClassDescription` | String | No | Scalar description of class membership intent. | Plain text; repeated class attributes not embedded. | No | No index by default | Clarify class use. | Inventory | Classification administration |
| `status` | String | Yes (proposed) | Lifecycle for new Tool assignments. | Vocabulary/transitions OPEN. | No | Candidate status filter | Retain historic class assignments. | Project lifecycle rule OPEN | Tool selection |

## F. Field Groups

- **Identity and business code:** _id, toolClassCode, toolClassName
- **Lifecycle:** status
- **Audit:** createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy

## G. ID–Name Reference Pairs

No business-entity references; audit actor pairs still apply.

## H. System IDs and Business Numbers

- MES `_id`: 16-digit String, globally unique.
- Business/source identifier fields: see field dictionary. Patterns and uniqueness are OPEN except operationCode and scrapReasonCode.
- External identifiers are distinct from MES IDs.

## I. Data Types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## J. Required, Optional and Conditional Fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## K. Validation Rules

- Flat top-level scalar fields only; no nested documents or arrays.
- MES IDs are 16-digit strings; external identifiers remain separate.
- Enforce only approved code patterns and uniqueness scopes. Do not invent OPEN scopes or status vocabularies.
- Required ID/display pairs are complete and consistent; optional pairs are both absent or both populated.
- Audit actor pairs are complete; updatedAt >= createdAt.
- Retired reference records remain historically resolvable when established by decision.

## L. Status Values and State Transitions

No approved values.

## M. Relationships with Other Collections

**Outgoing:** Tool records may reference Tool Class by toolClassId + toolClassName; verify against Tool specification. Tool is distinct from Asset under DEC-022.  
**Incoming:** mes_tool_tool and compatibility rules.  
**Dependencies:** Tool class applicability attributes are not defined; no class-characteristic collection exists in inventory.

## N. External System Integration

No external integration identifier is specified in this design. Confirm source mapping before implementation.

## O. Recommended Indexes

- Default unique _id index.
- Candidate code lookup; uniqueness OPEN — Tool registration/compatibility; verify query frequency and selectivity before implementation.
- Optional search — Tool selection; verify query frequency and selectivity before implementation.
- Candidate status filter — Tool selection; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## P. Audit and Historical Data

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## Q. Real-World Manufacturing Exceptions

- Retired/obsolete reference values must not invalidate historical transactions where project decisions establish historical resolution.
- Rejected or unmapped source records should remain identifiable for correction; exact workflow is OPEN.
- No state transitions, error-code taxonomies or extra fields are invented here.

## R. Missing Field Assessment

No additional field is added without requirements. The key gaps remain: Code scope, classification vocabulary, repeated applicability characteristics under flat-only rule, compatibility relationship. Do not merge Tool and Asset.

## S. Missing Collection Assessment

No new collection is added by this specification. The current inventory remains the collection-set authority. Any repeated item or missing owner discovered later must be reported for inventory/change control; arrays remain prohibited.

## T. Cross-Collection Consistency Check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: CONDITIONAL — confirm Tool reference pair in its specification.
- Status vocabulary: OPEN — no approved values.
- Unresolved dependencies: see Section N; no silent resolution.

## U. Field Completeness Metrics

- Proposed fields total: 11
- Required by convention/approved decision: 8
- Proposed required: 2
- Optional: 1
- Entity reference pairs (excluding audit pairs): 0
- Status/control fields: 1
- Audit fields: 6
- External identifier/locator fields: 0
- Scenarios evaluated: 5
- Scenarios fully established by detailed project requirements: 0 (collection-specific requirements were unavailable)
- Research-backed field rows: 10 (evidence is shown per field; estimates are not a completeness claim)
- Unresolved functional/design gaps: see Sections R and X.

Scenario Coverage Percentage: 0% established from detailed requirements; this is a documentation-evidence metric, not a claim of functional failure.

Field list: _id, createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy, toolClassCode, toolClassName, toolClassDescription, status

## V. One Representative MongoDB Document

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  toolClassCode: "<toolClassCode>",
  toolClassName: "<toolClassName>",
  toolClassDescription: "<toolClassDescription>",
  status: "<status>",
  createdAt: ISODate("2026-06-01T08:00:00.000+0000"),
  createdById: "1000000000000002",
  createdBy: "MES Configuration Service",
  updatedAt: ISODate("2026-06-01T08:00:00.000+0000"),
  updatedById: "1000000000000002",
  updatedBy: "MES Configuration Service"
}

## W. Assumptions

- The current inventory purpose and module owner bound this design.
- ID/display duplication is intentional strategic denormalization; copied-name synchronization policy remains OPEN unless decided.
- APPROVED is the requested document status; it does not close OPEN business rules.

## X. Open Issues

Code scope, classification vocabulary, repeated applicability characteristics under flat-only rule, compatibility relationship. Do not merge Tool and Asset.

## Y. Research Sources

- [SAP Classifying Production Resources/Tools](https://help.sap.com/docs/SAP_S4HANA_ON-PREMISE/000598f44016404fa82337e65a45264c/6985b6535fe6b74ce10000000a174cb4.html?locale=en-US&state=PRODUCTION&version=2025.001) — Describes class assignment and characteristic-based lookup for production tools; project class structure remains OPEN.
- [SAP Digital Manufacturing Tool API](https://help.sap.com/docs/sap-digital-manufacturing/service-catalog/e274644077d94140bf1f61f78dee6985.html) — Describes reusable, movable production tools and usage/loading operations.

## Z. Final Design Review

Status label: APPROVED under Project Owner instruction. This is not production readiness.

Covered: inventory-defined purpose, identified fields, types, references, research evidence, and flat representation.

Remaining: OPEN items in Section X; no unsupported lifecycle, code, source, or integration rule is treated as resolved.



