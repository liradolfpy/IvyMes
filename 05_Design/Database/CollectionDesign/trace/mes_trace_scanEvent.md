**FILE TYPE: PROJECT SPECIFIC**

# mes_trace_scanEvent — Collection Specification

**Document ID:** MES-COL-TRACE-scanEvent  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** trace  
**Collection type:** Transaction  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Records a scanned barcode/QR/RFID value, resolution and result.

## B. Business definition

Each scan attempt is a separate event; raw scanned content is scalar, and sensitive-data handling remains open.

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
| `scannedValue` | String | Yes | Raw scanned string. | Scalar only; length/retention policy OPEN. | No | Candidate scannedValue+scannedAt | Evidence | DATA-001; SAP Scan Data | Troubleshooting |
| `scanType` | String | Yes | Scanner/data type. | Vocabulary OPEN. | No | Candidate scanType+scannedAt | Decode context | SAP Scan Data | Process routing |
| `serialNumberId` | String | Conditional | Resolved Serial Number. | If resolved pair with serialNumberName. | Yes: serialNumberId + serialNumberName -> mes_trace_serialNumber | Candidate serialNumberId+scannedAt | Entity resolution | DATA-003 | Trace lookup |
| `serialNumberName` | String | Conditional | Serial display snapshot. | Paired with serialNumberId. | Yes: paired with serialNumberId | No index | Readable entity | DATA-003 | Display |
| `scanResult` | String | Yes | Resolution or validation outcome. | Allowed values OPEN. | No | Candidate scanResult+scannedAt | Failure analysis | SAP Scan Data | Exceptions |
| `scannedAt` | Date | Yes | Scan timestamp. | BSON Date. | No | Candidate scannedAt | Event order | SAP Scan Data | History |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

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

No status; scanResult vocabulary OPEN.

## I. Relationships

**Outgoing:** Optional resolved Lot, Serial, Material, Tool and production context; exact typed references OPEN.  
**Incoming:** Traceability and analytics consumers.  
**Dependencies:** Scanner/device identity, data decoding configuration, context refs, privacy/retention.

## J. Recommended indexes

- Default unique _id index.
- Candidate scannedValue+scannedAt — Troubleshooting; verify query frequency and selectivity before implementation.
- Candidate scanType+scannedAt — Process routing; verify query frequency and selectivity before implementation.
- Candidate serialNumberId+scannedAt — Trace lookup; verify query frequency and selectivity before implementation.
- Candidate scanResult+scannedAt — Exceptions; verify query frequency and selectivity before implementation.
- Candidate scannedAt — History; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  scannedValue: "<scannedValue>",
  scanType: "<scanType>",
  serialNumberId: "<serialNumberId>",
  serialNumberName: "<serialNumberName>",
  scanResult: "<scanResult>",
  scannedAt: <value>,
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

Polymorphic IDs require matching name pairs; device/operator reference owners; accepted/rejected taxonomy; raw barcode sensitivity; deduplication; context IDs and malformed scans.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: Serial target exists; remaining polymorphic endpoints OPEN.
- Status vocabulary: OPEN — scanResult values.
- Unresolved dependencies: see Section N; no silent resolution.

## P. Audit and Historical Data

The standard audit fields are defined in Section K. Preserve source chronology and corrections under approved project controls; retention, immutability and event-reversal policy remain OPEN where not decided.

## Q. Real-World Manufacturing Exceptions

Consider late and duplicate messages, incomplete source context, retries, overnight shifts, corrections and partial completion when applicable. Do not use nested data or scalar-encoded structured payloads to represent repeated facts.

## R. Missing Field Assessment

The current inventory bounds collection scope. Known field/workflow gaps remain: Polymorphic IDs require matching name pairs; device/operator reference owners; accepted/rejected taxonomy; raw barcode sensitivity; deduplication; context IDs and malformed scans.. Raise additional needs through requirements/change control rather than adding fields silently.

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

Polymorphic IDs require matching name pairs; device/operator reference owners; accepted/rejected taxonomy; raw barcode sensitivity; deduplication; context IDs and malformed scans.

## Y. Research Sources

See Research basis below. Sources support manufacturing workflows and scenarios; they do not approve unresolved project rules or transfer vendor schemas.

## Z. Final Design Review

Status label: APPROVED under prior Project Owner instruction. OPEN items remain unresolved. Flatness, reference conventions, inventory coverage and research evidence are summarized in Sections O and T.
## Research basis
- [SAP Digital Manufacturing Scan Data](https://help.sap.com/docs/sap-digital-manufacturing/application-help-for-manage-pods-2-0/scan-data) — Barcode scanning captures data at configured process points and applies defined processing rules.
- [SAP Digital Manufacturing Data Scanning Configurations](https://help.sap.com/docs/sap-digital-manufacturing/execution/understand-data-scanning-configurations?locale=en-US) — Raw scan input is parsed with configured qualifiers and mapped to process inputs.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.

