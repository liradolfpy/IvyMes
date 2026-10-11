**FILE TYPE: PROJECT SPECIFIC**

# mes_man_operator — Collection Specification

**Document ID:** MES-COL-MAN-operator  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** man  
**Collection type:** Master  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Maintains MES Operator profiles and operational lifecycle linked to shared Employee identity.

## B. Business definition

Operator is a workforce role/profile used for MES shop-floor activity. DEC-022 requires each Operator to reference Employee by employeeId and employeeName.

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
| `operatorCode` | String | Yes (proposed) | MES-facing operator identifier separate from _id. | Pattern/uniqueness OPEN. | No | Candidate lookup; uniqueness OPEN | Operator selection. | Inventory; DEC-022 | Operator assignment |
| `operatorName` | String | Yes (proposed) | Readable operator-profile display name. | Snapshot relationship to employeeName OPEN. | No | Candidate text search | Operator display. | DATA-003 | Shop-floor UI |
| `employeeId` | String | Yes | MES Employee identity for this Operator. | 16-digit ID resolving mes_man_employee. | Yes: employeeId + employeeName -> mes_man_employee | Candidate employeeId; unique only if cardinality approved | Shared person identity. | DEC-022 | Person resolution |
| `employeeName` | String | Yes | Employee display snapshot paired with employeeId. | Must resolve consistently at write time; refresh policy OPEN. | Yes: paired with employeeId | No index by default | Avoid lookup for display. | DATA-003; DEC-022 | Assignment display |
| `status` | String | Yes (proposed) | Operator-profile lifecycle/assignment eligibility. | Vocabulary OPEN; separate from employee state and attendance. | No | Candidate eligibility filter | Control MES assignment. | Lifecycle rules OPEN | Operator assignment |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `employeeId` — Yes: employeeId + employeeName -> mes_man_employee.
- `employeeName` — Yes: paired with employeeId.

## G. Validation rules

- Flat top-level scalar fields only; no nested documents or arrays.
- MES IDs are 16-digit strings; external identifiers remain separate.
- Enforce only approved code patterns and uniqueness scopes. Do not invent OPEN scopes or status vocabularies.
- Required ID/display pairs are complete and consistent; optional pairs are both absent or both populated.
- Audit actor pairs are complete; updatedAt >= createdAt.
- Retired reference records remain historically resolvable when established by decision.

## H. Status values

Operator-profile lifecycle/eligibility is separate from employee employment, attendance and availability. Values OPEN.

## I. Relationships

**Outgoing:** Required employeeId/employeeName pair to mes_man_employee.  
**Incoming:** Labor assignment, crew, attendance, training, qualification, production, quality and other consumers where approved specs require operator reference.  
**Dependencies:** mes_man_employee confirmed; external HR source remains undecided. Skills/certifications use separate relationship/credential records; no arrays.

## J. Recommended indexes

- Default unique _id index.
- Candidate lookup; uniqueness OPEN — Operator assignment; verify query frequency and selectivity before implementation.
- Candidate text search — Shop-floor UI; verify query frequency and selectivity before implementation.
- Candidate employeeId; unique only if cardinality approved — Person resolution; verify query frequency and selectivity before implementation.
- Candidate eligibility filter — Operator assignment; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  operatorCode: "<operatorCode>",
  operatorName: "<operatorName>",
  employeeId: "<employeeId>",
  employeeName: "<employeeName>",
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

Operator code requirement/uniqueness; operatorName vs employeeName; Employee-to-Operator cardinality; lifecycle; authorization; inactive-history behavior.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: PASS — required Employee ID/name pair defined.
- Status vocabulary: OPEN — values not approved.
- Unresolved dependencies: see Section N; no silent resolution.


## P. Audit and Historical Data

Standard audit fields are flat: createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity source, retention period, and immutable-history requirements remain OPEN unless an approved project rule states otherwise. Lifecycle or location history must not be embedded as arrays.

## Q. Real-World Manufacturing Exceptions

Handle retired, unmapped, or unavailable references without silently substituting a different identity. Store only scalar exception context already represented in the field dictionary. Detailed correction, retry, and reconciliation behavior remains OPEN where applicable.

## R. Missing Field Assessment

The current project copy does not provide collection-specific functional requirement artifacts for this item. Potential missing fields must be evaluated with operations, planning, engineering, quality, integration, maintenance, material, and database roles before implementation. Known assessment items: Operator code requirement/uniqueness; operatorName vs employeeName; Employee-to-Operator cardinality; lifecycle; authorization; inactive-history behavior.

## S. Missing Collection Assessment

No collection is added by this specification. The Collection Inventory remains authoritative. If an unresolved parent, interval, classification, location, or external-system owner requires another collection, report it for inventory review; do not embed the repeated concept.

## T. Cross-Collection Consistency Check

- Inventory name, module, type, and purpose: checked against Collection Inventory v1.6.
- Flat document and singular field stems: PASS for defined fields.
- Project ID and audit conventions: PASS.
- Entity ID/display pairs: see Section F; unresolved owners/cardinality are OPEN.
- Types: follow current conventions for defined fields.
- Status and business-number rules: remain OPEN where not explicitly decided.
- References and dependencies: see Sections I and N; no foreign-key enforcement is assumed.

## U. Field Completeness Metrics

Metrics are provisional because detailed collection requirements and the full Stage 2 research artifacts are unavailable in this active project copy.

- Proposed fields: see Section E.
- Required / conditional / optional: see Section E; proposed requiredness is not treated as a confirmed business rule.
- ID–Name pairs: see Section F.
- Status/control fields: collection-specific field dictionary.
- Audit fields: 6.
- External fields: see Section E.
- Scenario coverage: no percentage claimed until detailed requirements are linked.
- Research sources: see Section Y.
- Unresolved gaps: Operator code requirement/uniqueness; operatorName vs employeeName; Employee-to-Operator cardinality; lifecycle; authorization; inactive-history behavior.

## V. One Representative MongoDB Document

The single illustrative flat document structure is provided in Section L. No second document or sample-data set is added here.

## W. Assumptions

- The inventory determines collection identity, module, type, and bounded purpose.
- DATA-001 requires separate flat records for repeated concepts.
- DATA-003 ID/display duplication is intentional strategic denormalization.
- APPROVED is the user's requested specification status; OPEN questions remain unapproved and unresolved.

## X. Open Issues

Operator code requirement/uniqueness; operatorName vs employeeName; Employee-to-Operator cardinality; lifecycle; authorization; inactive-history behavior.

## Y. Research Sources

The collection-specific public research references and their supported design context are in the Research basis section immediately below. They are functional benchmarks only and do not establish this project's schema or value lists.

## Z. Final Design Review

- **Status:** APPROVED under the Project Owner's prior instruction; not equivalent to production readiness.
- **Covered:** inventory-bounded purpose, candidate field baseline, flat-document constraint, known references, research context, and explicit unresolved questions.
- **Remaining:** Section X and any unresolved field-level validation or value rules.
- **Readiness:** reviewable collection-design baseline. Do not implement a blocker-marked schema until its semantic blocker is resolved.
## Research basis

- [SAP ME Personnel Qualifications Integration](https://help.sap.com/docs/SAP_MANUFACTURING_EXECUTION/e1adc70af32241619335c8768a892edb/638cd4971d6f49c899b2481aa80675c5.html) — Describes personnel and qualification integration into MES; supports separation of identity and qualification.
- [SAP Digital Manufacturing Role Templates](https://help.sap.com/docs/sap-digital-manufacturing/operations-guide/role-templates) — Documents role/skill-based workforce configuration and labor assignment context.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


