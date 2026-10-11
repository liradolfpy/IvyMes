**FILE TYPE: PROJECT SPECIFIC**

# mes_prod_routingOperation — Collection Specification

**Document ID:** MES-COL-PROD-routingOperation  
**Version:** 1.0  
**Status:** APPROVED  
**Approval basis:** Project Owner's prior instruction for collection specifications; OPEN issues below remain unresolved and are not thereby approved.  
**Module:** prod  
**Collection type:** Relationship  
**Inventory baseline:** Collection Inventory v1.6.

## A. Collection purpose

Represents one ordered operation step in one routing, with scalar step requirements and reusable Operation identity.

## B. Business definition

Routing Operation is a routing-specific occurrence, distinct from reusable Operation master identity. Each step is a separate flat record, linked to Routing and Operation; repeated steps may reference the same operation identity.

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
| `routingId` | String | Yes | Parent routing identity. | 16-digit ID resolving mes_prod_routing. | Yes: routingId + routingName -> mes_prod_routing | Candidate routingId + sequence | Parent/child step linkage. | DEC-003; DATA-001 | Routing retrieval |
| `routingName` | String | Yes | Routing display snapshot. | Paired with routingId. | Yes: paired with routingId | No index by default | Read without lookup. | DATA-003 | Step display |
| `operationId` | String | Yes | Reusable operation identity. | 16-digit ID resolving mes_prod_operation. | Yes: operationId + operationName -> mes_prod_operation | Candidate operationId | Reuse operation master. | DEC-003; SAP routing integration | Step identity |
| `operationName` | String | Yes | Operation display snapshot. | Paired with operationId. | Yes: paired with operationId | No index by default | Avoid routine lookup. | DATA-003 | Execution display |
| `routingOperationSequence` | Int32 | Yes | Explicit order of this step within routing. | Positive integer; uniqueness per routing/revision unresolved. | No | Candidate unique routingId+sequence after scope approval | Order flat records deterministically. | DATA-001; SAP routing step sequence | Ordered routing traversal |
| `workCenterId` | String | Conditional (proposed) | Planning work-center reference if the routing step is assigned to one. | If present paired with workCenterName; no resource/machine equivalence. | Yes: workCenterId + workCenterName -> mes_plan_workCenter | Candidate workCenterId | Step planning location. | Inventory; DEC-022 | Scheduling/execution context |
| `workCenterName` | String | Conditional | Work-center display snapshot. | Both work-center fields present or absent. | Yes: paired with workCenterId | No index by default | Readable context. | DATA-003 | Operator display |
| `standardCycleTime` | Double | Conditional | Standard cycle-time value when required by approved production rule. | Double; time unit and precision OPEN; no decimal constructors. | No | No index by default | Cycle planning input. | DEC-010 applies to this exact field; unit OPEN | Scheduling |
| `status` | String | Proposed | Step-specific lifecycle/validity state if kept distinct from parent routing. | Meaning and values OPEN; confirm if redundant. | No | Candidate status filter | May control step eligibility. | Inventory; status meaning unresolved | Step selection |

## D. Data types

IDs, codes, names and descriptions are BSON String unless the field dictionary states otherwise; timestamps are BSON Date. Numeric and Boolean fields use their per-field BSON type. Any OPEN type is unresolved and blocks sample generation for that field. Decimal constructors are prohibited by DATA-002/DEC-010.

## E. Required and optional fields

Fields marked Yes are proposed as required unless a current decision explicitly establishes them. Fields marked No are optional. Proposed requiredness and unresolved rules remain identified in the dictionary.

## F. ID–Name reference pairs

- `routingId` — Yes: routingId + routingName -> mes_prod_routing.
- `routingName` — Yes: paired with routingId.
- `operationId` — Yes: operationId + operationName -> mes_prod_operation.
- `operationName` — Yes: paired with operationId.
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

Field necessity and vocabulary unresolved; do not duplicate parent lifecycle without a business rule.

## I. Relationships

**Outgoing:** Required Routing and Operation pairs; optional Work Center pair; no asset/resource link is inferred.  
**Incoming:** Execution, work instruction, quality and planning records may reference the routing step.  
**Dependencies:** mes_prod_routing, mes_prod_operation; Work Center link is limited to organizational planning identity; resource ownership unresolved.

## J. Recommended indexes

- Default unique _id index.
- Candidate routingId + sequence — Routing retrieval; verify query frequency and selectivity before implementation.
- Candidate operationId — Step identity; verify query frequency and selectivity before implementation.
- Candidate unique routingId+sequence after scope approval — Ordered routing traversal; verify query frequency and selectivity before implementation.
- Candidate workCenterId — Scheduling/execution context; verify query frequency and selectivity before implementation.
- Candidate status filter — Step selection; verify query frequency and selectivity before implementation.
- No unique constraint is recommended where uniqueness scope is OPEN.

## K. Audit fields

createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Actor identity ownership/source is OPEN; fields remain paired and flat.

## L. Representative structure

Illustrative shape only; placeholders are not sample data.


{
  _id: "1000000000000001",
  routingId: "<routingId>",
  routingName: "<routingName>",
  operationId: "<operationId>",
  operationName: "<operationName>",
  routingOperationSequence: <value>,
  workCenterId: "<workCenterId>",
  workCenterName: "<workCenterName>",
  standardCycleTime: <value>,
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

Sequence type/uniqueness scope; branching/alternate routing; repeated same operation in one routing; step-level work center assignment; operation-specific quantities/time units; whether status is needed; historical routing snapshot behavior.

## O. Cross-collection consistency check

- Inventory name and module: PASS.
- Flat-document and singular naming rules: PASS.
- ID format and standard audit pairs: PASS.
- References: PASS — required Routing/Operation ID-name pairs; optional Work Center pair.
- Status vocabulary: OPEN — field meaning/value rule requires decision.
- Unresolved dependencies: see Section N; no silent resolution.


## P. Audit and Historical Data

Standard flat audit fields are createdAt, createdById, createdBy, updatedAt, updatedById, updatedBy. Audit actor source and retention remain OPEN. Revision/effectivity history must be preserved by approved revision rules, not embedded arrays.

## Q. Real-World Manufacturing Exceptions

Address obsolete revisions, missing parent references, and effective-date conflicts without substituting another entity. Exception and correction semantics remain OPEN where not defined.

## R. Missing Field Assessment

Detailed collection-specific requirement artifacts were unavailable in this project batch. Assess proposed fields with engineering, production, material control, quality, integration, and database roles before implementation. Known gaps: Sequence type/uniqueness scope; branching/alternate routing; repeated same operation in one routing; step-level work center assignment; operation-specific quantities/time units; whether status is needed; historical routing snapshot behavior.

## S. Missing Collection Assessment

No collection is added. Repeated routing steps or BOM components are separate records in the inventory-defined relationship collections. Any further missing owner requires inventory change control; no arrays are introduced.

## T. Cross-Collection Consistency Check

- Inventory identity/module/type: checked against Collection Inventory v1.6.
- Flat documents and singular reference keys: PASS for defined fields.
- Required ID/display pairs and audit pairs: PASS for defined references.
- Reusable Operation vs routing-specific operation step: consistent with DEC-003.
- Product vs Material distinction in BOM header: consistent with DEC-022.
- Effectivity, values, uniqueness and external rules: OPEN where stated.

## U. Field Completeness Metrics

Field, requiredness, reference-pair, type, and index counts are listed in Sections E, F, and J. Detailed requirements are unavailable, so no functional scenario coverage percentage is claimed. Research sources are listed below. Unresolved gaps: Sequence type/uniqueness scope; branching/alternate routing; repeated same operation in one routing; step-level work center assignment; operation-specific quantities/time units; whether status is needed; historical routing snapshot behavior.

## V. One Representative MongoDB Document

The sole illustrative flat structure is in Section L. No extra sample document or 10-document sample set is generated.

## W. Assumptions

- Inventory and approved ownership decisions bound scope.
- Repeated steps/components use separate flat records under DATA-001.
- ID/name duplication is intentional strategic denormalization.
- APPROVED follows the Project Owner's prior instruction; open items remain unresolved.

## X. Open Issues

Sequence type/uniqueness scope; branching/alternate routing; repeated same operation in one routing; step-level work center assignment; operation-specific quantities/time units; whether status is needed; historical routing snapshot behavior.

## Y. Research Sources

Collection-specific research sources and the design question each informs are recorded in the Research basis section below. Vendor examples are functional evidence only, not project schema authority.

## Z. Final Design Review

- **Status:** APPROVED under Project Owner instruction; not production readiness.
- **Covered:** inventory-bounded purpose, field baseline, flat structure, references, research, and dependency constraints.
- **Remaining:** Section X and all unresolved revision, effectivity, code, and lifecycle rules.
- **Readiness:** reviewable baseline; implementation must wait on any blocking semantic issue.
## Research basis

- [SAP ME Routing](https://help.sap.com/docs/SAP_MANUFACTURING_EXECUTION/e1adc70af32241619335c8768a892edb/9cf01debaa804b4c9f59c6d5f9c8a5d0.html) — Routing consists of individual operation steps with ordering and flow; used for relationship-record behavior.
- [SAP Digital Manufacturing Routing Integration](https://help.sap.com/docs/sap-digital-manufacturing/integration-guide/routing-integration?ai=true) — Defines routing operations and sequence and explains mapping of operations/sub-operations.

## Readiness note

Specification status is **APPROVED** under prior Project Owner instruction. Remaining OPEN issues are not approved decisions and do not establish production readiness.


