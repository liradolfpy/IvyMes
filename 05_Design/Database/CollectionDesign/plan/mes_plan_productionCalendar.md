**FILE TYPE: PROJECT SPECIFIC**

# mes_plan_productionCalendar — Collection Specification

**Document ID:** MES-COL-PLAN-productionCalendar  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** plan  
**Collection type:** Configuration  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Defines calendar context used by planning/scheduling to determine working and nonworking time and exceptions.

## B. Business definition

Production calendar provides time-availability rules. DATA-001 forbids embedded interval/exception arrays; exact definition-versus-rule record grain is unresolved.

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
| `calendarCode` | String | Yes (proposed) | Business-facing calendar identifier. | Pattern and uniqueness scope OPEN. | No | Candidate calendarCode lookup; uniqueness OPEN | Select the calendar. | Inventory; SAP calendar concepts | Calendar selection |
| `calendarName` | String | Yes | Readable calendar name. | Non-empty. | No | Candidate text search | UI/reporting display. | DATA-003; SAP calendar concepts | Planning |
| `plantId` | String | Conditional (proposed) | Plant scope if calendar is plant-specific. | If present pair with plantName; 16-digit ID. | Yes: plantId + plantName -> mes_plan_plant | Candidate plant scope | Site-level calendar selection. | Inventory; SAP Calendars | Scheduling by site |
| `plantName` | String | Conditional | Plant display snapshot. | Both plant fields present or absent. | Yes: paired with plantId | No index by default | Readable scope. | DATA-003 | Calendar display |
| `workCenterId` | String | Conditional (proposed) | Work-center scope if calendar rules are local to a work center. | If present pair with workCenterName; not machine/resource association. | Yes: workCenterId + workCenterName -> mes_plan_workCenter | Candidate scope lookup | Local operating calendar context. | Inventory; DEC-022 | Scheduling |
| `workCenterName` | String | Conditional | Work-center display snapshot. | Both work-center fields present or absent. | Yes: paired with workCenterId | No index by default | Readable scope. | DATA-003 | Calendar display |
| `status` | String | Proposed | Calendar-definition lifecycle/availability. | Vocabulary and transitions OPEN. | No | Candidate status filter | Preserve historical planning context. | Lifecycle rules OPEN | Calendar selection |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `plantId` — Yes: plantId + plantName -> mes_plan_plant.
- `plantName` — Yes: paired with plantId.
- `workCenterId` — Yes: workCenterId + workCenterName -> mes_plan_workCenter.
- `workCenterName` — Yes: paired with workCenterId.

## G. Validation rules

- Flat top-level scalar fields only; no nested documents or arrays.
- MES IDs are 16-digit strings; external identifiers remain separate.
- Enforce only approved code patterns and uniqueness scopes. Do not invent OPEN scopes or status vocabularies.
- Required ID/display pairs are complete and consistent; optional pairs are both absent or both populated.
- Audit actor pairs are complete; updatedAt >= createdAt.
- Retired reference records remain historically resolvable when established by decision.

## H. Status values

Meaning and values OPEN; status applies to calendar definition only.

## I. Relationships

**Outgoing:** Optional plant/work-center scope pairs.  
**Incoming:** Schedule, capacity, shift and performance consumers where established.  
**Dependencies:** Plant/work center are optional scope candidates. Calendar/shift association direction OPEN. Inventory requires flat intervals but not record grain.

## J. Recommended indexes

- Default unique _id index.
- Candidate calendarCode lookup; uniqueness OPEN — Calendar selection; verify query frequency and selectivity before implementation.
- Candidate text search — Planning; verify query frequency and selectivity before implementation.
- Candidate plant scope — Scheduling by site; verify query frequency and selectivity before implementation.
- Candidate scope lookup — Scheduling; verify query frequency and selectivity before implementation.
- Candidate status filter — Calendar selection; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  calendarCode: "<calendarCode>",
  calendarName: "<calendarName>",
  plantId: "<plantId>",
  plantName: "<plantName>",
  workCenterId: "<workCenterId>",
  workCenterName: "<workCenterName>",
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

BLOCKER: define calendar vs interval/exception record grain, recurrence, effectivity, timezone, plant/work-center/shift scope and exception precedence. No arrays or inferred parent collection.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: OPEN — scope optionality and rule grain unresolved.
- Status vocabulary: OPEN — values not approved.
- Unresolved dependencies: see Section N; no silent resolution.


## P. Audit and Historical Data

Standard audit fields are flat: createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity source, retention period, and immutable-history requirements remain OPEN unless an approved project rule states otherwise. Lifecycle or location history must not be embedded as arrays.

## Q. Real-World Manufacturing Exceptions

Handle retired, unmapped, or unavailable references without silently substituting a different identity. Store only scalar exception context already represented in the field dictionary. Detailed correction, retry, and reconciliation behavior remains OPEN where applicable.

## R. Missing Field Assessment

The current project copy does not provide collection-specific functional requirement artifacts for this item. Potential missing fields must be evaluated with operations, planning, engineering, quality, integration, maintenance, material, and database roles before implementation. Known assessment items: BLOCKER: define calendar vs interval/exception record grain, recurrence, effectivity, timezone, plant/work-center/shift scope and exception precedence. No arrays or inferred parent collection.

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
- Unresolved gaps: BLOCKER: define calendar vs interval/exception record grain, recurrence, effectivity, timezone, plant/work-center/shift scope and exception precedence. No arrays or inferred parent collection.

## V. One Representative MongoDB Document

The single illustrative flat document structure is provided in Section L. No second document or sample-data set is added here.

## W. Assumptions

- The inventory determines collection identity, module, type, and bounded purpose.
- DATA-001 requires separate flat records for repeated concepts.
- DATA-003 ID/display duplication is intentional strategic denormalization.
- APPROVED is the user's requested specification status; OPEN questions remain unapproved and unresolved.

## X. Open Issues

BLOCKER: define calendar vs interval/exception record grain, recurrence, effectivity, timezone, plant/work-center/shift scope and exception precedence. No arrays or inferred parent collection.

## Y. Research Sources

The collection-specific public research references and their supported design context are in the Research basis section immediately below. They are functional benchmarks only and do not establish this project's schema or value lists.

## Z. Final Design Review

- **Status:** APPROVED under the Project Owner's prior instruction; not equivalent to production readiness.
- **Covered:** inventory-bounded purpose, candidate field baseline, flat-document constraint, known references, research context, and explicit unresolved questions.
- **Remaining:** Section X and any unresolved field-level validation or value rules.
- **Readiness:** reviewable collection-design baseline. Do not implement a blocker-marked schema until its semantic blocker is resolved.
## Research basis

- [SAP ERP Scheduling Rules](https://help.sap.com/docs/SAP_ERP/698b19fa88b846359bc611f11184c810/6886bf53f106b44ce10000000a174cb4.html) — Working time uses calendars, workdays, shifts and breaks; informs required semantics.
- [SAP IBP Calendars](https://help.sap.com/docs/SAP_INTEGRATED_BUSINESS_PLANNING/feae3cea3cc549aaa9d9de7d363a83e6/4f2a9a904d954c7c8e6804619dbc452f.html) — Planning calendars distinguish working/nonworking intervals and may derive from factory calendars; no vendor schema adopted.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


