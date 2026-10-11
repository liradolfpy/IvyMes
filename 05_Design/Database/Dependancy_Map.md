**FILE TYPE: PROJECT SPECIFIC**

# MES MongoDB Collection Dependency Map

**Document ID:** MES-DATA-DEP-001  
**Version:** 1.0  
**Status:** CURRENT  
**Date:** 2026-10-11  
**Inventory baseline:** `05_Design/Database/Collection_Inventory.md` v1.5 (119 collections)  
**Purpose:** Dependency-aware sequencing aid for collection specification work across all ten MES modules.

## 1. Authority and use

The Collection Inventory remains authoritative for the set, names, module ownership, and scope of collections. Current project instructions, requirements, decisions, and applicable collection-specific requirements remain authoritative for their respective topics. This map organizes the 119 inventory entries into proposed dependency waves to make design work manageable.

The waves are an ordering aid, not a schema, approval, new collection decision, or permission to infer fields. A collection listed in an earlier wave is not thereby approved or complete. A later-wave collection may be designed only when its actual dependencies and open questions are understood. Where the inventory or an approved decision does not establish ownership or relationship direction, retain the issue as open and seek an explicit project decision; do not resolve it by this map.

Before designing a collection, consult this map, its inventory entry, applicable requirements and decisions, and relevant existing specifications. Verify direct dependencies against current source artifacts; revise this map when those sources change.

## 2. Sequencing principles

- Establish identities and controlled reference vocabularies before dependent records.
- Establish organizational context and reusable definitions before schedules and operational transactions.
- Design order and schedule concepts before dispatch and execution records.
- Design operational anchors before transaction details and derived reports.
- Keep repeated concepts as separate flat records under DATA-001 / DEC-003; define parent references, query patterns, indexes, ordering, and consistency behavior in the relevant specifications.
- Treat each wave as a suggested readiness grouping, not as a requirement that every listed collection be designed simultaneously.
- Do not treat cross-collection references as enforced database foreign keys unless a project decision explicitly requires that behavior.

## 3. Proposed dependency waves

### Wave 0 — Foundational identities and reference vocabularies (12)

- `mes_plan_plant`
- `mes_matl_unitOfMeasure`
- `mes_matl_product`
- `mes_matl_material`
- `mes_man_employee`
- `mes_man_skill`
- `mes_prod_operation`
- `mes_prod_scrapReason`
- `mes_prod_instructionContent`
- `mes_perf_downtimeClassification`
- `mes_maint_failureCode`
- `mes_tool_toolClass`

### Wave 1 — Organizational context and shared operating masters (8)

- `mes_plan_workCenter`
- `mes_plan_productionCalendar`
- `mes_plan_integrationExchange`
- `mes_maint_asset`
- `mes_man_operator`
- `mes_man_shift`
- `mes_matl_wipLocation`
- `mes_tool_toolLocation`

### Wave 2 — Reusable definitions, rules, and relationship records (25)

- `mes_prod_routing`
- `mes_prod_routingOperation`
- `mes_prod_workInstruction`
- `mes_matl_billOfMaterial`
- `mes_matl_bomComponent`
- `mes_matl_materialSubstitutionRule`
- `mes_qa_specification`
- `mes_qa_samplingPlan`
- `mes_qa_inspectionPlan`
- `mes_maint_checklistDefinition`
- `mes_maint_maintenancePlan`
- `mes_man_crew`
- `mes_man_crewMembership`
- `mes_man_operatorSkill`
- `mes_man_certification`
- `mes_man_trainingRequirement`
- `mes_tool_tool`
- `mes_tool_toolCompatibilityRule`
- `mes_plan_resourceCapacity`
- `mes_plan_changeoverRule`
- `mes_perf_performanceTarget`
- `mes_trace_labelDefinition`
- `mes_ai_kpiDefinition`
- `mes_ai_dashboardDefinition`
- `mes_ai_dashboardRoleAccess`

### Wave 3 — Demand, orders, and schedule records (6)

- `mes_plan_productionPlan`
- `mes_prod_productionOrder`
- `mes_prod_workOrder`
- `mes_plan_schedule`
- `mes_plan_scheduleSimulation`
- `mes_plan_schedulePublication`

### Wave 4 — Operational anchors and core events (16)

- `mes_trace_lot`
- `mes_trace_serialNumber`
- `mes_matl_inventoryBalance`
- `mes_man_laborAssignment`
- `mes_tool_toolReservation`
- `mes_prod_dispatchAssignment`
- `mes_prod_operationExecution`
- `mes_prod_downtimeEvent`
- `mes_prod_shiftHandover`
- `mes_prod_andonCase`
- `mes_prod_andonEvent`
- `mes_perf_machineStateEvent`
- `mes_perf_cycleTimeObservation`
- `mes_perf_productionLossEvent`
- `mes_perf_energyObservation`
- `mes_maint_conditionObservation`

### Wave 5 — Cross-domain transactions and transaction details (37)

- `mes_prod_productionReport`
- `mes_prod_reworkRecord`
- `mes_prod_scrapRecord`
- `mes_matl_materialReservation`
- `mes_matl_materialMovement`
- `mes_matl_materialConsumption`
- `mes_matl_wipMovement`
- `mes_matl_kanbanSignal`
- `mes_matl_inventoryHold`
- `mes_matl_finishedGoodsDeclaration`
- `mes_trace_genealogyLink`
- `mes_trace_asBuiltRecord`
- `mes_trace_labelPrintEvent`
- `mes_trace_scanEvent`
- `mes_trace_recallCase`
- `mes_trace_conformanceCertificate`
- `mes_qa_inspectionRequest`
- `mes_qa_inspectionResult`
- `mes_qa_nonconformance`
- `mes_qa_correctiveAction`
- `mes_qa_deviationWaiver`
- `mes_qa_qualityHold`
- `mes_qa_spcObservation`
- `mes_qa_calibrationVerification`
- `mes_maint_maintenanceWorkOrder`
- `mes_maint_maintenanceSchedule`
- `mes_maint_checklistResult`
- `mes_man_trainingRecord`
- `mes_man_laborTimeEntry`
- `mes_man_attendanceRecord`
- `mes_tool_toolMovement`
- `mes_tool_toolLifeRecord`
- `mes_tool_toolUsageRecord`
- `mes_tool_toolCalibrationRecord`
- `mes_tool_toolMaintenanceOrder`
- `mes_tool_toolIncident`
- `mes_tool_toolCostRecord`

### Wave 6 — Derived performance and analytics outputs (15)

- `mes_perf_oeeResult`
- `mes_perf_shiftPerformanceSummary`
- `mes_maint_assetHealthSummary`
- `mes_ai_kpiResult`
- `mes_ai_qualityAnalysisResult`
- `mes_ai_traceabilityReport`
- `mes_ai_downtimeAnalysisResult`
- `mes_ai_anomalyResult`
- `mes_ai_maintenanceInsight`
- `mes_ai_productionForecast`
- `mes_ai_rootCauseCase`
- `mes_ai_operationalQuery`
- `mes_ai_operationalAlert`
- `mes_ai_recommendation`
- `mes_ai_dataExportJob`

## 4. Cross-module dependency flow

| Dependency flow | Design implication |
|---|---|
| `plan` plant / work-center context → planning, production, maintenance, quality, and performance consumers | `plan` owns Plant and Work Center identities under the project’s confirmed ownership decisions. Work Center is distinct and has no machine/resource association. |
| `matl` product, material, and unit of measure → production, BOM, inventory, quality, traceability, and analytics | Product is distinct from Material; product composition is represented through BOM concepts. `matl` owns the shared quantity-unit identity. |
| `man` employee → operator and workforce records → execution/labor consumers | Employee is the shared person identity; Operator is a workforce role/profile referencing Employee. |
| `maint` Asset and `tool` Tool → maintenance, execution context, and analytics | Tool remains a distinct business identity even when associated with an asset; do not merge tool and machine/asset identities. |
| `prod` operation and routing definitions → work orders, dispatch, operation execution, instruction, quality, and reporting | Reusable Operation is distinct from a Routing Operation step; routing-step occurrences are separate flat records. |
| Production Order → Work Order → dispatch / operation execution → production and domain transactions | Preserve macro authorization versus granular execution. Do not make dependent transactions substitutes for their parent concepts. |
| Traceability identity and operational/material/quality events → genealogy, recall, certificates, and traceability analytics | Determine relationship direction and endpoint typing; avoid requiring reciprocal references that create write-order cycles. |
| Operational source events → performance calculations → Analytics outputs | Derived records should identify their source basis and calculation/version context as applicable; do not duplicate operational event ownership. |

## 5. Dependencies and design questions that block or constrain sequencing

These are known open modeling questions from the current inventory/context, not decisions made by this map. Resolve them in their owning requirement, decision, or specification before the affected schemas are finalized.

1. **Inspection-plan / sampling-plan direction:** The inventory does not fully establish whether an Inspection Plan owns or references a Sampling Plan, or how sampling-plan definitions relate to inspection definitions. Clarify direction and avoid a circular required dependency.
2. **Maintenance plan / checklist definition direction:** Current inventory dependency descriptions can be read in both directions. Decide whether checklist definitions are reusable independently or owned by a maintenance plan, then document one-way ownership/reference semantics.
3. **Capacity target ownership:** There is no separate Resource collection in the inventory; Work Center is not a machine/resource. Define exactly what `resourceCapacity` measures and what identity it is keyed by without equating Work Center, Asset, machine, or Tool.
4. **Asset, location, and classification ownership:** Confirm whether any dedicated asset class or production-line identity is needed and identify the existing owner for location/classification concepts where the inventory does not name one. Do not invent collections or conflate asset/location types.
5. **Repeated inspection and checklist items:** The flat-document convention requires repeated characteristics/checklist items to be represented as separate records. Identify the owning collection and parent-reference strategy from the approved inventory and requirements; flag any inventory gap for change control rather than embedding arrays or silently adding a collection.
6. **Material Inventory Hold / Quality Hold relationship:** Clarify whether these are distinct holds with a one-way link, or independent domain records referring to a shared case. Avoid reciprocal mandatory references.
7. **Genealogy and event references:** Define the owner and direction for genealogy edges and links to lot/serial identities, production reports, and material movements. Do not require both sides to be created atomically absent an approved mechanism.
8. **Externally owned identities:** User/role, customer/supplier, external content provider/source, and some external system identities may be externally mastered. Define the external identity/reference convention and source ownership before treating them as MES collection dependencies.
9. **Tool-to-Asset relation:** Tool is distinct from Asset and may be associated with one. Define whether this is optional, cardinality, and ownership direction in the Tool/Asset specifications.
10. **Source-event and derived-event overlap:** Define reconciliation between `mes_prod_downtimeEvent` and `mes_perf_machineStateEvent`, and source/posting boundaries between material consumption, movement, and inventory balances to prevent duplicate counting.
11. **Analytics derivation:** KPI formulas, source windows, refresh/rebuild strategy, and definition/version references remain collection-specific decisions; dependency order alone does not define these semantics.
12. **Business identifiers and life cycles:** Collection-specific business-number scopes, ID allocation, status meanings, retention, and deletion behavior must follow their authoritative requirements/decisions and remain open where not defined.

## 6. Coverage and integrity check

The seven wave lists contain every one of the 119 inventory collection names exactly once. Collection names may be mentioned again in explanatory sections below; those mentions are not additional wave membership. The groups cover the inventory only and do not assert that all business dependencies are resolved or that all referenced entities are MES-owned collections. Recheck the count and exact names against the current inventory whenever it changes.

## 7. Change and maintenance rule

Update this map whenever a material inventory, ownership, dependency, or approved decision change alters design order. Update the prompt and inventory pointers in the same change. Record material artifact changes in `00_Project_Control/Change_Log.md`. Do not use this map to record new business decisions; record those in `Decisions_Log.md` first, then reflect their sequencing impact here.

## 8. Recommended next step

Treat the map as the dependency checklist for Stage 4. Resolve or explicitly carry forward the open questions relevant to a collection group, then design specifications in dependency order. Keep each collection's specification status distinct from its inventory inclusion status.

