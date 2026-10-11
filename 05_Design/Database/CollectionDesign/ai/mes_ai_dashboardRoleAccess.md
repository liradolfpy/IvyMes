**FILE TYPE: PROJECT SPECIFIC**

# mes_ai_dashboardRoleAccess — Collection Specification

**Document ID:** MES-COL-AI-dashboardRoleAccess  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** ai  
**Collection type:** Relationship  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Associates dashboard definitions with roles permitted to view or manage them.

## B. Business definition

Dashboard Role Access is a flat authorization relationship between one dashboard and one external or MES role identity. Each dashboard-role grant is a separate record, never an embedded role array.

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
| `dashboardId` | String | Yes | Dashboard definition identity. | 16-digit ID resolving mes_ai_dashboardDefinition. | Yes: dashboardId + dashboardName -> mes_ai_dashboardDefinition | Candidate dashboardId + role identifier | Link access grant to dashboard. | Inventory; DATA-003 | Dashboard authorization |
| `dashboardName` | String | Yes | Dashboard display snapshot. | Paired with dashboardId. | Yes: paired with dashboardId | No index by default | Readable access list. | DATA-003 | Administration |
| `externalRoleId` | String | Yes (proposed) | Identifier of role from the authorization provider/catalog. | Provider, namespace and whether role is MES-managed OPEN. | External role identity; pair with externalRoleName. | Candidate externalRoleId | Associate authorized identity. | Inventory; SAP dashboard access control | Access evaluation |
| `externalRoleName` | String | Yes (proposed) | Role display value paired with externalRoleId. | Must be consistent with external provider; role catalog owner OPEN. | Paired with externalRoleId. | No index by default | Readable authorization administration. | DATA-003 pair principle; no role collection listed | Access review |
| `permissionLevel` | String | Yes (proposed) | Granted dashboard action level. | View/manage vocabulary and conflict rules OPEN. | No | Candidate dashboard+role+permission | Define access effect. | SAP Manage Dashboards access levels | Authorization |
| `status` | String | Yes (proposed) | Grant lifecycle state. | Vocabulary and revocation semantics OPEN. | No | Candidate role+status | Manage grant revocation/history. | Lifecycle rules OPEN | Access validation |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `dashboardId` — Yes: dashboardId + dashboardName -> mes_ai_dashboardDefinition.
- `dashboardName` — Yes: paired with dashboardId.

## G. Validation rules

- Flat top-level scalar fields only; no nested documents or arrays.
- MES IDs are 16-digit strings; external identifiers remain separate.
- Enforce only approved code patterns and uniqueness scopes. Do not invent OPEN scopes or status vocabularies.
- Required ID/display pairs are complete and consistent; optional pairs are both absent or both populated.
- Audit actor pairs are complete; updatedAt >= createdAt.
- Retired reference records remain historically resolvable when established by decision.

## H. Status values

Grant lifecycle, not dashboard lifecycle. Values OPEN.

## I. Relationships

**Outgoing:** Dashboard pair and external role pair.  
**Incoming:** Dashboard authorization queries and audit/review.  
**Dependencies:** mes_ai_dashboardDefinition; external identity/role provider and permission model unresolved.

## J. Recommended indexes

- Default unique _id index.
- Candidate dashboardId + role identifier — Dashboard authorization; verify query frequency and selectivity before implementation.
- Candidate externalRoleId — Access evaluation; verify query frequency and selectivity before implementation.
- Candidate dashboard+role+permission — Authorization; verify query frequency and selectivity before implementation.
- Candidate role+status — Access validation; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  dashboardId: "<dashboardId>",
  dashboardName: "<dashboardName>",
  externalRoleId: "<externalRoleId>",
  externalRoleName: "<externalRoleName>",
  permissionLevel: "<permissionLevel>",
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

Role source/owner and ID format; whether role ID is provider external ID or MES ID; read/manage permission vocabulary; inheritance/deny precedence; effective dates; status/revocation history; uniqueness scope.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: OPEN — external role identity source and pair rules need approval.
- Status vocabulary: OPEN — values not approved.
- Unresolved dependencies: see Section N; no silent resolution.


## P. Audit and Historical Data

Standard audit fields remain flat and paired. Preserve KPI calculation versions and dashboard/access changes according to approved governance; retention and immutable publication history remain OPEN.

## Q. Real-World Manufacturing Exceptions

Do not publish a KPI with unresolved formula lineage, expose a dashboard through an unverified role, or treat an external locator as an MES entity. Access-denied and stale-source workflows remain OPEN.

## R. Missing Field Assessment

Detailed analytics and authorization requirements are incomplete. Review with production, quality, analytics, security, and data-governance roles. Known gaps: Role source/owner and ID format; whether role ID is provider external ID or MES ID; read/manage permission vocabulary; inheritance/deny precedence; effective dates; status/revocation history; uniqueness scope.

## S. Missing Collection Assessment

No collection is added. The inventory does not identify a KPI-to-dashboard content relationship collection or role master. Repeated KPI/visual links must not be represented by arrays; raise missing owners for inventory/change review.

## T. Cross-Collection Consistency Check

- Inventory name/module/type checked against v1.6.
- Dashboard and KPI identities use paired MES references where defined.
- External role identity remains explicitly external and unresolved.
- Flat relationship principle respected; KPI/dashboard item relationship is an open gap.
- Formula, access, status, and source lineage remain OPEN.

## U. Field Completeness Metrics

Field/reference/index baselines are in Sections E, F, and J. Detailed analytics/security requirements are not available; no completeness percentage is claimed. Known gaps: Role source/owner and ID format; whether role ID is provider external ID or MES ID; read/manage permission vocabulary; inheritance/deny precedence; effective dates; status/revocation history; uniqueness scope.

## V. One Representative MongoDB Document

The sole illustrative flat structure is in Section L; no sample dataset is generated.

## W. Assumptions

- Inventory and approved project rules bound this design.
- Repeated access grants are separate flat relationship records.
- ID/name duplication is intentional strategic denormalization.
- APPROVED follows prior owner instruction; OPEN formula/access decisions remain unresolved.

## X. Open Issues

Role source/owner and ID format; whether role ID is provider external ID or MES ID; read/manage permission vocabulary; inheritance/deny precedence; effective dates; status/revocation history; uniqueness scope.

## Y. Research Sources

See collection-specific public references in the Research basis section below. These sources inform analytics/dashboard scenarios only, not project-specific KPI or security rules.

## Z. Final Design Review

- **Status:** APPROVED per prior Project Owner instruction; not production readiness.
- **Covered:** logical purpose, candidate metadata, defined references, flat grants, research, and known missing relationships.
- **Remaining:** Section X; formula, role-owner, and dashboard-content blockers must be resolved before implementation.
## Research basis

- [SAP Digital Manufacturing Manage Dashboards](https://help.sap.com/docs/sap-digital-manufacturing/insights/manage-dashboards) — Dashboards can be shared with predefined access levels; supports grant relationship scenarios.
- [SAP Digital Manufacturing Role Templates](https://help.sap.com/docs/sap-digital-manufacturing/operations-guide/role-templates) — Role templates govern application access; external role ownership remains a project decision.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


