**FILE TYPE: PROJECT SPECIFIC**

# mes_man_crewMembership — Collection Specification

**Document ID:** MES-COL-MAN-crewMembership  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** man  
**Collection type:** Relationship  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Records an Operator’s membership in a Crew over an effective interval.

## B. Business definition

Crew Membership is an effective-dated relationship between one Crew and one Operator. Each member assignment is its own flat record, enabling history and overlap queries.

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
| `crewId` | String | Yes | Crew identity. | 16-digit ID resolving mes_man_crew. | Yes: crewId + crewName -> mes_man_crew | Candidate crewId + effectiveFrom | Group membership. | DATA-003; inventory | Crew roster |
| `crewName` | String | Yes | Crew display snapshot. | Paired with crewId. | Yes: paired with crewId | No index by default | Roster display. | DATA-003 | Staffing UI |
| `operatorId` | String | Yes | Employee-linked Operator identity. | 16-digit ID resolving mes_man_operator. | Yes: operatorId + operatorName -> mes_man_operator | Candidate operatorId + effectiveFrom | Identify member. | DEC-022; DATA-003 | Operator roster |
| `operatorName` | String | Yes | Operator display snapshot. | Paired with operatorId. | Yes: paired with operatorId | No index by default | Readable roster. | DATA-003 | Shift planning |
| `shiftId` | String | Conditional (proposed) | Shift context if membership is shift-bound. | If present pair with shiftName; permanence vs shift scope OPEN. | Yes: shiftId + shiftName -> mes_man_shift | Candidate shiftId + effectiveFrom | Staff shift roster. | Inventory; SAP labor scheduling | Shift staffing |
| `shiftName` | String | Conditional | Shift display snapshot. | Both fields present or absent. | Yes: paired with shiftId | No index by default | Readable context. | DATA-003 | Roster |
| `effectiveFrom` | Date | Yes (proposed) | Membership start instant/date. | BSON Date; date vs instant semantics OPEN. | No | Candidate crew/operator + effectiveFrom | Historical roster query. | Inventory effective interval | Membership validity |
| `effectiveTo` | Date | Conditional | Membership end instant/date. | BSON Date >= effectiveFrom. | No | Candidate active-membership query | End membership without deletion. | Inventory effective interval | Membership closure |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `crewId` — Yes: crewId + crewName -> mes_man_crew.
- `crewName` — Yes: paired with crewId.
- `operatorId` — Yes: operatorId + operatorName -> mes_man_operator.
- `operatorName` — Yes: paired with operatorId.
- `shiftId` — Yes: shiftId + shiftName -> mes_man_shift.
- `shiftName` — Yes: paired with shiftId.

## G. Validation rules

- Flat top-level scalar fields only; no nested documents or arrays.
- MES IDs are 16-digit strings; external identifiers remain separate.
- Enforce only approved code patterns and uniqueness scopes. Do not invent OPEN scopes or status vocabularies.
- Required ID/display pairs are complete and consistent; optional pairs are both absent or both populated.
- Audit actor pairs are complete; updatedAt >= createdAt.
- Retired reference records remain historically resolvable when established by decision.

## H. Status values

No status field unless approved; effective interval indicates relationship validity.

## I. Relationships

**Outgoing:** Required Crew and Operator pairs; optional Shift pair.  
**Incoming:** Crew roster and labor/attendance queries.  
**Dependencies:** mes_man_crew, mes_man_operator, optional mes_man_shift.

## J. Recommended indexes

- Default unique _id index.
- Candidate crewId + effectiveFrom — Crew roster; verify query frequency and selectivity before implementation.
- Candidate operatorId + effectiveFrom — Operator roster; verify query frequency and selectivity before implementation.
- Candidate shiftId + effectiveFrom — Shift staffing; verify query frequency and selectivity before implementation.
- Candidate crew/operator + effectiveFrom — Membership validity; verify query frequency and selectivity before implementation.
- Candidate active-membership query — Membership closure; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  crewId: "<crewId>",
  crewName: "<crewName>",
  operatorId: "<operatorId>",
  operatorName: "<operatorName>",
  shiftId: "<shiftId>",
  shiftName: "<shiftName>",
  effectiveFrom: <value>,
  effectiveTo: <value>,
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

Membership record grain, date/time semantics, overlapping memberships, shift-bound vs enduring membership, duplicate active member constraints, end-date inclusivity.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: PASS — required Crew/Operator and optional Shift pairs defined.
- Status vocabulary: N/A — no lifecycle status proposed.
- Unresolved dependencies: see Section N; no silent resolution.


## P. Audit and Historical Data

Standard audit fields are flat and paired. Effective dates and credential expiration must be BSON Dates where specified. Retention, record correction, and evidence storage rules remain OPEN.

## Q. Real-World Manufacturing Exceptions

Preserve expired, ended, revoked, or reassessed relationships for history where required; do not treat an expired qualification as active by inference. Exact transitions remain OPEN.

## R. Missing Field Assessment

Collection-specific requirement detail is incomplete. Review with workforce, production supervision, HR/integration, and database roles. Known gaps: Membership record grain, date/time semantics, overlapping memberships, shift-bound vs enduring membership, duplicate active member constraints, end-date inclusivity.

## S. Missing Collection Assessment

No collection is added. Crew membership and operator skill are separate flat records. Certification type ownership is unresolved; do not invent a catalog or embed certificate payloads.

## T. Cross-Collection Consistency Check

- Inventory identity/module/type: checked against v1.6.
- Flat relationships and singular naming: PASS for defined fields.
- Operator references remain distinct from Employee identity; DEC-022 respected.
- Every identified MES entity reference has an ID/display pair.
- Qualification/certification taxonomies, status, overlap, and external ownership remain OPEN.

## U. Field Completeness Metrics

Field and requiredness counts are in Section E; ID/name pairs in Section F; candidate indexes in Section J. No full scenario coverage metric is claimed without detailed requirements. Known gaps: Membership record grain, date/time semantics, overlapping memberships, shift-bound vs enduring membership, duplicate active member constraints, end-date inclusivity.

## V. One Representative MongoDB Document

The single illustrative flat document is in Section L; no additional examples or sample-data set are generated.

## W. Assumptions

- Inventory and approved DEC-022 ownership govern scope.
- Membership, skill, and certification assignments are separate records, never embedded arrays.
- ID/name duplication is intentional strategic denormalization.
- APPROVED follows owner instruction; unresolved fields/taxonomies remain OPEN.

## X. Open Issues

Membership record grain, date/time semantics, overlapping memberships, shift-bound vs enduring membership, duplicate active member constraints, end-date inclusivity.

## Y. Research Sources

See the collection-specific public documentation under Research basis below. It informs staffing, qualifications, and validity scenarios only.

## Z. Final Design Review

- **Status:** APPROVED per Project Owner direction; not production readiness.
- **Covered:** identity boundaries, relationship-record representation, required references, research, and open points.
- **Remaining:** Section X and unresolved qualification/effectivity rules.
## Research basis

- [SAP Digital Manufacturing Resource Orchestration Overview](https://help.sap.com/docs/sap-digital-manufacturing/resource-orchestration/overview-and-key-capabilities?version=latest) — Schedules operators across shifts and assignments; supports effective workforce allocation scenarios.
- [SAP Digital Manufacturing Role Templates](https://help.sap.com/docs/sap-digital-manufacturing/operations-guide/role-templates) — Documents shift plans and operator assignment; functional comparison only.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


