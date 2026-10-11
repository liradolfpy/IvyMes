**FILE TYPE: PROJECT SPECIFIC**

# mes_trace_labelDefinition — Collection Specification

**Document ID:** MES-COL-TRACE-labelDefinition  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** trace  
**Collection type:** Configuration  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Defines controlled label identity/content version and applicable product or traceability scope without binding to a printer technology.

## B. Business definition

Label Definition controls a reusable label template/version. Rendered label content and print events are separate concerns; repeated label elements must not be embedded as arrays or nested documents.

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
| `labelDefinitionCode` | String | Yes (proposed) | Business identifier for label template. | Pattern/uniqueness OPEN. | No | Candidate code+revision | Select label template. | Inventory; SAP label management | Print selection |
| `labelDefinitionName` | String | Yes | Readable label definition name. | Non-empty. | No | Candidate text search | Administration/display. | DATA-003 | Label setup |
| `labelDefinitionRevision` | String | Yes (proposed) | Controlled content revision. | Revision/effectivity OPEN. | No | Candidate code+revision | Version control. | Inventory; label definition | Traceability |
| `productId` | String | Conditional (proposed) | Product scope for label applicability. | If present pair with productName. | Yes: productId + productName -> mes_matl_product | Candidate productId | Product-specific label rules. | Inventory; DATA-003 | Print label |
| `productName` | String | Conditional | Product display snapshot. | Both fields present or absent. | Yes: paired with productId | No index by default | Readable context. | DATA-003 | Label administration |
| `labelContentLocator` | String | Conditional | Opaque external locator to rendered template/content if managed outside MES. | Source owner, syntax and version immutability OPEN. | External locator, not entity ref unless source is managed entity. | Candidate locator lookup | Resolve template without embedding content. | Inventory; flat-document rule | Label rendering |
| `status` | String | Yes (proposed) | Label-definition lifecycle/effectivity state. | Values/transitions OPEN. | No | Candidate status filter | Control printing revisions. | Lifecycle rules OPEN | Label selection |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `productId` — Yes: productId + productName -> mes_matl_product.
- `productName` — Yes: paired with productId.

## G. Validation rules

- Flat top-level scalar fields only; no nested documents or arrays.
- MES IDs are 16-digit strings; external identifiers remain separate.
- Enforce only approved code patterns and uniqueness scopes. Do not invent OPEN scopes or status vocabularies.
- Required ID/display pairs are complete and consistent; optional pairs are both absent or both populated.
- Audit actor pairs are complete; updatedAt >= createdAt.
- Retired reference records remain historically resolvable when established by decision.

## H. Status values

Label-definition status, not print-event status; vocabulary OPEN.

## I. Relationships

**Outgoing:** Product pair and possible external content locator.  
**Incoming:** Label Print Event and traceability workflows.  
**Dependencies:** Product master; print interface, label content source, label element representation and approval rules unresolved.

## J. Recommended indexes

- Default unique _id index.
- Candidate code+revision — Print selection; verify query frequency and selectivity before implementation.
- Candidate text search — Label setup; verify query frequency and selectivity before implementation.
- Candidate code+revision — Traceability; verify query frequency and selectivity before implementation.
- Candidate productId — Print label; verify query frequency and selectivity before implementation.
- Candidate locator lookup — Label rendering; verify query frequency and selectivity before implementation.
- Candidate status filter — Label selection; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  labelDefinitionCode: "<labelDefinitionCode>",
  labelDefinitionName: "<labelDefinitionName>",
  labelDefinitionRevision: "<labelDefinitionRevision>",
  productId: "<productId>",
  productName: "<productName>",
  labelContentLocator: "<labelContentLocator>",
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

BLOCKER: content source and locator ownership; whether label elements need an inventory child collection; revision/approval/effectivity; product/lot/serial scope; language and print technology boundaries; status vocabulary.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: OPEN — external content source and repeated label element owner unresolved.
- Status vocabulary: OPEN — values not approved.
- Unresolved dependencies: see Section N; no silent resolution.


## P. Audit and Historical Data

Standard audit fields remain flat and paired. Preserve revisions, effectivity, and historical target/label/transition context according to future approved rules. Retention remains OPEN.

## Q. Real-World Manufacturing Exceptions

Do not infer capacity targets, changeover units, KPI target values, or external label-rendering behavior. Preserve the source context needed for correction only when supported by approved fields.

## R. Missing Field Assessment

Collection-specific requirements are incomplete. Review with planning, production, traceability, performance, and data-architecture roles. Known gaps: BLOCKER: content source and locator ownership; whether label elements need an inventory child collection; revision/approval/effectivity; product/lot/serial scope; language and print technology boundaries; status vocabulary.

## S. Missing Collection Assessment

No collection is added. Any repeated transition/label content dimensions must be represented as separate records in an identified owner collection or returned for inventory review; no arrays are permitted.

## T. Cross-Collection Consistency Check

- Inventory identity/module/type: checked against v1.6.
- Defined references use singular ID/name pairs.
- Flat-document convention is respected.
- Numeric types, external ownership, scope precedence, and lifecycle rules remain OPEN where stated.
- Work Center is not used as a resource identity.

## U. Field Completeness Metrics

See Sections E, F, and J for field, relationship, and index baselines. Detailed source requirements are not available; no completeness percentage is claimed. Known gaps: BLOCKER: content source and locator ownership; whether label elements need an inventory child collection; revision/approval/effectivity; product/lot/serial scope; language and print technology boundaries; status vocabulary.

## V. One Representative MongoDB Document

The single illustrative flat structure is in Section L. No additional example documents are generated.

## W. Assumptions

- Current inventory and approved ownership decisions determine scope.
- ID/name duplication is intentional strategic denormalization.
- Repeated concepts are separate records.
- APPROVED is the requested status, not closure of open issues.

## X. Open Issues

BLOCKER: content source and locator ownership; whether label elements need an inventory child collection; revision/approval/effectivity; product/lot/serial scope; language and print technology boundaries; status vocabulary.

## Y. Research Sources

See Research basis below for collection-specific public sources and the supported operational scenarios. They do not establish project values or schema.

## Z. Final Design Review

- **Status:** APPROVED under the prior Project Owner instruction; not production readiness.
- **Covered:** bounded purpose, candidate field set, references, research, and explicit dependencies.
- **Remaining:** Section X; target/content semantic blockers must be resolved before implementation.
## Research basis

- [SAP Business ByDesign Labels](https://help.sap.com/docs/SAP_BUSINESS_BYDESIGN/2754875d2d2a403f95e58a41a9c7d6de/2cb4bb13722d1014bc61f117edda772b.html) — Labels identify products/logistics units for tracking and can be printed with varied content; informs scope only.
- [SAP Label Definition](https://help.sap.com/docs/SAP_S4HANA_ON-PREMISE/ff6d35a34fbe4bea99e4ac41b0d5db03/e18ab051077f1e49e10000000a445394.html) — Labels may contain text elements and multilingual output; repeated label element ownership is an open flat-model question.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


