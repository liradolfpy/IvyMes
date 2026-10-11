**FILE TYPE: PROJECT SPECIFIC**

# mes_ai_dashboardDefinition — Collection Specification

**Document ID:** MES-COL-AI-dashboardDefinition  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** ai  
**Collection type:** Configuration  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Defines dashboard identity, intended audience, and logical analytics content without implementing a UI technology.

## B. Business definition

Dashboard Definition is a logical governed dashboard. KPI selections, layout widgets, and role access are repeated relationships and cannot be embedded arrays; only dashboardRoleAccess is separately inventoried, so KPI/visual element ownership remains unresolved.

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
| `dashboardCode` | String | Yes (proposed) | Stable dashboard identifier. | Pattern/uniqueness OPEN. | No | Candidate dashboardCode; uniqueness OPEN | Select dashboard. | Inventory; SAP Manage Dashboards | Dashboard catalog |
| `dashboardName` | String | Yes | Readable dashboard title. | Non-empty. | No | Candidate text search | User display. | DATA-003 | Dashboard access |
| `dashboardDescription` | String | No | Scalar audience/purpose explanation. | Plain text. | No | No index by default | Explain dashboard use. | Inventory | Governance |
| `dashboardContentLocator` | String | Conditional | Opaque external locator for a managed dashboard artifact if content is externally stored. | Content source, syntax, version and owner OPEN. | External locator, not entity reference unless provider registry is adopted. | Candidate locator | Keep UI payload outside flat metadata record. | SAP dashboard management; inventory logical scope | Open dashboard |
| `status` | String | Yes (proposed) | Dashboard definition lifecycle/publication state. | Vocabulary and publish/retire semantics OPEN. | No | Candidate status filter | Govern availability. | Lifecycle rules OPEN | Dashboard catalog |

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

Dashboard definition lifecycle/publication status; values OPEN.

## I. Relationships

**Outgoing:** No KPI array. KPI selection/link strategy is unresolved. Content locator is external scalar if adopted.  
**Incoming:** Dashboard Role Access and dashboard usage/reporting.  
**Dependencies:** KPI Definition; role/access owner; external dashboard/content system and repeated visual element model unresolved.

## J. Recommended indexes

- Default unique _id index.
- Candidate dashboardCode; uniqueness OPEN — Dashboard catalog; verify query frequency and selectivity before implementation.
- Candidate text search — Dashboard access; verify query frequency and selectivity before implementation.
- Candidate locator — Open dashboard; verify query frequency and selectivity before implementation.
- Candidate status filter — Dashboard catalog; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  dashboardCode: "<dashboardCode>",
  dashboardName: "<dashboardName>",
  dashboardDescription: "<dashboardDescription>",
  dashboardContentLocator: "<dashboardContentLocator>",
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

BLOCKER: separate flat relation for multiple KPI/visual content selections is not listed; role identities are external; content locator/provider/version; dashboard sharing/ownership and publication lifecycle; access-control boundary.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: OPEN — KPI relationship owner and role scope need definition.
- Status vocabulary: OPEN — values not approved.
- Unresolved dependencies: see Section N; no silent resolution.


## P. Audit and Historical Data

Standard audit fields remain flat and paired. Preserve KPI calculation versions and dashboard/access changes according to approved governance; retention and immutable publication history remain OPEN.

## Q. Real-World Manufacturing Exceptions

Do not publish a KPI with unresolved formula lineage, expose a dashboard through an unverified role, or treat an external locator as an MES entity. Access-denied and stale-source workflows remain OPEN.

## R. Missing Field Assessment

Detailed analytics and authorization requirements are incomplete. Review with production, quality, analytics, security, and data-governance roles. Known gaps: BLOCKER: separate flat relation for multiple KPI/visual content selections is not listed; role identities are external; content locator/provider/version; dashboard sharing/ownership and publication lifecycle; access-control boundary.

## S. Missing Collection Assessment

No collection is added. The inventory does not identify a KPI-to-dashboard content relationship collection or role master. Repeated KPI/visual links must not be represented by arrays; raise missing owners for inventory/change review.

## T. Cross-Collection Consistency Check

- Inventory name/module/type checked against v1.6.
- Dashboard and KPI identities use paired MES references where defined.
- External role identity remains explicitly external and unresolved.
- Flat relationship principle respected; KPI/dashboard item relationship is an open gap.
- Formula, access, status, and source lineage remain OPEN.

## U. Field Completeness Metrics

Field/reference/index baselines are in Sections E, F, and J. Detailed analytics/security requirements are not available; no completeness percentage is claimed. Known gaps: BLOCKER: separate flat relation for multiple KPI/visual content selections is not listed; role identities are external; content locator/provider/version; dashboard sharing/ownership and publication lifecycle; access-control boundary.

## V. One Representative MongoDB Document

The sole illustrative flat structure is in Section L; no sample dataset is generated.

## W. Assumptions

- Inventory and approved project rules bound this design.
- Repeated access grants are separate flat relationship records.
- ID/name duplication is intentional strategic denormalization.
- APPROVED follows prior owner instruction; OPEN formula/access decisions remain unresolved.

## X. Open Issues

BLOCKER: separate flat relation for multiple KPI/visual content selections is not listed; role identities are external; content locator/provider/version; dashboard sharing/ownership and publication lifecycle; access-control boundary.

## Y. Research Sources

See collection-specific public references in the Research basis section below. These sources inform analytics/dashboard scenarios only, not project-specific KPI or security rules.

## Z. Final Design Review

- **Status:** APPROVED per prior Project Owner instruction; not production readiness.
- **Covered:** logical purpose, candidate metadata, defined references, flat grants, research, and known missing relationships.
- **Remaining:** Section X; formula, role-owner, and dashboard-content blockers must be resolved before implementation.
## Research basis

- [SAP Digital Manufacturing Manage Dashboards](https://help.sap.com/docs/sap-digital-manufacturing/insights/manage-dashboards) — Dashboards select attributes from production analytic data and are subject to access levels; used for logical dashboard scenarios.
- [SAP Manage Analytics Dashboards Roles](https://help.sap.com/docs/SAP_APM/8c95dca44fda4d56a17f6494f421758d/5ecbd4fbbcbd411eba805a9556375f14.html) — Dashboard operations can be role-authorized; authorization vocabulary is not imported into MES.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


