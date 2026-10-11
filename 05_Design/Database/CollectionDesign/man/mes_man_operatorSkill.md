**FILE TYPE: PROJECT SPECIFIC**

# mes_man_operatorSkill — Collection Specification

**Document ID:** MES-COL-MAN-operatorSkill  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** man  
**Collection type:** Relationship  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Records an Operator’s assessed skill level and validity as a separate flat relationship record.

## B. Business definition

Operator Skill links one operator to one skill assessment, preserving proficiency, assessor, and validity without embedding a skills array.

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
| `operatorId` | String | Yes | Operator identity receiving skill assessment. | 16-digit ID resolving mes_man_operator. | Yes: operatorId + operatorName -> mes_man_operator | Candidate operatorId + skillId | Qualification relationship. | DATA-003; inventory | Assignment eligibility |
| `operatorName` | String | Yes | Operator display snapshot. | Paired with operatorId. | Yes: paired with operatorId | No index by default | Readable skill matrix. | DATA-003 | Supervisor review |
| `skillId` | String | Yes | Reusable skill definition identity. | 16-digit ID resolving mes_man_skill. | Yes: skillId + skillName -> mes_man_skill | Candidate skillId + validity | Controlled skill reference. | DATA-003; inventory | Authorization |
| `skillName` | String | Yes | Skill display snapshot. | Paired with skillId. | Yes: paired with skillId | No index by default | Readable qualification. | DATA-003 | Skill matrix |
| `proficiencyLevel` | String | Conditional (proposed) | Assessed proficiency level. | Scale, meaning, and allowable values OPEN. | No | Candidate filter after taxonomy approval | Evaluate role/work eligibility. | SAP workforce scheduling and skill docs | Operator assignment |
| `assessorEmployeeId` | String | Conditional (proposed) | Employee who assessed this skill. | If populated pair with assessorEmployeeName. | Yes: assessorEmployeeId + assessorEmployeeName -> mes_man_employee | Candidate assessor lookup | Assessment accountability. | DATA-003; inventory assessor concept | Audit |
| `assessorEmployeeName` | String | Conditional | Assessor display snapshot. | Both assessor fields present or absent. | Yes: paired with assessorEmployeeId | No index by default | Readable accountability. | DATA-003 | Review |
| `validFrom` | Date | Conditional | Start of skill assessment validity. | BSON Date; interval semantics OPEN. | No | Candidate operator+skill+validFrom | Time-valid qualification. | SAP qualification validity | Eligibility |
| `validTo` | Date | Conditional | End of skill assessment validity. | BSON Date >= validFrom. | No | Candidate validity filter | Expire assessment. | SAP qualification validity | Renewal |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `operatorId` — Yes: operatorId + operatorName -> mes_man_operator.
- `operatorName` — Yes: paired with operatorId.
- `skillId` — Yes: skillId + skillName -> mes_man_skill.
- `skillName` — Yes: paired with skillId.
- `assessorEmployeeId` — Yes: assessorEmployeeId + assessorEmployeeName -> mes_man_employee.
- `assessorEmployeeName` — Yes: paired with assessorEmployeeId.

## G. Validation rules

- Flat top-level scalar fields only; no nested documents or arrays.
- MES IDs are 16-digit strings; external identifiers remain separate.
- Enforce only approved code patterns and uniqueness scopes. Do not invent OPEN scopes or status vocabularies.
- Required ID/display pairs are complete and consistent; optional pairs are both absent or both populated.
- Audit actor pairs are complete; updatedAt >= createdAt.
- Retired reference records remain historically resolvable when established by decision.

## H. Status values

No shared status vocabulary; validity period and assessment lifecycle meaning need clarification.

## I. Relationships

**Outgoing:** Operator, Skill, optional assessing Employee pairs.  
**Incoming:** Authorization, labor assignment, training and skill matrix queries.  
**Dependencies:** mes_man_operator, mes_man_skill, mes_man_employee. Proficiency and certification boundary OPEN.

## J. Recommended indexes

- Default unique _id index.
- Candidate operatorId + skillId — Assignment eligibility; verify query frequency and selectivity before implementation.
- Candidate skillId + validity — Authorization; verify query frequency and selectivity before implementation.
- Candidate filter after taxonomy approval — Operator assignment; verify query frequency and selectivity before implementation.
- Candidate assessor lookup — Audit; verify query frequency and selectivity before implementation.
- Candidate operator+skill+validFrom — Eligibility; verify query frequency and selectivity before implementation.
- Candidate validity filter — Renewal; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  operatorId: "<operatorId>",
  operatorName: "<operatorName>",
  skillId: "<skillId>",
  skillName: "<skillName>",
  proficiencyLevel: "<proficiencyLevel>",
  assessorEmployeeId: "<assessorEmployeeId>",
  assessorEmployeeName: "<assessorEmployeeName>",
  validFrom: <value>,
  validTo: <value>,
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

Proficiency scale; assessment evidence; who may assess; validity/expiry; relationship uniqueness vs history; skill vs certification semantics; whether skill record needs status.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: PASS — all defined entity references have singular ID/name pairs.
- Status vocabulary: OPEN — status vs validity semantics to settle.
- Unresolved dependencies: see Section N; no silent resolution.


## P. Audit and Historical Data

Standard audit fields are flat and paired. Effective dates and credential expiration must be BSON Dates where specified. Retention, record correction, and evidence storage rules remain OPEN.

## Q. Real-World Manufacturing Exceptions

Preserve expired, ended, revoked, or reassessed relationships for history where required; do not treat an expired qualification as active by inference. Exact transitions remain OPEN.

## R. Missing Field Assessment

Collection-specific requirement detail is incomplete. Review with workforce, production supervision, HR/integration, and database roles. Known gaps: Proficiency scale; assessment evidence; who may assess; validity/expiry; relationship uniqueness vs history; skill vs certification semantics; whether skill record needs status.

## S. Missing Collection Assessment

No collection is added. Crew membership and operator skill are separate flat records. Certification type ownership is unresolved; do not invent a catalog or embed certificate payloads.

## T. Cross-Collection Consistency Check

- Inventory identity/module/type: checked against v1.6.
- Flat relationships and singular naming: PASS for defined fields.
- Operator references remain distinct from Employee identity; DEC-022 respected.
- Every identified MES entity reference has an ID/display pair.
- Qualification/certification taxonomies, status, overlap, and external ownership remain OPEN.

## U. Field Completeness Metrics

Field and requiredness counts are in Section E; ID/name pairs in Section F; candidate indexes in Section J. No full scenario coverage metric is claimed without detailed requirements. Known gaps: Proficiency scale; assessment evidence; who may assess; validity/expiry; relationship uniqueness vs history; skill vs certification semantics; whether skill record needs status.

## V. One Representative MongoDB Document

The single illustrative flat document is in Section L; no additional examples or sample-data set are generated.

## W. Assumptions

- Inventory and approved DEC-022 ownership govern scope.
- Membership, skill, and certification assignments are separate records, never embedded arrays.
- ID/name duplication is intentional strategic denormalization.
- APPROVED follows owner instruction; unresolved fields/taxonomies remain OPEN.

## X. Open Issues

Proficiency scale; assessment evidence; who may assess; validity/expiry; relationship uniqueness vs history; skill vs certification semantics; whether skill record needs status.

## Y. Research Sources

See the collection-specific public documentation under Research basis below. It informs staffing, qualifications, and validity scenarios only.

## Z. Final Design Review

- **Status:** APPROVED per Project Owner direction; not production readiness.
- **Covered:** identity boundaries, relationship-record representation, required references, research, and open points.
- **Remaining:** Section X and unresolved qualification/effectivity rules.
## Research basis

- [SAP Digital Manufacturing Resource Orchestration Overview](https://help.sap.com/docs/sap-digital-manufacturing/resource-orchestration/overview-and-key-capabilities?version=latest) — Labor allocation balances operator skills, availability and workload.
- [SAP Qualification Monitoring](https://help.sap.com/docs/SAP_S4HANA_ON-PREMISE/44a53a90e25549cb9982eb07bcf79c66/fea7e2539c70424de10000000a174cb4.html) — Qualifications can have validity periods, expire, and trigger renewal/training actions.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


