**FILE TYPE: PROJECT SPECIFIC**

# mes_prod_downtimeEvent — Collection Specification

**Document ID:** MES-COL-PROD-downtimeEvent  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** prod  
**Collection type:** Transaction  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Records a downtime interval/event affecting production execution and its classification/reason.

## B. Business definition

Downtime Event is an operational downtime record, distinct from machine-state telemetry and Andon event history. The record can remain open until an end time is known.

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
| `downtimeEventNo` | String | No (DEC-013) | Optional business number. | DT-YYYY-###### when present; unique within collection when present (DEC-013). | No | Unique partial index when present | Integration/reporting ID. | DEC-013 | Reconciliation |
| `workCenterId` | String | Conditional (proposed) | Affected organizational work center if identified. | If present pair with workCenterName; no resource/machine inference. | Yes: workCenterId + workCenterName -> mes_plan_workCenter | Candidate workCenter+startTime | Operational scope. | DEC-022; SAP downtime | Downtime review |
| `workCenterName` | String | Conditional | Work-center display snapshot. | Both fields present or absent. | Yes: paired with workCenterId | No index by default | Readable context. | DATA-003 | Reporting |
| `downtimeClassificationId` | String | Yes (proposed) | Controlled downtime classification. | 16-digit ID paired with downtimeClassificationName. | Yes: downtimeClassificationId + downtimeClassificationName -> mes_perf_downtimeClassification | Candidate classification+startTime | Categorize loss. | Inventory; DATA-003 | OEE analysis |
| `downtimeClassificationName` | String | Yes (proposed) | Classification display snapshot. | Paired with downtimeClassificationId. | Yes: paired with downtimeClassificationId | No index by default | Readable reason. | DATA-003 | Downtime review |
| `startTime` | Date | Yes | Downtime start timestamp. | BSON Date; <= endTime if closed. | No | Candidate workCenter+startTime | Duration and loss calculations. | SAP downtime event | Open downtime |
| `endTime` | Date | Conditional | Downtime end timestamp. | BSON Date >= startTime; absent while open. | No | Candidate endTime | Close interval. | SAP downtime event | Recovery |
| `durationSeconds` | Double | Conditional | Duration in seconds when reported/calculated. | BSON Double per DEC-010; consistency with timestamps and precision/rounding rules OPEN. | No | No index by default | Loss measurement. | DEC-010; SAP Downtime API | Performance/OEE |
| `status` | String | Yes | Downtime lifecycle state. | Open, Closed, Cancelled (DEC-011). | No | Candidate status+startTime | Open/closed interval management. | DEC-011 | Downtime lifecycle |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `workCenterId` — Yes: workCenterId + workCenterName -> mes_plan_workCenter.
- `workCenterName` — Yes: paired with workCenterId.
- `downtimeClassificationId` — Yes: downtimeClassificationId + downtimeClassificationName -> mes_perf_downtimeClassification.
- `downtimeClassificationName` — Yes: paired with downtimeClassificationId.

## G. Validation rules

- Flat top-level scalar fields only; no nested documents or arrays.
- MES IDs are 16-digit strings; external identifiers remain separate.
- Enforce only approved code patterns and uniqueness scopes. Do not invent OPEN scopes or status vocabularies.
- Required ID/display pairs are complete and consistent; optional pairs are both absent or both populated.
- Audit actor pairs are complete; updatedAt >= createdAt.
- Retired reference records remain historically resolvable when established by decision.

## H. Status values

Approved values per DEC-011. Classification/status do not imply telemetry state.

## I. Relationships

**Outgoing:** Optional Work Center and required Downtime Classification pairs.  
**Incoming:** Performance machine state/OEE/loss and maintenance/analytics consumers.  
**Dependencies:** Downtime Classification; Work Center is organizational scope only. Machine/resource target and overlap with machineStateEvent unresolved.

## J. Recommended indexes

- Default unique _id index.
- Unique partial index when present — Reconciliation; verify query frequency and selectivity before implementation.
- Candidate workCenter+startTime — Downtime review; verify query frequency and selectivity before implementation.
- Candidate classification+startTime — OEE analysis; verify query frequency and selectivity before implementation.
- Candidate workCenter+startTime — Open downtime; verify query frequency and selectivity before implementation.
- Candidate endTime — Recovery; verify query frequency and selectivity before implementation.
- Candidate status+startTime — Downtime lifecycle; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  downtimeEventNo: "<downtimeEventNo>",
  workCenterId: "<workCenterId>",
  workCenterName: "<workCenterName>",
  downtimeClassificationId: "<downtimeClassificationId>",
  downtimeClassificationName: "<downtimeClassificationName>",
  startTime: <value>,
  endTime: <value>,
  durationSeconds: <value>,
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

Affected resource/asset identity absent; work-center vs machine boundary; overlap/reconciliation with machineStateEvent; multiple reasons/splits; duration derivation; open event edits; status transition and correction history.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: PASS — Work Center and classification pairs defined; asset/resource unresolved.
- Status vocabulary: PASS — exact values from DEC-011.
- Unresolved dependencies: see Section N; no silent resolution.


## P. Audit and Historical Data

Standard audit fields remain flat and paired. Operation execution, dispatch changes, downtime closure, reservation conflicts, and corrections require a durable chronology; detailed event-history ownership remains OPEN.

## Q. Real-World Manufacturing Exceptions

Handle reassignment, overlapping reservations, partial execution, pause/resume, unclosed downtime, and conflicting resource identity without silently altering parent references. Preserve correction history under approved controls.

## R. Missing Field Assessment

Collection-specific operational requirements are incomplete. Review with production supervision, workforce, tool-room, maintenance, performance, integration, and database roles. Known gaps: Affected resource/asset identity absent; work-center vs machine boundary; overlap/reconciliation with machineStateEvent; multiple reasons/splits; duration derivation; open event edits; status transition and correction history.

## S. Missing Collection Assessment

No collection is added. Do not infer a machine/resource entity from Work Center. If a target, assignment history, or repeated event structure needs another owner, raise it for inventory review rather than embedding arrays.

## T. Cross-Collection Consistency Check

- Inventory scope checked against v1.6.
- Work Order and operation step references align with DEC-003/DEC-004.
- Approved PROD business-number/status rules applied where applicable (DEC-011/DEC-013).
- DEC-010 Double is used only for the specified downtime duration field here.
- Entity references use ID/name pairs; resource/asset gaps remain OPEN.
- Flat-document rule respected.

## U. Field Completeness Metrics

Field, reference, and index counts are in Sections E, F, and J. Detailed workflow requirements are not complete; no full scenario coverage percentage is claimed. Known gaps: Affected resource/asset identity absent; work-center vs machine boundary; overlap/reconciliation with machineStateEvent; multiple reasons/splits; duration derivation; open event edits; status transition and correction history.

## V. One Representative MongoDB Document

The single illustrative flat structure is in Section L. No additional samples are generated.

## W. Assumptions

- Current inventory and approved project decisions govern scope.
- Repeated assignments and event history are separate records.
- ID/name duplication is intentional strategic denormalization.
- APPROVED follows prior user direction; open questions remain unresolved.

## X. Open Issues

Affected resource/asset identity absent; work-center vs machine boundary; overlap/reconciliation with machineStateEvent; multiple reasons/splits; duration derivation; open event edits; status transition and correction history.

## Y. Research Sources

Collection-specific public references and the manufacturing scenarios they support are documented in Research basis below; vendor behavior does not set project rules.

## Z. Final Design Review

- **Status:** APPROVED per prior Project Owner instruction; not production readiness.
- **Covered:** bounded purpose, operational field baseline, relationships, approved decision alignment, research, and gaps.
- **Remaining:** Section X; assignment, event overlap, and target ownership rules must be resolved before implementation where marked blocking.
## Research basis

- [SAP Digital Manufacturing Downtime and Speed Loss API](https://help.sap.com/docs/sap-digital-manufacturing/apis/speed-loss) — Downtime has time duration and reason code and can be tied to resources/work center; project target identity remains unresolved.
- [SAP Resource Downtimes Created Event](https://help.sap.com/docs/sap-digital-manufacturing/production-process-designer/resource-downtimes-created?locale=en-US) — Downtime event includes start/end, reason code, resource and comments; used as workflow benchmark only.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


