**FILE TYPE: PROJECT SPECIFIC**

# mes_qa_inspectionResult — Collection Specification

**Document ID:** MES-COL-QA-inspectionResult  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** qa  
**Collection type:** Transaction  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Stores one inspection characteristic observation and disposition per record.

## B. Business definition

One result record represents one measured/qualitative characteristic observation for one request/sample; repeated results are separate flat records.

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
| `inspectionRequestId` | String | Yes | Parent inspection request. | Pair with inspectionRequestName. | Yes: inspectionRequestId + inspectionRequestName -> mes_qa_inspectionRequest | Candidate inspectionRequestId+characteristicId | Result grouping | Inventory; DATA-003 | Request completion |
| `inspectionRequestName` | String | Yes | Request display snapshot. | Paired with inspectionRequestId. | Yes: paired with inspectionRequestId | No index | Readable parent | DATA-003 | Display |
| `characteristicCode` | String | Yes | Inspected characteristic identity/code. | Characteristic owner/master collection unresolved; code uniqueness OPEN. | No | Candidate characteristicCode+recordedAt | Repeated item key | SAP Quality Inspection API | Characteristic result |
| `characteristicName` | String | Yes | Characteristic display name. | Paired display only; no owner ID available until resolved. | No | No index | Readable result | DATA-003 guidance; inventory gap | Display |
| `numericValue` | Double | Conditional | Measured numeric value. | Double; numeric rule and measurement precision/unit decision required. | No | No index | Quantitative measurement | DEC-010 guidance; SAP Quality Results | SPC |
| `textValue` | String | Conditional | Qualitative/code result when nonnumeric. | Value domain OPEN; numeric/text exclusivity rule required. | No | No index | Qualitative inspection | SAP Quality Results | Disposition |
| `recordedAt` | Date | Yes | Result timestamp. | BSON Date. | No | Candidate inspectionRequestId+recordedAt | History | SAP Quality Inspection API | Audit |
| `valuation` | String | Yes | Acceptance/rejection/valuation result. | Vocabulary and authority OPEN. | No | Candidate valuation+recordedAt | Quality decision | SAP Results Recording | Disposition |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `inspectionRequestId` — Yes: inspectionRequestId + inspectionRequestName -> mes_qa_inspectionRequest.
- `inspectionRequestName` — Yes: paired with inspectionRequestId.

## G. Validation rules

- Flat top-level scalar fields only; no nested documents or arrays.
- MES IDs are 16-digit strings; external identifiers remain separate.
- Enforce only approved code patterns and uniqueness scopes. Do not invent OPEN scopes or status vocabularies.
- Required ID/display pairs are complete and consistent; optional pairs are both absent or both populated.
- Audit actor pairs are complete; updatedAt >= createdAt.
- Retired reference records remain historically resolvable when established by decision.

## H. Status values

No independent status specified; valuation is result outcome, not record lifecycle.

## I. Relationships

**Outgoing:** Inspection Request; characteristic identity currently lacks owning collection; trace subject inherited via request.  
**Incoming:** Nonconformance, SPC, certificate and hold consumers.  
**Dependencies:** Inspection Request; characteristic definition owner; sampling and measurement/UOM rules.

## J. Recommended indexes

- Default unique _id index.
- Candidate inspectionRequestId+characteristicId — Request completion; verify query frequency and selectivity before implementation.
- Candidate characteristicCode+recordedAt — Characteristic result; verify query frequency and selectivity before implementation.
- Candidate inspectionRequestId+recordedAt — Audit; verify query frequency and selectivity before implementation.
- Candidate valuation+recordedAt — Disposition; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  inspectionRequestId: "<inspectionRequestId>",
  inspectionRequestName: "<inspectionRequestName>",
  characteristicCode: "<characteristicCode>",
  characteristicName: "<characteristicName>",
  numericValue: <value>,
  textValue: "<textValue>",
  recordedAt: <value>,
  valuation: "<valuation>",
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

Missing characteristic definition collection/ID pair; repeated sample model; numeric types; unit; limits and result validation; status vs valuation; edits after confirmation and synchronization.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: Inspection Request exists; inventory lacks a characteristic owner collection, flagged as a gap.
- Status vocabulary: N/A.
- Unresolved dependencies: see Section N; no silent resolution.

## P. Audit and Historical Data

The standard audit fields are defined in Section K. Preserve source chronology and corrections under approved project controls; retention, immutability and event-reversal policy remain OPEN where not decided.

## Q. Real-World Manufacturing Exceptions

Consider late and duplicate messages, incomplete source context, retries, overnight shifts, corrections and partial completion when applicable. Do not use nested data or scalar-encoded structured payloads to represent repeated facts.

## R. Missing Field Assessment

The current inventory bounds collection scope. Known field/workflow gaps remain: Missing characteristic definition collection/ID pair; repeated sample model; numeric types; unit; limits and result validation; status vs valuation; edits after confirmation and synchronization.. Raise additional needs through requirements/change control rather than adding fields silently.

## S. Missing Collection Assessment

No collection is added by this specification. Repeated concepts remain separate flat records. Where an owning entity, item identity, or relationship record is absent, retain the gap for inventory review.

## T. Cross-Collection Consistency Check

- Inventory name/module and purpose checked against the current collection list.
- Flat scalar representation, singular key naming, ID/display pairs, and standard audit fields checked.
- Reference targets and lifecycle/type gaps are called out in Sections N, O, and X; unresolved items remain OPEN.

## U. Field Completeness Metrics

- Field names listed in the dictionary: 15, including system ID and audit fields.
- Entity reference pairs are enumerated in Section F; audit pairs are listed in Section K.
- Scenario and source evidence is collection-specific where documented; no unsupported requirement coverage percentage is claimed.

## V. One Representative MongoDB Document

The single illustrative flat structure is in Section L. No sample document set is generated in this phase.

## W. Assumptions

- Current project context, functional inventory, requirements and approved decisions bound this design.
- ID/display duplication is intentional strategic denormalization.
- APPROVED is the requested specification status; OPEN items remain unresolved and this label does not establish production readiness.

## X. Open Issues

Missing characteristic definition collection/ID pair; repeated sample model; numeric types; unit; limits and result validation; status vs valuation; edits after confirmation and synchronization.

## Y. Research Sources

See Research basis below. Sources support manufacturing workflows and scenarios; they do not approve unresolved project rules or transfer vendor schemas.

## Z. Final Design Review

Status label: APPROVED under prior Project Owner instruction. OPEN items remain unresolved. Flatness, reference conventions, inventory coverage and research evidence are summarized in Sections O and T.
## Research basis
- [SAP Digital Manufacturing Quality Inspection API](https://help.sap.com/docs/sap-digital-manufacturing/apis/quality-inspection) — Single-value result recording is keyed by inspection lot, operation and characteristic.
- [SAP Digital Manufacturing Recording Quality Inspection Results](https://help.sap.com/docs/sap-digital-manufacturing/execution/recording-quality-inspection-results-c531af7448a642f3a294161fe3adfab7) — Characteristic results may be recorded repeatedly until confirmation/synchronization.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.

