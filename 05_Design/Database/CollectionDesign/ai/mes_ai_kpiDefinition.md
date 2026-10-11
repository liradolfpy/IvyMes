**FILE TYPE: PROJECT SPECIFIC**

# mes_ai_kpiDefinition — Collection Specification

**Document ID:** MES-COL-AI-kpiDefinition  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** ai  
**Collection type:** Configuration  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Defines governed KPI identity, scope, calculation reference/version, and ownership for analytics results.

## B. Business definition

KPI Definition is the metadata/control record for a calculated indicator. Calculation logic, source fields, time window, aggregation, and change governance must be versioned; no formula is inferred from vendor documentation.

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
| `kpiCode` | String | Yes (proposed) | Stable business identifier for KPI definition. | Pattern/uniqueness OPEN. | No | Candidate kpiCode; uniqueness OPEN | Select metric definition. | Inventory; SAP Manage Dashboards | Analytics calculation |
| `kpiName` | String | Yes | Readable KPI name. | Non-empty. | No | Candidate text search | Dashboard/report display. | DATA-003 | KPI viewing |
| `kpiDescription` | String | No | Scalar business explanation. | Plain text; no embedded formula object. | No | No index by default | Interpret KPI. | Inventory | Governance |
| `calculationReference` | String | Yes (proposed) | Opaque reference to approved calculation implementation/specification. | Owner, syntax, and immutability OPEN; do not store executable payload without decision. | External locator or controlled local reference unresolved. | Candidate lookup if stable | Link KPI to calculation logic. | Inventory; SAP KPI analytics | Calculation/version audit |
| `calculationVersion` | String | Yes (proposed) | Version of calculation logic. | Versioning rules OPEN. | No | Candidate kpiCode+version | Reproducibility. | Inventory calculation/version context | Historical KPI result |
| `sourceModuleCode` | String | Conditional | MES module code providing primary source data. | Must match Functional Structure module code; multiple sources need a flat relationship strategy. | Module code, not entity reference. | Candidate module filter | Identify ownership/source domain. | Inventory; Functional Structure | Source lineage |
| `status` | String | Yes (proposed) | Definition lifecycle/publication state. | Values/transitions and draft-vs-approved semantics OPEN. | No | Candidate status filter | Control calculation use. | Lifecycle rules OPEN | KPI publication |

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

KPI definition lifecycle/publication state; vocabulary and approval semantics OPEN.

## I. Relationships

**Outgoing:** Source module is a code; multiple metric/source dependencies are not modeled as arrays.  
**Incoming:** KPI Result and Dashboard Definition may refer to KPI identity once relationship strategy is established.  
**Dependencies:** Approved source data and formula owners; external calculation reference/source details are unresolved.

## J. Recommended indexes

- Default unique _id index.
- Candidate kpiCode; uniqueness OPEN — Analytics calculation; verify query frequency and selectivity before implementation.
- Candidate text search — KPI viewing; verify query frequency and selectivity before implementation.
- Candidate lookup if stable — Calculation/version audit; verify query frequency and selectivity before implementation.
- Candidate kpiCode+version — Historical KPI result; verify query frequency and selectivity before implementation.
- Candidate module filter — Source lineage; verify query frequency and selectivity before implementation.
- Candidate status filter — KPI publication; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  kpiCode: "<kpiCode>",
  kpiName: "<kpiName>",
  kpiDescription: "<kpiDescription>",
  calculationReference: "<calculationReference>",
  calculationVersion: "<calculationVersion>",
  sourceModuleCode: "<sourceModuleCode>",
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

Formula authority; source collection/field lineage; multiple source dimensions; unit/aggregation/window; version/effectivity; change approval; external calculation implementation locator; status values.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: OPEN — multiple-source relationship representation unresolved.
- Status vocabulary: OPEN — values not approved.
- Unresolved dependencies: see Section N; no silent resolution.


## P. Audit and Historical Data

Standard audit fields remain flat and paired. Preserve KPI calculation versions and dashboard/access changes according to approved governance; retention and immutable publication history remain OPEN.

## Q. Real-World Manufacturing Exceptions

Do not publish a KPI with unresolved formula lineage, expose a dashboard through an unverified role, or treat an external locator as an MES entity. Access-denied and stale-source workflows remain OPEN.

## R. Missing Field Assessment

Detailed analytics and authorization requirements are incomplete. Review with production, quality, analytics, security, and data-governance roles. Known gaps: Formula authority; source collection/field lineage; multiple source dimensions; unit/aggregation/window; version/effectivity; change approval; external calculation implementation locator; status values.

## S. Missing Collection Assessment

No collection is added. The inventory does not identify a KPI-to-dashboard content relationship collection or role master. Repeated KPI/visual links must not be represented by arrays; raise missing owners for inventory/change review.

## T. Cross-Collection Consistency Check

- Inventory name/module/type checked against v1.6.
- Dashboard and KPI identities use paired MES references where defined.
- External role identity remains explicitly external and unresolved.
- Flat relationship principle respected; KPI/dashboard item relationship is an open gap.
- Formula, access, status, and source lineage remain OPEN.

## U. Field Completeness Metrics

Field/reference/index baselines are in Sections E, F, and J. Detailed analytics/security requirements are not available; no completeness percentage is claimed. Known gaps: Formula authority; source collection/field lineage; multiple source dimensions; unit/aggregation/window; version/effectivity; change approval; external calculation implementation locator; status values.

## V. One Representative MongoDB Document

The sole illustrative flat structure is in Section L; no sample dataset is generated.

## W. Assumptions

- Inventory and approved project rules bound this design.
- Repeated access grants are separate flat relationship records.
- ID/name duplication is intentional strategic denormalization.
- APPROVED follows prior owner instruction; OPEN formula/access decisions remain unresolved.

## X. Open Issues

Formula authority; source collection/field lineage; multiple source dimensions; unit/aggregation/window; version/effectivity; change approval; external calculation implementation locator; status values.

## Y. Research Sources

See collection-specific public references in the Research basis section below. These sources inform analytics/dashboard scenarios only, not project-specific KPI or security rules.

## Z. Final Design Review

- **Status:** APPROVED per prior Project Owner instruction; not production readiness.
- **Covered:** logical purpose, candidate metadata, defined references, flat grants, research, and known missing relationships.
- **Remaining:** Section X; formula, role-owner, and dashboard-content blockers must be resolved before implementation.
## Research basis

- [SAP Digital Manufacturing Manage Dashboards](https://help.sap.com/docs/sap-digital-manufacturing/insights/manage-dashboards) — Dashboards select production data attributes from analytic data objects and build visualizations; supports governed KPI sourcing scenarios.
- [SAP Digital Manufacturing Performance KPI](https://help.sap.com/docs/sap-digital-manufacturing/insights/performance-kpi?version=latest) — Illustrates a defined performance indicator comparing actual production speed with standard rate; formula itself is not adopted.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


