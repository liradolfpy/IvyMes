**FILE TYPE: PROJECT SPECIFIC**

# mes_trace_labelPrintEvent — Collection Specification

**Document ID:** MES-COL-TRACE-labelPrintEvent  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** trace  
**Collection type:** Transaction  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Records a label generation or print request, outcome and traceable subject.

## B. Business definition

One record captures one print attempt using a Label Definition; reprints are new events, not overwrites.

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
| `labelDefinitionId` | String | Yes | Label template definition. | Pair with labelDefinitionName. | Yes: labelDefinitionId + labelDefinitionName -> mes_trace_labelDefinition | Candidate labelDefinitionId+printedAt | Template trace | Inventory; DATA-003 | Reprint audit |
| `labelDefinitionName` | String | Yes | Label definition snapshot. | Paired with labelDefinitionId. | Yes: paired with labelDefinitionId | No index | Readable template | DATA-003 | Display |
| `serialNumberId` | String | Conditional | Printed subject Serial Number. | If applicable, pair with serialNumberName. | Yes: serialNumberId + serialNumberName -> mes_trace_serialNumber | Candidate serialNumberId+printedAt | Trace label target | Inventory | Reprint history |
| `serialNumberName` | String | Conditional | Serial snapshot. | Paired with serialNumberId. | Yes: paired with serialNumberId | No index | Readable subject | DATA-003 | Display |
| `printResult` | String | Yes | Print outcome. | Status vocabulary and retry policy OPEN. | No | Candidate printResult+printedAt | Failure/reprint investigation | SAP Printing Guide | Operations |
| `printedAt` | Date | Yes | Attempt time. | BSON Date. | No | Candidate printedAt | Chronology | SAP Printing Guide | Audit |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `labelDefinitionId` — Yes: labelDefinitionId + labelDefinitionName -> mes_trace_labelDefinition.
- `labelDefinitionName` — Yes: paired with labelDefinitionId.
- `serialNumberId` — Yes: serialNumberId + serialNumberName -> mes_trace_serialNumber.
- `serialNumberName` — Yes: paired with serialNumberId.

## G. Validation rules

- Flat top-level scalar fields only; no nested documents or arrays.
- MES IDs are 16-digit strings; external identifiers remain separate.
- Enforce only approved code patterns and uniqueness scopes. Do not invent OPEN scopes or status vocabularies.
- Required ID/display pairs are complete and consistent; optional pairs are both absent or both populated.
- Audit actor pairs are complete; updatedAt >= createdAt.
- Retired reference records remain historically resolvable when established by decision.

## H. Status values

printResult is outcome, not lifecycle status; values OPEN.

## I. Relationships

**Outgoing:** Label Definition and one applicable traceable subject; operator/printer owner unresolved.  
**Incoming:** Audit/reporting consumers.  
**Dependencies:** Label Definition; trace subject; printer/content integration boundary.

## J. Recommended indexes

- Default unique _id index.
- Candidate labelDefinitionId+printedAt — Reprint audit; verify query frequency and selectivity before implementation.
- Candidate serialNumberId+printedAt — Reprint history; verify query frequency and selectivity before implementation.
- Candidate printResult+printedAt — Operations; verify query frequency and selectivity before implementation.
- Candidate printedAt — Audit; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  labelDefinitionId: "<labelDefinitionId>",
  labelDefinitionName: "<labelDefinitionName>",
  serialNumberId: "<serialNumberId>",
  serialNumberName: "<serialNumberName>",
  printResult: "<printResult>",
  printedAt: <value>,
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

Subject types and ID/name pairs; printer identity; output reference/content locator; count/copies; failure/retry; user identity source; sensitive label content.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: Label Definition and Serial Number exist; subject polymorphism and print service identity OPEN.
- Status vocabulary: OPEN — printResult taxonomy.
- Unresolved dependencies: see Section N; no silent resolution.

## P. Audit and Historical Data

The standard audit fields are defined in Section K. Preserve source chronology and corrections under approved project controls; retention, immutability and event-reversal policy remain OPEN where not decided.

## Q. Real-World Manufacturing Exceptions

Consider late and duplicate messages, incomplete source context, retries, overnight shifts, corrections and partial completion when applicable. Do not use nested data or scalar-encoded structured payloads to represent repeated facts.

## R. Missing Field Assessment

The current inventory bounds collection scope. Known field/workflow gaps remain: Subject types and ID/name pairs; printer identity; output reference/content locator; count/copies; failure/retry; user identity source; sensitive label content.. Raise additional needs through requirements/change control rather than adding fields silently.

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

Subject types and ID/name pairs; printer identity; output reference/content locator; count/copies; failure/retry; user identity source; sensitive label content.

## Y. Research Sources

See Research basis below. Sources support manufacturing workflows and scenarios; they do not approve unresolved project rules or transfer vendor schemas.

## Z. Final Design Review

Status label: APPROVED under prior Project Owner instruction. OPEN items remain unresolved. Flatness, reference conventions, inventory coverage and research evidence are summarized in Sections O and T.
## Research basis
- [SAP Digital Manufacturing Automatic Printing](https://help.sap.com/docs/sap-digital-manufacturing/printing-guide/automatic-printing) — Printing can be triggered at defined production actions and attach labels to production identifiers.
- [SAP Digital Manufacturing Goods Receipt Labels](https://help.sap.com/docs/sap-digital-manufacturing/execution/manual-printing) — Production labels use a managed print template and can be manually printed.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.

