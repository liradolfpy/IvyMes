**FILE TYPE: PROJECT SPECIFIC**

# mes_qa_calibrationVerification — Collection Specification

**Document ID:** MES-COL-QA-calibrationVerification  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** qa  
**Collection type:** Transaction  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Records point-of-use verification that a measurement resource/tool was within calibration validity.

## B. Business definition

Calibration Verification is a check at use time, distinct from the Tool Calibration Record that documents calibration work/results.

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
| `toolId` | String | Conditional | Verified Tool identity. | Pair with toolName when tool applies. | Yes: toolId + toolName -> mes_tool_tool | Candidate toolId+verifiedAt | Measurement tool | DEC-022 | Inspection validity |
| `toolName` | String | Conditional | Tool display snapshot. | Paired with toolId; both absent or present. | Yes: paired with toolId | No index by default | Readable identity | DATA-003 | Display |
| `assetId` | String | Conditional | Verified Asset/equipment identity. | If asset-based pair with assetName; exactly-one subject model OPEN. | Yes: assetId + assetName -> mes_maint_asset | Candidate assetId+verifiedAt | Measuring equipment | DEC-022 | Validity check |
| `assetName` | String | Conditional | Asset display snapshot. | Paired with assetId; both absent or present. | Yes: paired with assetId | No index by default | Readable identity | DATA-003 | Display |
| `verifiedAt` | Date | Yes | Point-of-use check time. | BSON Date. | No | Candidate verifiedAt | Validity at use | Calibration verification workflow | Quality result |
| `verificationResult` | String | Yes | Valid/invalid/exception outcome. | Allowed values OPEN. | No | Candidate verificationResult+verifiedAt | Use decision | Inventory | Gate |
| `calibrationRecordId` | String | Conditional | Source Tool Calibration Record when linked. | Pair with calibrationRecordName. | Yes: calibrationRecordId + calibrationRecordName -> mes_tool_toolCalibrationRecord | Candidate calibrationRecordId | Evidence lineage | Inventory; DATA-003 | Audit |
| `calibrationRecordName` | String | Conditional | Tool Calibration Record display snapshot. | Paired with calibrationRecordId; both absent or present. | Yes: paired with calibrationRecordId | No index by default | Readable evidence | DATA-003 | Display |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `toolId` — Yes: toolId + toolName -> mes_tool_tool.
- `assetId` — Yes: assetId + assetName -> mes_maint_asset.
- `calibrationRecordId` — Yes: calibrationRecordId + calibrationRecordName -> mes_tool_toolCalibrationRecord.

## G. Validation rules

- Flat top-level scalar fields only; no nested documents or arrays.
- MES IDs are 16-digit strings; external identifiers remain separate.
- Enforce only approved code patterns and uniqueness scopes. Do not invent OPEN scopes or status vocabularies.
- Required ID/display pairs are complete and consistent; optional pairs are both absent or both populated.
- Audit actor pairs are complete; updatedAt >= createdAt.
- Retired reference records remain historically resolvable when established by decision.

## H. Status values

verificationResult is outcome; no separate lifecycle status.

## I. Relationships

**Outgoing:** Tool or Asset subject; optional Tool Calibration Record.  
**Incoming:** Inspection result and audit records.  
**Dependencies:** Tool/Asset identity; calibration record; validity/effective-date rules.

## J. Recommended indexes

- Default unique _id index.
- Candidate toolId+verifiedAt — Inspection validity; verify query frequency and selectivity before implementation.
- Candidate assetId+verifiedAt — Validity check; verify query frequency and selectivity before implementation.
- Candidate verifiedAt — Quality result; verify query frequency and selectivity before implementation.
- Candidate verificationResult+verifiedAt — Gate; verify query frequency and selectivity before implementation.
- Candidate calibrationRecordId — Audit; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  toolId: "<toolId>",
  toolName: "<toolName>",
  assetId: "<assetId>",
  assetName: "<assetName>",
  verifiedAt: <value>,
  verificationResult: "<verificationResult>",
  calibrationRecordId: "<calibrationRecordId>",
  calibrationRecordName: "<calibrationRecordName>",
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

Exactly-one Tool/Asset subject; calibration record may not cover Asset; validity date evaluation; external calibration certificates and evidence; inspector identity; accepted outcomes.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: Tool, Asset and Tool Calibration targets exist; Asset calibration record coverage is unresolved.
- Status vocabulary: N/A.
- Unresolved dependencies: see Section N; no silent resolution.

## P. Audit and Historical Data

The standard audit fields are defined in Section K. Preserve source chronology and corrections under approved project controls; retention, immutability and event-reversal policy remain OPEN where not decided.

## Q. Real-World Manufacturing Exceptions

Consider late and duplicate messages, incomplete source context, retries, overnight shifts, corrections and partial completion when applicable. Do not use nested data or scalar-encoded structured payloads to represent repeated facts.

## R. Missing Field Assessment

The current inventory bounds collection scope. Known field/workflow gaps remain: Exactly-one Tool/Asset subject; calibration record may not cover Asset; validity date evaluation; external calibration certificates and evidence; inspector identity; accepted outcomes.. Raise additional needs through requirements/change control rather than adding fields silently.

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

Exactly-one Tool/Asset subject; calibration record may not cover Asset; validity date evaluation; external calibration certificates and evidence; inspector identity; accepted outcomes.

## Y. Research Sources

See Research basis below. Sources support manufacturing workflows and scenarios; they do not approve unresolved project rules or transfer vendor schemas.

## Z. Final Design Review

Status label: APPROVED under prior Project Owner instruction. OPEN items remain unresolved. Flatness, reference conventions, inventory coverage and research evidence are summarized in Sections O and T.
## Research basis
- [SAP S/4HANA Measuring Points and Counters](https://help.sap.com/docs/SAP_S4HANA_ON-PREMISE/21aead0c98bd4755abdacd91c99e3393/3b6db65334e6b54ce10000000a174cb4.html?locale=en-US&state=PRODUCTION&version=2025.001) — Technical-object measurement readings are time-stamped and used for condition/counter-based maintenance.
- [SAP Digital Manufacturing Tool service](https://help.sap.com/docs/sap-digital-manufacturing/service-catalog/e274644077d94140bf1f61f78dee6985.html) — Tool management supports calibration operations and tool usage tracking.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.




