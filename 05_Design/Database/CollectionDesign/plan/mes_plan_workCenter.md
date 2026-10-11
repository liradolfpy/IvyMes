**FILE TYPE: PROJECT SPECIFIC**

# Collection Specification — `mes_plan_workCenter`

**Specification status:** CURRENT — updated under GEN-001 on 2026-10-11
**Design scope:** Current collection design baseline. Open items remain unresolved.  
**Version:** 1.0  
**Module:** Planning & Scheduling (`plan`)  
**Collection type:** Master  
**Inventory authority:** `05_Design/Database/Collection_Inventory.md` (CURRENT)

## A. Collection overview

`mes_plan_workCenter` is the MES master identity for an organizational/work execution area used to scope capacity and production references. It references one owning plant and may reference one parent work center to represent a scalar hierarchy edge. It does not embed resources, machines, capacity calendars, operators, or assignments.

**Business definition:** A work center is a named manufacturing area or logical resource grouping at which operations can be planned, dispatched, or reported. A physical machine/resource remains a distinct entity; project ownership of resource identity is an open cross-module issue.

## B. Industry research summary

| Source | Documented capability / standard | Design implication |
|---|---|---|
| ISA, *ISA-95 Series of Standards* ([source](https://www.isa.org/standards-and-publications/isa-standards/isa-95-standard); includes ANSI/ISA-95.00.01-2025) | Defines manufacturing operations and equipment/resource models and technology-neutral exchanges; organizational and equipment levels support planning/execution context. | Model a stable work-center identity and explicit plant scope; avoid embedding lower-level equipment and scheduling details. |
| SAP Digital Manufacturing, *Feature Scope Description 2605* ([source](https://help.sap.com/doc/13c9f83611f94a5ab2c94f23cacfc217/latest/en-US/SAP_DMC_FSD_enUS.pdf)) | Public feature documentation describes work centers, hierarchy down to resource level, resource assignment, and shift assignment. | Use one flat work-center record, parent link for hierarchy where needed, and keep resources and shifts in their own collections. |
| SAP Digital Manufacturing, *Order POD* ([source](https://help.sap.com/docs/sap-digital-manufacturing/execution/order-pod)) | Execution work lists are scoped by work center/resource and planned date; users may be assigned to relevant work centers. | Work center is a frequently queried execution scope, supporting plant+code and plant+name access paths. |

**Standards:** ISA-95 is relevant to resource/work-center and operations context. ISA-88 is not assumed for discrete work-center hierarchy. ISO 22400 can inform later KPI rollups but does not prescribe this master schema. This design makes no compliance claim.

**Research limitation:** Public documentation establishes capabilities, not vendor database layouts. The project has no current resource-master owner, hierarchy policy, or detailed work-center requirements. Project Context now records general MES settings; these detailed work-center rules remain open.

## C. Manufacturing scenarios and lifecycle

Covered scenarios: facility-scoped work-center search; a cell/area hierarchy; assignment of operations and schedules to a center; shared work center referenced by many transactions; center rename or retirement; multiple plants with duplicate local codes; unavailable equipment distinct from work-center identity.

Creation event is master-data registration. Updates change descriptive or hierarchy values. Historical transactions retain scalar reference snapshots. Whether a work center may be moved across plants, whether parent-child cycles are prohibited, and exact lifecycle transitions remain OPEN until defined and documented.

## D. Functional coverage matrix

| Persona / need | Coverage | Boundary |
|---|---|---|
| Planner | Find center in plant; use it as planning/capacity scope. | Capacity and calendars are separate collections. |
| Operator / supervisor | Resolve center identity in dispatch/execution. | Live equipment state belongs to asset/performance events. |
| Quality engineer | Identify operation context by center. | Inspection plan/result fields belong to QA. |
| Maintenance engineer | Identify the center associated with equipment. | Asset-to-center assignment ownership remains open. |
| Integration engineer | Stable MES code and optional hierarchy reference. | External-system identifiers not included until contracted. |

## E. Complete field dictionary

| Field | BSON type | Requiredness | Meaning / validation |
|---|---|---|---|
| `_id` | String | Required | Immutable custom 16-digit MES ID. |
| `workCenterCode` | String | Required | Human-facing center code. Nonblank; pattern and uniqueness scope open. |
| `workCenterName` | String | Required | Center display name. |
| `description` | String | Optional | Plain-text purpose/area description. |
| `plantId` | String | Required | MES `_id` of owning plant. |
| `plantName` | String | Required | Plant display snapshot paired with `plantId`. |
| `parentWorkCenterId` | String | Optional | One parent center reference for hierarchy; omit together with name if root. |
| `parentWorkCenterName` | String | Optional | Parent name paired with parent ID. |
| `status` | String | Required | Work-center lifecycle/availability state; vocabulary and semantics open. |
| `createdAt` | BSON Date | Required | UTC creation timestamp. |
| `createdById` | String | Required | Creator MES ID. |
| `createdBy` | String | Required | Creator display name. |
| `updatedAt` | BSON Date | Optional | Update time; requires full update audit group. |
| `updatedById` | String | Optional | Updater MES ID. |
| `updatedBy` | String | Optional | Updater display name. |

## F. Field groups, references, and identifiers

Identity: code/name/description. Scope: required `plantId` + `plantName`; optional self-reference `parentWorkCenterId` + `parentWorkCenterName`. Lifecycle: `status`. Audit: standard groups.

Both references use 16-digit MES system IDs. Parent must belong to the same plant unless a current cross-plant hierarchy rule allows otherwise. Parent references must not create cycles; enforcement and hierarchy depth are open. Work-center code is a business key, not `_id`; no current numbering pattern or uniqueness scope exists outside `prod`.

## G. Data types and requiredness

String and BSON Date only. Required: `_id`, code/name, plant reference pair, status, creation audit. Optional: description, parent reference pair, update audit group. Parent ID and name must be both present or both absent.

## H. Validation rules

1. `_id` is unique immutable 16-digit string.
2. `plantId` resolves to an existing plant; new scheduling/execution references require an eligible plant.
3. `parentWorkCenterId` resolves to a different existing work center in the same plant (proposed default; rule remains OPEN); reject cycles.
4. Parent ID/name and audit ID/display pairs are atomic.
5. Codes/names are trimmed nonempty strings.
6. Lifecycle and eligibility checks follow the current work-center vocabulary once established.

## I. Status values and transitions

`status` is required and denotes whether the center is eligible for new planning/dispatch and whether it remains a valid historical reference. Allowed values, definitions, transition graph, and effect on in-progress schedules are **OPEN**. No values are silently established.

## J. Relationships

Outgoing: required plant; optional parent work center. Incoming: routing operations, dispatch assignments, execution, schedules, resource capacity, performance observations, maintenance, and other inventory consumers. Resource/machine ownership is not resolved; do not assume `mes_maint_asset` is the sole owner of every production resource.

## K. External-system integration

ERP work-center identifiers and MES resource mappings may be needed, but the project does not identify an ERP/CMMS system or master-data synchronization direction. No external key is included pending a defined mapping contract.

## L. Recommended indexes

| Index | Unique? | Query supported | Rationale / cost |
|---|---:|---|---|
| `{ _id: 1 }` | Yes | Direct reference lookup | Mandatory system identity. |
| `{ plantId: 1, workCenterCode: 1 }` | Proposed unique; scope definition OPEN | Plant-scoped code lookup and duplicate validation | Natural likely scope, but confirm whether code is globally unique or plant-local before enforcing. |
| `{ plantId: 1, status: 1, workCenterName: 1 }` | No | Eligible center selection by plant/name | Supports planning and UI filters; status semantics must be defined and documented. |
| `{ parentWorkCenterId: 1 }` | No | Enumerate children | Useful for hierarchy traversal; additional write cost is modest. |

## M. Audit and history

Standard creation audit is required; update triplet optional as a group. No history collection is defined. Parent/plant changes and name snapshots need a controlled historical policy; audit timestamps alone do not retain old values.

## N. Exceptions and missing-field assessment

| Gap | Scenario / reason | Target | Priority | Evidence / disposition |
|---|---|---|---|---|
| Resource/machine assignment relationship | Work center contains multiple resources and may share resources per product/process | Inventory owner undecided | High | SAP feature scope documents a work-center hierarchy to resource level; keep assignment outside this record. Confirm separate relationship collection/owner. |
| Capacity/shift calendar | Shift-specific available capacity | `mes_plan_resourceCapacity` / calendar and assignment collections | High | Inventory already separates capacity/calendar concepts; detailed links remain to be specified. |
| Local vs global work-center code uniqueness | Identical codes across plants may be valid | This collection | High | Project uniqueness convention requires a defined per-collection scope; none defined. |
| Work-center type/department | Different center capabilities | Governance/configuration TBD | Medium | No current taxonomy or requirement; avoid speculative enum. |

## O. Missing-collection assessment

No collection added. Existing inventory includes `mes_plan_resourceCapacity`; resource assignment may need a distinct relationship, but first resolve whether `mes_maint_asset`, production resource, or another owner represents equipment/resources. Submit any required new collection through inventory change control.

## P. Cross-collection consistency check

Singular keys `plantId`/`plantName`, `parentWorkCenterId`/`parentWorkCenterName`; audit pairs are exact. All IDs are 16-digit strings. Every other collection referencing work center must use these field stems and a scalar snapshot, never arrays.

## Q. Completeness metrics

| Metric | Result |
|---|---:|
| Proposed fields | 15 |
| Required / optional / conditional fields | 9 / 6 / 0 |
| Business ID–Name pairs | 1 required, 1 optional; audit pairs standard |
| Status/control fields | 1 |
| Audit fields | 6 maximum |
| External integration fields | 0 |
| Applicable scenario families evaluated / covered | 8 / 6 (75%) |
| Unresolved functional gaps | 4 |
| Research-backed field groups | plant/work-center/resource hierarchy and context |
| Project-specific inferred field groups | code, parent pointer, lifecycle placeholder, audit |

Scenario metric is indicative only; unresolved resource assignments and code uniqueness are excluded from claimed coverage.

## R. Representative MongoDB document

```javascript
{
  "_id": "2000293847561029",
  "workCenterCode": "BLR-MACH-01",
  "workCenterName": "Cylinder Head Machining Cell",
  "description": "CNC machining and in-process transfer area",
  "plantId": "1000293847561029",
  "plantName": "Bengaluru Vehicle Systems Plant",
  "status": "<value OPEN — value not defined>",
  "createdAt": ISODate("2026-10-08T09:15:00.000+0000"),
  "createdById": "5566778899001122",
  "createdBy": "MES Master Data Service"
}
```

## S. Assumptions

- Inventory assigns the work-center collection to Planning & Scheduling.
- Each work center has one plant owner and at most one parent in the proposed hierarchy representation.
- Hierarchy is represented by a scalar self-reference, not an embedded tree.
- Resources, shifts, and capacity remain separately modeled.

## T. Open issues

1. Resolve work-center/resource/machine entity ownership across `plan`, `maint`, and `prod`.
2. Confirm code uniqueness scope and pattern.
3. Define and document status vocabulary, availability semantics, and transition effects.
4. Confirm parent hierarchy needs, cycle handling, and cross-plant rules.
5. Define assignment/effectivity and master rename history behavior.
6. Determine external system identifiers and integration direction.

## U. Final design review

**Status:** CURRENT under GEN-001, updated 2026-10-11. Flat structure and reference pairs conform to conventions. Readiness for implementation depends on open ownership, uniqueness, and lifecycle decisions; current schema status does not close them.

**Research sources:** [ISA-95 series](https://www.isa.org/standards-and-publications/isa-standards/isa-95-standard); [SAP DMC Feature Scope 2605](https://help.sap.com/doc/13c9f83611f94a5ab2c94f23cacfc217/latest/en-US/SAP_DMC_FSD_enUS.pdf); [SAP Order POD](https://help.sap.com/docs/sap-digital-manufacturing/execution/order-pod).


Documentation update — 2026-10-11: Apply GEN-001 / DEC-018; remove separate documentation sign-off gates, retain open requirements and operational controls, and use current artifact status. See CHG-022.
