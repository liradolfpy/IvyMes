**FILE TYPE: PROJECT SPECIFIC**

# mes_man_certification — Collection Specification

**Document ID:** MES-COL-MAN-certification  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** man  
**Collection type:** Transaction  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Records certification issue, scope, expiry, and current validity for an Operator.

## B. Business definition

Certification is an operator-specific credential assignment, distinct from reusable Skill and training completion. Each credential assignment is a separate record.

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
| `operatorId` | String | Yes | Operator receiving certification. | 16-digit ID resolving mes_man_operator. | Yes: operatorId + operatorName -> mes_man_operator | Candidate operatorId + expiryDate | Eligibility lookup. | DEC-022; SAP ME certification assignment | Work assignment validation |
| `operatorName` | String | Yes | Operator display snapshot. | Paired with operatorId. | Yes: paired with operatorId | No index by default | Readable credential list. | DATA-003 | Supervisor review |
| `certificationCode` | String | Yes (proposed) | Credential type/code. | Taxonomy owner and uniqueness OPEN; no certification-type collection listed. | Scalar classification pending owner decision. | Candidate code + operator | Identify credential type. | Inventory; SAP ME certifications | Authorization |
| `certificationName` | String | Yes | Credential type display name. | Must pair if certificationCode is governed as entity; owner unresolved. | Pair semantics OPEN. | No index by default | Readable credential. | DATA-003 if entity | Credential display |
| `certificateNumber` | String | Conditional | Issuer-provided certificate identifier. | External identifier; issuer namespace OPEN. | External identifier, not MES ID. | Candidate issuer+number | Evidence reconciliation. | SAP ME certification assignment | Audit/review |
| `issuedAt` | Date | Yes (proposed) | Credential effective/issue date. | BSON Date; semantics OPEN. | No | No index by default | Validity tracking. | SAP ME certification assignment | Qualification |
| `expiresAt` | Date | Conditional | Credential expiry date. | BSON Date >= issuedAt if applicable. | No | Candidate operator+expiresAt | Renewal warning and eligibility. | SAP Qualification Monitoring | Expiry handling |
| `status` | String | Yes (proposed) | Certification assignment validity/hold/revocation state. | Project vocabulary and transitions OPEN; no vendor values adopted. | No | Candidate operator+status+expiry | Control assignment eligibility. | Inventory; SAP ME certification example | Authorization |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `operatorId` — Yes: operatorId + operatorName -> mes_man_operator.
- `operatorName` — Yes: paired with operatorId.

## G. Validation rules

- Flat top-level scalar fields only; no nested documents or arrays.
- MES IDs are 16-digit strings; external identifiers remain separate.
- Enforce only approved code patterns and uniqueness scopes. Do not invent OPEN scopes or status vocabularies.
- Required ID/display pairs are complete and consistent; optional pairs are both absent or both populated.
- Audit actor pairs are complete; updatedAt >= createdAt.
- Retired reference records remain historically resolvable when established by decision.

## H. Status values

Credential assignment state, distinct from Operator or Employee status; allowed values OPEN.

## I. Relationships

**Outgoing:** Operator pair; certification type identity currently a scalar because no owner collection is defined.  
**Incoming:** Training, authorization, operator assignments and audit consumers.  
**Dependencies:** Operator and certification taxonomy/evidence source. Employee identity is reached through Operator, not duplicated here unless required.

## J. Recommended indexes

- Default unique _id index.
- Candidate operatorId + expiryDate — Work assignment validation; verify query frequency and selectivity before implementation.
- Candidate code + operator — Authorization; verify query frequency and selectivity before implementation.
- Candidate issuer+number — Audit/review; verify query frequency and selectivity before implementation.
- Candidate operator+expiresAt — Expiry handling; verify query frequency and selectivity before implementation.
- Candidate operator+status+expiry — Authorization; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  operatorId: "<operatorId>",
  operatorName: "<operatorName>",
  certificationCode: "<certificationCode>",
  certificationName: "<certificationName>",
  certificateNumber: "<certificateNumber>",
  issuedAt: <value>,
  expiresAt: <value>,
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

BLOCKER: certification-type owner/entity classification; required ID/name pair if type is entity; issuer and evidence storage locator; issue/expiry semantics; revocation and hold vocabularies; training relationship; privacy/retention.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: OPEN — certification taxonomy is not owned by a defined collection.
- Status vocabulary: OPEN — values not approved.
- Unresolved dependencies: see Section N; no silent resolution.


## P. Audit and Historical Data

Standard audit fields are flat and paired. Effective dates and credential expiration must be BSON Dates where specified. Retention, record correction, and evidence storage rules remain OPEN.

## Q. Real-World Manufacturing Exceptions

Preserve expired, ended, revoked, or reassessed relationships for history where required; do not treat an expired qualification as active by inference. Exact transitions remain OPEN.

## R. Missing Field Assessment

Collection-specific requirement detail is incomplete. Review with workforce, production supervision, HR/integration, and database roles. Known gaps: BLOCKER: certification-type owner/entity classification; required ID/name pair if type is entity; issuer and evidence storage locator; issue/expiry semantics; revocation and hold vocabularies; training relationship; privacy/retention.

## S. Missing Collection Assessment

No collection is added. Crew membership and operator skill are separate flat records. Certification type ownership is unresolved; do not invent a catalog or embed certificate payloads.

## T. Cross-Collection Consistency Check

- Inventory identity/module/type: checked against v1.6.
- Flat relationships and singular naming: PASS for defined fields.
- Operator references remain distinct from Employee identity; DEC-022 respected.
- Every identified MES entity reference has an ID/display pair.
- Qualification/certification taxonomies, status, overlap, and external ownership remain OPEN.

## U. Field Completeness Metrics

Field and requiredness counts are in Section E; ID/name pairs in Section F; candidate indexes in Section J. No full scenario coverage metric is claimed without detailed requirements. Known gaps: BLOCKER: certification-type owner/entity classification; required ID/name pair if type is entity; issuer and evidence storage locator; issue/expiry semantics; revocation and hold vocabularies; training relationship; privacy/retention.

## V. One Representative MongoDB Document

The single illustrative flat document is in Section L; no additional examples or sample-data set are generated.

## W. Assumptions

- Inventory and approved DEC-022 ownership govern scope.
- Membership, skill, and certification assignments are separate records, never embedded arrays.
- ID/name duplication is intentional strategic denormalization.
- APPROVED follows owner instruction; unresolved fields/taxonomies remain OPEN.

## X. Open Issues

BLOCKER: certification-type owner/entity classification; required ID/name pair if type is entity; issuer and evidence storage locator; issue/expiry semantics; revocation and hold vocabularies; training relationship; privacy/retention.

## Y. Research Sources

See the collection-specific public documentation under Research basis below. It informs staffing, qualifications, and validity scenarios only.

## Z. Final Design Review

- **Status:** APPROVED per Project Owner direction; not production readiness.
- **Covered:** identity boundaries, relationship-record representation, required references, research, and open points.
- **Remaining:** Section X and unresolved qualification/effectivity rules.
## Research basis

- [SAP ME User Certification Assignment Maintenance](https://help.sap.com/docs/SAP_MANUFACTURING_EXECUTION/e1adc70af32241619335c8768a892edb/bdb9854d27934d5682d6e599dea3b522.html) — Certification assignment includes validity dates, status, and evidence file metadata; project stores only fields authorized by its own scope.
- [SAP Qualification Monitoring](https://help.sap.com/docs/SAP_S4HANA_ON-PREMISE/44a53a90e25549cb9982eb07bcf79c66/fea7e2539c70424de10000000a174cb4.html) — Valid qualifications are monitored for expiry and renewal/training action.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


