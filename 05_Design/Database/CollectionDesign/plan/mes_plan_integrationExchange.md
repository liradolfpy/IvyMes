**FILE TYPE: PROJECT SPECIFIC**

# mes_plan_integrationExchange — Collection Specification

**Document ID:** MES-COL-PLAN-integrationExchange  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** plan  
**Collection type:** Audit  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Records inbound or outbound plan/schedule exchange activity and its processing outcome for traceability.

## B. Business definition

One integration-exchange record captures an exchange attempt or message and its correlation to an external endpoint and planning context where available; it is not a source-system master.

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
| `exchangeDirection` | String | Yes (proposed) | Inbound/outbound direction. | Allowed values and retry semantics OPEN. | No | Candidate filter | Interpret exchange flow. | Inventory; SAP Integration Messages | Inbound/outbound processing |
| `sourceSystemCode` | String | Yes (proposed) | External endpoint/system code. | Non-empty; vocabulary and owner OPEN. | External scalar, not MES entity | Candidate correlation compound index | Identify external namespace. | DEC-022; SAP Integration Guide | Reconciliation |
| `messageCorrelationId` | String | Conditional (proposed) | Correlates a message and processing steps. | Source-scoped uniqueness/retention OPEN. | External correlation token | Candidate sourceSystemCode + messageCorrelationId | Retry and reconciliation. | SAP Integration Guide | Message tracing |
| `externalDocumentId` | String | Conditional | External plan/schedule business identifier. | Separate from MES _id; namespace OPEN. | External locator, not MES entity reference | Candidate source + externalDocumentId | Cross-system mapping. | Inventory; integration docs | Reference reconciliation |
| `exchangeStatus` | String | Yes (proposed) | Processing outcome for this exchange. | Vocabulary, retry states and transitions OPEN. | No | Candidate status/time index | Operational dashboard. | SAP Submessage Status (benchmark only) | Exception handling |
| `receivedAt` | Date | Conditional | Inbound receipt time. | BSON Date; requiredness direction-dependent. | No | Candidate time index | Chronology. | Inventory; integration logging | Inbound audit |
| `sentAt` | Date | Conditional | Outbound send time. | BSON Date; requiredness direction-dependent. | No | Candidate time index | Chronology. | Inventory; integration logging | Outbound audit |
| `errorSummary` | String | No | Scalar short failure summary. | No payload/nested diagnostic object. | No | No index by default | Failure triage. | Inventory exchange outcome | Exception review |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

No business-entity references; audit actor pairs still apply.

## G. Validation rules

- Flat top-level scalar fields only; no nested documents or arrays.
- MES IDs are 16-digit strings; external identifiers remain separate.
- Enforce only approved code patterns and uniqueness scopes. Do not invent OPEN scopes or status vocabularies.
- Required ID/display pairs are complete and consistent; optional pairs are both absent or both populated.
- Audit actor pairs are complete; updatedAt >= createdAt.
- Retired reference records remain historically resolvable when established by decision.

## H. Status values

exchangeStatus is collection-specific processing outcome; values/transitions OPEN; no vendor vocabulary adopted.

## I. Relationships

**Outgoing:** External plan/schedule reference fields are unresolved; sourceSystemCode is an external scalar.  
**Incoming:** Integration monitoring and planning reconciliation consumers.  
**Dependencies:** Planning/schedule owners and exact reference pairs unresolved. No external-system master collection is listed. Payload retention/security OPEN.

## J. Recommended indexes

- Default unique _id index.
- Candidate filter — Inbound/outbound processing; verify query frequency and selectivity before implementation.
- Candidate correlation compound index — Reconciliation; verify query frequency and selectivity before implementation.
- Candidate sourceSystemCode + messageCorrelationId — Message tracing; verify query frequency and selectivity before implementation.
- Candidate source + externalDocumentId — Reference reconciliation; verify query frequency and selectivity before implementation.
- Candidate status/time index — Exception handling; verify query frequency and selectivity before implementation.
- Candidate time index — Inbound audit; verify query frequency and selectivity before implementation.
- Candidate time index — Outbound audit; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  exchangeDirection: "<exchangeDirection>",
  sourceSystemCode: "<sourceSystemCode>",
  messageCorrelationId: "<messageCorrelationId>",
  externalDocumentId: "<externalDocumentId>",
  exchangeStatus: "<exchangeStatus>",
  receivedAt: <value>,
  sentAt: <value>,
  errorSummary: "<errorSummary>",
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

BLOCKER: one-record grain (attempt/message/aggregate); direction/status vocabulary; exact plan/schedule ID/name fields; external namespace; payload retention; retry/idempotency; correlation uniqueness; directional timestamps.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: OPEN — plan/schedule references not specified.
- Status vocabulary: OPEN — taxonomy not approved.
- Unresolved dependencies: see Section N; no silent resolution.


## P. Audit and Historical Data

Standard audit fields are flat: createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity source, retention period, and immutable-history requirements remain OPEN unless an approved project rule states otherwise. Lifecycle or location history must not be embedded as arrays.

## Q. Real-World Manufacturing Exceptions

Handle retired, unmapped, or unavailable references without silently substituting a different identity. Store only scalar exception context already represented in the field dictionary. Detailed correction, retry, and reconciliation behavior remains OPEN where applicable.

## R. Missing Field Assessment

The current project copy does not provide collection-specific functional requirement artifacts for this item. Potential missing fields must be evaluated with operations, planning, engineering, quality, integration, maintenance, material, and database roles before implementation. Known assessment items: BLOCKER: one-record grain (attempt/message/aggregate); direction/status vocabulary; exact plan/schedule ID/name fields; external namespace; payload retention; retry/idempotency; correlation uniqueness; directional timestamps.

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
- Unresolved gaps: BLOCKER: one-record grain (attempt/message/aggregate); direction/status vocabulary; exact plan/schedule ID/name fields; external namespace; payload retention; retry/idempotency; correlation uniqueness; directional timestamps.

## V. One Representative MongoDB Document

The single illustrative flat document structure is provided in Section L. No second document or sample-data set is added here.

## W. Assumptions

- The inventory determines collection identity, module, type, and bounded purpose.
- DATA-001 requires separate flat records for repeated concepts.
- DATA-003 ID/display duplication is intentional strategic denormalization.
- APPROVED is the user's requested specification status; OPEN questions remain unapproved and unresolved.

## X. Open Issues

BLOCKER: one-record grain (attempt/message/aggregate); direction/status vocabulary; exact plan/schedule ID/name fields; external namespace; payload retention; retry/idempotency; correlation uniqueness; directional timestamps.

## Y. Research Sources

The collection-specific public research references and their supported design context are in the Research basis section immediately below. They are functional benchmarks only and do not establish this project's schema or value lists.

## Z. Final Design Review

- **Status:** APPROVED under the Project Owner's prior instruction; not equivalent to production readiness.
- **Covered:** inventory-bounded purpose, candidate field baseline, flat-document constraint, known references, research context, and explicit unresolved questions.
- **Remaining:** Section X and any unresolved field-level validation or value rules.
- **Readiness:** reviewable collection-design baseline. Do not implement a blocker-marked schema until its semantic blocker is resolved.
## Research basis

- [SAP Digital Manufacturing Submessage Type and Status](https://help.sap.com/docs/sap-digital-manufacturing/execution/submessage-type-and-status?locale=en-US) — Messages can produce submessages with distinct processing outcomes; informs exchange lifecycle scenarios, not project values.
- [SAP Digital Manufacturing Integration Guide](https://help.sap.com/doc/f6b2ab2222794bebad4c0dcd33138e71/latest/en-US/SAP_DMC_Integration_Guide_enUS.pdf) — Integration message tracing uses correlation IDs; informs correlation/reconciliation scenarios.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


