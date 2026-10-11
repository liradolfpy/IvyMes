**FILE TYPE: PROJECT SPECIFIC**

# mes_man_crew — Collection Specification

**Document ID:** MES-COL-MAN-crew  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** man  
**Collection type:** Master  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Defines a crew/team identity and lifecycle for coordinated workforce assignments.

## B. Business definition

Crew is a reusable workforce grouping; actual member assignments are separate effective-dated mes_man_crewMembership records, not embedded operator arrays.

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
| `crewCode` | String | Yes (proposed) | Business-facing crew identifier. | Pattern/uniqueness OPEN. | No | Candidate crewCode; uniqueness OPEN | Select crew. | Inventory; SAP workforce scheduling | Staffing |
| `crewName` | String | Yes | Readable crew name. | Non-empty. | No | Candidate text search | Supervisor/worker display. | DATA-003 | Crew assignment |
| `status` | String | Yes (proposed) | Crew identity lifecycle/availability. | Vocabulary/transitions OPEN. | No | Candidate status filter | Retain historical membership. | Lifecycle rules OPEN | Crew selection |

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

Crew lifecycle values OPEN; not current staffed/available state.

## I. Relationships

**Outgoing:** No direct member array; membership is managed through mes_man_crewMembership.  
**Incoming:** Crew Membership, labor assignment and shift handover may reference crew identity.  
**Dependencies:** Operator, Shift; optional plant scope not established.

## J. Recommended indexes

- Default unique _id index.
- Candidate crewCode; uniqueness OPEN — Staffing; verify query frequency and selectivity before implementation.
- Candidate text search — Crew assignment; verify query frequency and selectivity before implementation.
- Candidate status filter — Crew selection; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  crewCode: "<crewCode>",
  crewName: "<crewName>",
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

Crew code uniqueness; crew is permanent team vs shift-specific grouping; plant/shift scope; status semantics; membership overlap and minimum/maximum size.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: PASS — membership is separate; no embedded array.
- Status vocabulary: OPEN — values not approved.
- Unresolved dependencies: see Section N; no silent resolution.


## P. Audit and Historical Data

Standard audit fields are flat and paired. Effective dates and credential expiration must be BSON Dates where specified. Retention, record correction, and evidence storage rules remain OPEN.

## Q. Real-World Manufacturing Exceptions

Preserve expired, ended, revoked, or reassessed relationships for history where required; do not treat an expired qualification as active by inference. Exact transitions remain OPEN.

## R. Missing Field Assessment

Collection-specific requirement detail is incomplete. Review with workforce, production supervision, HR/integration, and database roles. Known gaps: Crew code uniqueness; crew is permanent team vs shift-specific grouping; plant/shift scope; status semantics; membership overlap and minimum/maximum size.

## S. Missing Collection Assessment

No collection is added. Crew membership and operator skill are separate flat records. Certification type ownership is unresolved; do not invent a catalog or embed certificate payloads.

## T. Cross-Collection Consistency Check

- Inventory identity/module/type: checked against v1.6.
- Flat relationships and singular naming: PASS for defined fields.
- Operator references remain distinct from Employee identity; DEC-022 respected.
- Every identified MES entity reference has an ID/display pair.
- Qualification/certification taxonomies, status, overlap, and external ownership remain OPEN.

## U. Field Completeness Metrics

Field and requiredness counts are in Section E; ID/name pairs in Section F; candidate indexes in Section J. No full scenario coverage metric is claimed without detailed requirements. Known gaps: Crew code uniqueness; crew is permanent team vs shift-specific grouping; plant/shift scope; status semantics; membership overlap and minimum/maximum size.

## V. One Representative MongoDB Document

The single illustrative flat document is in Section L; no additional examples or sample-data set are generated.

## W. Assumptions

- Inventory and approved DEC-022 ownership govern scope.
- Membership, skill, and certification assignments are separate records, never embedded arrays.
- ID/name duplication is intentional strategic denormalization.
- APPROVED follows owner instruction; unresolved fields/taxonomies remain OPEN.

## X. Open Issues

Crew code uniqueness; crew is permanent team vs shift-specific grouping; plant/shift scope; status semantics; membership overlap and minimum/maximum size.

## Y. Research Sources

See the collection-specific public documentation under Research basis below. It informs staffing, qualifications, and validity scenarios only.

## Z. Final Design Review

- **Status:** APPROVED per Project Owner direction; not production readiness.
- **Covered:** identity boundaries, relationship-record representation, required references, research, and open points.
- **Remaining:** Section X and unresolved qualification/effectivity rules.
## Research basis

- [SAP Digital Manufacturing Resource Orchestration Overview](https://help.sap.com/docs/sap-digital-manufacturing/resource-orchestration/overview-and-key-capabilities?version=latest) — Workforce schedule functions manage operator shifts and assignments and handle skill/availability constraints.
- [SAP Digital Manufacturing Role Templates](https://help.sap.com/docs/sap-digital-manufacturing/operations-guide/role-templates) — Labor allocation associates operators, shifts, work centers, skills, and workload; used as functional context only.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


