# Collection Specification — `mes_plan_plant`

**Specification status:** CURRENT — updated under GEN-001 on 2026-10-11
**Design scope:** Current collection design baseline. Open items below remain unresolved and are not treated as decisions.  
**Version:** 1.0  
**Module:** Planning & Scheduling (`plan`)  
**Collection type:** Master  
**Inventory authority:** `05_Design/Database/Collection_Inventory.md` (CURRENT)

## A. Collection overview

`mes_plan_plant` owns the MES-facing identity and lifecycle of one production plant/site boundary. It enables planning and execution records to be scoped to a facility. It does not own enterprise, company, site hierarchy, addresses, or ERP plant mappings because those owners and boundaries are not established in the current inventory.

**Business definition:** A plant is an organizational manufacturing facility or site at which production operations are planned and coordinated. The record is a master identity, not a production transaction.

**Owning module:** Planning & Scheduling, as assigned by the current inventory. This ownership is an inventory assignment, not an enterprise-wide master-data governance decision.

## B. Research summary

| Source | Documented capability / standard | Design implication |
|---|---|---|
| ISA, *ISA-95 Series of Standards* ([source](https://www.isa.org/standards-and-publications/isa-standards/isa-95-standard); current series includes ANSI/ISA-95.00.01-2025) | ISA-95 describes enterprise-to-manufacturing integration, organizational/equipment hierarchy, and manufacturing operations information exchange independently of a particular software product. | Keep plant as a stable organizational scope referenced by operational records; do not encode a vendor-specific hierarchy or integration protocol. |
| SAP Digital Manufacturing, *Manage Plants* entry in service catalog ([source](https://help.sap.com/docs/sap-digital-manufacturing/service-catalog/f8fc591655ca437082772a3416a2e158.html)) | SAP documents plant records as maintained master configuration and scopes several execution settings to a plant. | Treat plant identity and display name as master data; plant-specific settings should remain in their own configuration or owning collection. |
| SAP Digital Manufacturing, *Feature Scope Description 2605* ([source](https://help.sap.com/doc/13c9f83611f94a5ab2c94f23cacfc217/latest/en-US/SAP_DMC_FSD_enUS.pdf)) | Plant, work-center, resource, shift, and user management are distinct capabilities with hierarchical and assignment relationships. | Plant is a scope anchor; avoid embedding work centers, shifts, users, or settings in this flat plant record. |

**Standards applicability:** ISA-95 is relevant as a technology-neutral organizational and operations integration model. ISA-88 is not directly applicable to a generic discrete-manufacturing plant master. ISO 9001/IATF 16949 do not prescribe this MongoDB entity schema. This specification does not claim compliance or certification.

**Research limitation:** Public product documentation describes user-facing capabilities, not proprietary storage schemas. No ManufNEXT-specific plant governance or integration contract was available. `00_Project_Control/Project_Context.md` is empty.

## C. Manufacturing scenarios and lifecycle

Applicable cases: multi-plant planning; work centers and assets scoped to a facility; plant rename; plant retirement while historical records remain; ERP-originated plant identity; concurrent updates to a master record. No plant-specific transactions are stored here.

Create when a plant is registered for MES use. Update descriptive fields under the project's controlled master-data process. A plant referenced by historical transactions must remain resolvable. The exact activation, retirement, deletion, change-approval, timezone, and external-ERP synchronization policies are open.

## D. Functional coverage matrix

| Need / persona | Coverage in this collection | Boundary |
|---|---|---|
| Planner | Identify the facility used as planning scope. | Calendars and capacity are separate collections. |
| Operator / supervisor | Resolve the plant name associated with work context. | No operator, work-center, or production state is embedded. |
| Quality / maintenance | Resolve plant scope for quality and asset references. | No quality or maintenance policy is owned here. |
| Integration engineer | Maintain a stable MES identity and human-readable code/name. | ERP mapping and source-system ownership are open. |
| Plant management | Distinguish facilities in reports. | No location/address or corporate hierarchy is defined. |

## E. Complete field dictionary

| Field | BSON type | Requiredness | Meaning / validation |
|---|---|---|---|
| `_id` | String | Required | Immutable custom 16-digit MES system ID; globally unique per project convention. |
| `plantCode` | String | Required | Human-facing plant code. Nonblank; exact syntax and uniqueness scope require confirmation. |
| `plantName` | String | Required | Current display name; nonblank. |
| `description` | String | Optional | Short plain-text description; no nested content. |
| `status` | String | Required | Plant lifecycle state. Allowed values and transitions are open; do not assume `Active`/`Inactive` until defined and documented. |
| `createdAt` | BSON Date | Required | Creation timestamp in UTC. |
| `createdById` | String | Required | Creator's MES system ID. |
| `createdBy` | String | Required | Creator display name paired with `createdById`. |
| `updatedAt` | BSON Date | Optional | Last update timestamp; include update audit triplet together. |
| `updatedById` | String | Optional | Updater's MES system ID; conditional with the other update fields. |
| `updatedBy` | String | Optional | Updater display name paired with `updatedById`. |

No arrays, child lists, embedded settings, or fields for undefined enterprise/site ownership are included.

## F. Field groups; references and identifiers

Identity: `_id`, `plantCode`, `plantName`, `description`. Lifecycle: `status`. Audit: standard creation and optional update triplets.

This collection has no outgoing business-entity reference. Incoming references use `plantId` + `plantName` and must point to this collection. Audit references follow the mandatory `createdById` + `createdBy` and `updatedById` + `updatedBy` convention.

`plantCode` is a business key, not `_id`. The current project convention leaves non-Production-Execution numbering patterns and uniqueness scopes to collection-specific rules; no pattern is invented here. External identifiers are omitted until the integration owner and source-system mapping are defined.

## G. Data types and requiredness

The collection uses BSON String, Date only; no fractional or integer measurement is present. Required: `_id`, `plantCode`, `plantName`, `status`, and creation audit fields. Optional: `description` and the complete update audit triplet. There are no conditional business references.

## H. Validation rules

1. `_id` must be a unique 16-digit string and immutable.
2. `plantCode` and `plantName` must be nonempty trimmed strings.
3. Audit update fields must be all present or all absent; every ID has its paired display value.
4. `status` must be checked against the defined plant vocabulary once defined.
5. Do not physically delete a referenced plant absent a current retention/deletion rule.
6. Do not accept references to an unknown or retired plant for new work; the exact policy remains open.

## I. Status values and transitions

`status` is required and denotes plant master lifecycle/availability for new MES transactions. Its allowed values, definitions, transition graph, and effect on existing references are **OPEN**. No values are established by this specification.

## J. Relationships

Incoming references are expected from work centers and many module transactions/configurations. Inventory-level consumers include production orders/work orders and resource-scoped planning/execution. This collection references no other business collection. The plant/site/enterprise boundary and ownership need cross-module confirmation.

## K. External systems

ERP plant codes are common integration concepts, but no ERP product or mapping contract is current for this project. `plantCode` is the MES business key only; it must not be silently treated as an ERP identifier. Add an external mapping only after the integration contract and owner are defined.

## L. Recommended indexes

| Index | Unique? | Query supported | Rationale / cost |
|---|---:|---|---|
| `{ _id: 1 }` | Yes (MongoDB mandatory) | Direct reference lookup | System identity. |
| `{ plantCode: 1 }` | Proposed unique, rule definition OPEN | Search by code / duplicate detection | Appropriate for master lookup; cannot finalize until scope and source-of-truth are confirmed. |
| `{ plantName: 1 }` | No | Name search and administration | Useful for low-volume master-data search; write cost is small, collation/search semantics remain to be specified. |
| `{ status: 1, plantName: 1 }` | No | Select eligible plants after lifecycle definition | Optional UI filter; defer if no query requires it. |

Do not add every proposed index automatically. Confirm access patterns and uniqueness before deployment.

## M. Audit and history

Required standard creation audit fields; optional update group. There is no separate history collection in the current inventory. Name changes and lifecycle history retention are unresolved; audit-field presence alone does not provide field-level history.

## N. Exceptions and missing-field assessment

| Candidate | Scenario / why | Target | Priority | Evidence / disposition |
|---|---|---|---|---|
| Parent enterprise/company/site reference | Multi-entity organization hierarchy | No current owner | High | ISA-95 supports hierarchy concepts, but project ownership and required hierarchy levels are not decided; do not add a speculative key. |
| Time zone / locale | Local schedules and timestamps | Plant or calendar configuration TBD | Medium | Needed by some multi-region deployments; no project timezone rule or owning collection confirmed. |
| ERP plant mapping | Inbound planning and reconciliation | Integration/mapping owner TBD | High | SAP documentation demonstrates plant-scoped execution; project external-system contract is absent. |
| Address/contact details | Facility identification | Out of current inventory scope | Low | No functional requirement supplied. |

## O. Missing-collection assessment

No new collection is created. Enterprise/company/site hierarchy, external-identifier mapping, and master-data change history may require separate owners if project requirements demand them. Record those as inventory/change-control proposals before adding collections.

## P. Cross-collection consistency check

Uses singular `plantId` + `plantName`; 16-digit string references; standard audit pairs; flat scalar fields. References must use the MES plant `_id`, not `plantCode`. Work-center and transactional specifications must preserve this exact pair.

## Q. Completeness metrics

| Metric | Result |
|---|---:|
| Proposed fields | 11 |
| Required / optional / conditional fields | 7 / 4 / 0 |
| ID–Name business reference pairs | 0 outgoing; 1 standard audit pair for creation and optional update pair |
| Status/control fields | 1 |
| Audit fields | 6 maximum |
| External integration fields | 0 |
| Applicable scenario families evaluated / covered | 7 / 6 (85.7%) |
| Unresolved functional gaps | 4 |
| Research-backed field groups | plant identity, scope, lifecycle |
| Project-specific inferred field groups | code/name, audit, status placeholder |

Metric is a design review estimate, not proof of completeness. The one uncovered scenario is verified external-ERP mapping because its owner/contract is not defined.

## R. Representative MongoDB document

```javascript
{
  "_id": "1000293847561029",
  "plantCode": "PLT-BLR-01",
  "plantName": "Bengaluru Vehicle Systems Plant",
  "description": "Automotive component manufacturing facility",
  "status": "<value OPEN — value not defined>",
  "createdAt": ISODate("2026-10-08T09:00:00.000+0000"),
  "createdById": "5566778899001122",
  "createdBy": "MES Master Data Service"
}
```

Illustrative structure only; the status placeholder is intentionally not a valid production value and must be replaced after lifecycle definition.

## S. Assumptions

- Current inventory assigns ownership to Planning & Scheduling.
- One record represents one plant/site boundary.
- MES `_id` is the reference identity; code/name are display/business values.
- No specific cloud, ERP, HR, or identity-provider integration is implied.

## T. Open issues

1. Confirm whether plant means plant, site, or both, and define owning master-data authority.
2. Define and document plant-code pattern, uniqueness scope, external mapping, and duplicate handling.
3. Define and document lifecycle vocabulary and state-dependent reference rules.
4. Decide organization hierarchy, timezone, address, and plant settings ownership.
5. Define and document master-data rename propagation and deletion/retirement policy.

## U. Final design review

**Status:** CURRENT under GEN-001, updated 2026-10-11. Flat schema and inventory boundary are recorded. **Readiness limitation:** implementation constraints depending on the open items above must remain deferred; CURRENT does not mean every business rule or integration is settled.

**Research sources:** [ISA-95 series](https://www.isa.org/standards-and-publications/isa-standards/isa-95-standard); [SAP Digital Manufacturing service catalog](https://help.sap.com/docs/sap-digital-manufacturing/service-catalog/f8fc591655ca437082772a3416a2e158.html); [SAP DMC Feature Scope 2605](https://help.sap.com/doc/13c9f83611f94a5ab2c94f23cacfc217/latest/en-US/SAP_DMC_FSD_enUS.pdf).


Documentation update — 2026-10-11: Apply GEN-001 / DEC-018; remove separate documentation sign-off gates, retain open requirements and operational controls, and use current artifact status. See CHG-022.
