**FILE TYPE: PROJECT SPECIFIC**

# mes_prod_andonEvent — Collection Specification

**Document ID:** MES-COL-PROD-andonEvent  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** prod  
**Collection type:** Audit  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Stores one immutable-ish transition/event associated with an Andon Case.

## B. Business definition

Andon Event records one transition such as activation, escalation, acknowledgement, resolution, or cancellation, with a parent case reference. It has eventType, not a separate status field (DEC-005, DEC-012).

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
| `andonEventNo` | String | Yes | Approved event business number. | AE-YYYY-######; globally unique (DEC-013). | No | Unique andonEventNo | Event identity | DEC-013 | Event retrieval |
| `andonCaseId` | String | Yes | Parent Andon Case. | 16-digit ID paired with andonCaseName. | Yes: andonCaseId + andonCaseName -> mes_prod_andonCase | Candidate andonCaseId+eventTime | Connect lifecycle transitions | DEC-005; DATA-003 | Case history |
| `andonCaseName` | String | Yes | Andon Case display/business number snapshot. | Paired with andonCaseId. | Yes: paired with andonCaseId | No index by default | Readable case context | DATA-003 | Event display |
| `eventType` | String | Yes | Transition classification. | Activated, Escalated, Acknowledged, Resolved, Cancelled (DEC-012). | No | Candidate eventType+eventTime | Event semantics | DEC-012 | Case timeline |
| `eventTime` | Date | Yes | Occurrence time. | BSON Date. | No | Candidate andonCaseId+eventTime | Chronological event history | DEC-005 | Case timeline |
| `eventDescription` | String | No | Optional explanatory text. | Scalar text; no embedded structured payload. | No | No index by default | Additional event context | DATA-001 | Triage |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `andonCaseId` — Yes: andonCaseId + andonCaseName -> mes_prod_andonCase.
- `andonCaseName` — Yes: paired with andonCaseId.

## G. Validation rules

- Flat top-level scalar fields only; no nested documents or arrays.
- MES IDs are 16-digit strings; external identifiers remain separate.
- Enforce only approved code patterns and uniqueness scopes. Do not invent OPEN scopes or status vocabularies.
- Required ID/display pairs are complete and consistent; optional pairs are both absent or both populated.
- Audit actor pairs are complete; updatedAt >= createdAt.
- Retired reference records remain historically resolvable when established by decision.

## H. Status values

No status field by design. Approved eventType values per DEC-012.

## I. Relationships

**Outgoing:** Required Andon Case.  
**Incoming:** Case timeline and audit/reporting consumers.  
**Dependencies:** Andon Case; DEC-012 event vocabulary.

## J. Recommended indexes

- Default unique _id index.
- Unique andonEventNo — Event retrieval; verify query frequency and selectivity before implementation.
- Candidate andonCaseId+eventTime — Case history; verify query frequency and selectivity before implementation.
- Candidate eventType+eventTime — Case timeline; verify query frequency and selectivity before implementation.
- Candidate andonCaseId+eventTime — Case timeline; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  andonEventNo: "<andonEventNo>",
  andonCaseId: "<andonCaseId>",
  andonCaseName: "<andonCaseName>",
  eventType: "<eventType>",
  eventTime: <value>,
  eventDescription: "<eventDescription>",
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

Sequence ordering under same timestamp; allowed transition sequence/reopening; actor and recipient cardinality; event immutability/correction; event/case state synchronization.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: PASS — parent Case is defined in inventory and DEC-005.
- Status vocabulary: PASS — eventType values per DEC-012; no status field.
- Unresolved dependencies: see Section N; no silent resolution.

## P. Audit and Historical Data

Use the standard audit pairs defined in Section K. Preserve source timestamps and correction lineage where approved; retention, immutability, and correction policy remain unresolved unless stated in the decision log.

## Q. Real-World Manufacturing Exceptions

Consider late/out-of-order input, duplicate integration delivery, incomplete operational context, shift boundaries, retries, and correction/reversal scenarios where applicable. Do not invent fields or encode structured payloads in scalar fields to address them.

## R. Missing Field Assessment

The field inventory is bounded by current requirements, decisions, and the inventory purpose. Remaining gaps are: Sequence ordering under same timestamp; allowed transition sequence/reopening; actor and recipient cardinality; event immutability/correction; event/case state synchronization.. Raise missing operational data through requirements/change control; do not add fields silently.

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

Sequence ordering under same timestamp; allowed transition sequence/reopening; actor and recipient cardinality; event immutability/correction; event/case state synchronization.

## Y. Research Sources

See Research basis below; evidence supports operational scenarios only and does not approve unresolved project rules.

## Z. Final Design Review

Status label: APPROVED under prior Project Owner instruction. OPEN items remain unresolved and are not approved business decisions. Flat-document, naming, reference, and evidence checks are summarized in Sections O and T.
## Research basis
- [SAP Digital Manufacturing — Andon Management](https://help.sap.com/docs/sap-digital-manufacturing/execution/andon) — Andon systems support issue lifecycle and response events; the project's exact event vocabulary is controlled by DEC-012.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


