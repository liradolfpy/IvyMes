**FILE TYPE: PROJECT SPECIFIC**

# mes_man_trainingRequirement — Collection Specification

**Document ID:** MES-COL-MAN-trainingRequirement  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** man  
**Collection type:** Configuration  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Defines training requirements by skill, workforce role, or process scope for qualification and authorization planning.

## B. Business definition

A Training Requirement is a reusable rule describing required learning/qualification. Completion evidence belongs in Training Record; no role master is currently identified.

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
| `trainingRequirementCode` | String | Yes (proposed) | Business identifier for requirement rule. | Pattern/uniqueness OPEN. | No | Candidate code; uniqueness OPEN | Govern training rules. | Inventory; SAP training history | Requirement assignment |
| `trainingRequirementName` | String | Yes | Readable requirement name. | Non-empty. | No | Candidate text search | Planner/employee display. | DATA-003 | Training plan |
| `skillId` | String | Conditional (proposed) | Required skill definition where requirement targets a skill. | If present paired with skillName. | Yes: skillId + skillName -> mes_man_skill | Candidate skillId | Connect learning to qualification. | Inventory; SAP role templates | Qualification planning |
| `skillName` | String | Conditional | Skill display snapshot. | Both fields present or absent. | Yes: paired with skillId | No index by default | Readable rule. | DATA-003 | Training display |
| `requirementScopeType` | String | Yes (proposed) | Identifies whether rule scope is role, process, skill, or other approved scope. | Vocabulary and target owners OPEN. | No | Candidate scope filter | Apply requirement appropriately. | Inventory lists multiple scope types | Requirement evaluation |
| `status` | String | Yes (proposed) | Training-rule lifecycle state. | Values/transitions OPEN. | No | Candidate status filter | Control applicability. | Lifecycle rules OPEN | Training assignment |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `skillId` — Yes: skillId + skillName -> mes_man_skill.
- `skillName` — Yes: paired with skillId.

## G. Validation rules

- Flat top-level scalar fields only; no nested documents or arrays.
- MES IDs are 16-digit strings; external identifiers remain separate.
- Enforce only approved code patterns and uniqueness scopes. Do not invent OPEN scopes or status vocabularies.
- Required ID/display pairs are complete and consistent; optional pairs are both absent or both populated.
- Audit actor pairs are complete; updatedAt >= createdAt.
- Retired reference records remain historically resolvable when established by decision.

## H. Status values

Training rule lifecycle, not completion status. Values OPEN.

## I. Relationships

**Outgoing:** Optional Skill pair; role/process target lacks an identified collection owner.  
**Incoming:** Training Record, certification, skill and workforce eligibility workflows.  
**Dependencies:** Skill master; Operator/Employee references may be needed by downstream records. Training course/catalog owner unresolved.

## J. Recommended indexes

- Default unique _id index.
- Candidate code; uniqueness OPEN — Requirement assignment; verify query frequency and selectivity before implementation.
- Candidate text search — Training plan; verify query frequency and selectivity before implementation.
- Candidate skillId — Qualification planning; verify query frequency and selectivity before implementation.
- Candidate scope filter — Requirement evaluation; verify query frequency and selectivity before implementation.
- Candidate status filter — Training assignment; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  trainingRequirementCode: "<trainingRequirementCode>",
  trainingRequirementName: "<trainingRequirementName>",
  skillId: "<skillId>",
  skillName: "<skillName>",
  requirementScopeType: "<requirementScopeType>",
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

Scope target entity and ID/name owner; course/catalog identity; requirement-to-skill multiplicity; validity/recurrence; training evidence; status vocabulary and supersession.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: OPEN — role/process and course owners are not defined.
- Status vocabulary: OPEN — values not approved.
- Unresolved dependencies: see Section N; no silent resolution.


## P. Audit and Historical Data

Standard audit fields are flat and paired. Effective dates, assignment history, and lifecycle records must not be embedded. Retention and actor-source policies remain OPEN.

## Q. Real-World Manufacturing Exceptions

Do not infer workforce training eligibility, Tool availability, compatibility, or capacity from unrelated status fields. Invalid or unresolved targets remain visible for controlled reconciliation.

## R. Missing Field Assessment

Detailed requirements remain incomplete for this design. Review with workforce/tool-room/planning/maintenance and database roles. Known gaps: Scope target entity and ID/name owner; course/catalog identity; requirement-to-skill multiplicity; validity/recurrence; training evidence; status vocabulary and supersession.

## S. Missing Collection Assessment

No collection is added. Capacity target and training role/course owners are unresolved; do not reuse Work Center, Asset, or an external entity without approved identity ownership.

## T. Cross-Collection Consistency Check

- Inventory scope/type checked against v1.6.
- Defined references use singular ID/display pairs; Tool and Asset remain distinct under DEC-022.
- Repeated relationships are flat records.
- Resource-capacity target and certification/training taxonomies remain OPEN where identified.
- No cross-collection status vocabulary or numeric type is inferred.

## U. Field Completeness Metrics

See Sections E, F, and J for candidate fields, references, and indexes. Detailed scenario coverage is not established without collection-specific requirements. Known gaps: Scope target entity and ID/name owner; course/catalog identity; requirement-to-skill multiplicity; validity/recurrence; training evidence; status vocabulary and supersession.

## V. One Representative MongoDB Document

The single illustrative flat structure appears in Section L; no sample set is generated.

## W. Assumptions

- The collection inventory and approved ownership decisions control scope.
- DATA-001 requires separate flat records for repeated relationships.
- ID/name duplication is intentional strategic denormalization.
- APPROVED follows the prior owner instruction; open points remain unapproved.

## X. Open Issues

Scope target entity and ID/name owner; course/catalog identity; requirement-to-skill multiplicity; validity/recurrence; training evidence; status vocabulary and supersession.

## Y. Research Sources

Sources in the Research basis section below support the collection's operational use cases only; no vendor schema or status list is adopted.

## Z. Final Design Review

- **Status:** APPROVED under prior Project Owner instruction; not production readiness.
- **Covered:** bounded purpose, candidate fields, flat relationships, research, and unresolved scope.
- **Remaining:** Section X; capacity/identity taxonomy blockers must be resolved before implementation.
## Research basis

- [SAP Qualification Monitoring](https://help.sap.com/docs/SAP_S4HANA_ON-PREMISE/44a53a90e25549cb9982eb07bcf79c66/fea7e2539c70424de10000000a174cb4.html) — Expiry of qualifications can trigger renewal or training; supports training requirement lifecycle use cases.
- [SAP Digital Manufacturing Role Templates](https://help.sap.com/docs/sap-digital-manufacturing/operations-guide/role-templates) — Workforce scheduling considers skills and business requirements; used as operational context.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


