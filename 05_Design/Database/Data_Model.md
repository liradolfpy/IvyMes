# MES Data Model — Production Execution Order and Operation Concepts

**Document ID:** MES-DATA-MOD-PROD-001  
**Version:** 0.8
**Status:** CURRENT
**Date:** 2026-09-28  
**Scope:** Production-order/work-order planning and execution boundary, reusable operation identity, routing-specific operation steps, and controlled Production Execution references.

## 1. Authority and scope

This document records the current Production Order / Work Order distinction from `00_Project_Control/Decisions_Log.md` (DEC-004), together with the logical relationship between the current `mes_prod_operation` master and the `mes_prod_routingOperation` relationship collection (DEC-003). The Production Execution inventory is in `05_Design/Database/Collection_Inventory.md`.

This is not a complete MES-wide data model. Other entities and collection specifications require consistency review and documentation under GEN-001; no separate sign-off is required. No sample documents are included.

## 2. Modeling rules

- MongoDB is the current database platform.
- All documents are flat scalar records under DATA-001. No embedded documents or arrays are permitted.
- A reusable operation is represented once in `mes_prod_operation`.
- Each occurrence of an operation in a routing is represented by one separate `mes_prod_routingOperation` record. The routing step is not a duplicate operation master.
- References use the MES system ID and the matching flat display name. `operationId` references `_id` in `mes_prod_operation`; `operationName` is its denormalized display snapshot.
- `routingOperationId` references `_id` in `mes_prod_routingOperation`; `routingOperationName` identifies that routing-specific step. It is distinct from `operationName`.

## 3. Logical entities

| Entity | Owning collection | Meaning | Key attributes at model level |
|---|---|---|---|
| Operation | `mes_prod_operation` | Reusable definition of an operation independent of any routing. | System ID, operation code, operation name, description, lifecycle status. |
| Routing | `mes_prod_routing` | Revisioned sequence definition applicable to production context. | System ID, routing number/name, product reference, revision, effectivity, status. |
| Routing Operation | `mes_prod_routingOperation` | One occurrence/step for one operation within one routing. | System ID, routing reference, operation reference, sequence number, routing-step display name, optional work-center and cycle-time attributes. |
| Production Order | `mes_prod_productionOrder` | Macro-level manufacturing authorization and batch-progress identity, usually created by ERP and passed to MES. | System ID, business number/name, product and plant, authorized quantity/UOM, required date, overall status. |
| Work Order | `mes_prod_workOrder` | MES-managed granular execution task/step derived from a Production Order. | System ID, business number, parent production-order reference, product/plant, execution quantity, routing/resource references, execution status. |
| Dispatch Assignment | `mes_prod_dispatchAssignment` | Assignment of a Work Order or routing operation to a shop-floor resource and time window. | System ID, work-order reference, optional routing-operation reference, resource references, schedule, assignment status. |
| Work Instruction | `mes_prod_workInstruction` | Revision-controlled instruction definition applicable to a product or operation context, referencing one registered content version. | System ID, business number/name, revision, applicability references, required instruction-content ID/name pair, approval/effectivity. |
| Instruction Content Reference | `mes_prod_instructionContent` | Registry metadata for one exact externally managed instruction content version; payload remains outside MongoDB. | System ID, content name, required opaque external locator, lifecycle status. |
| Scrap Reason | `mes_prod_scrapReason` | Controlled reusable scrap classification referenced by Scrap Records. | System ID, reason code/name, description, availability status. |
| Andon Case | `mes_prod_andonCase` | One Andon condition with current state and affected resource/production context. | System ID, case number/name, work-center/context references, current status, activation/resolution times. |
| Andon Event | `mes_prod_andonEvent` | One lifecycle transition belonging to an Andon Case. | System ID, event number, case reference, per-case sequence, event type/time, actor and optional recipient references. |

The attribute lists are logical summaries. The current collection specifications define the current field dictionaries.

## 3.1 Production order and work order boundary

Under current `DEC-004` and the supplied project requirement, Production Order represents the macro-level authorization: what product or subassembly, how much, and by when, with overall batch progress. Work Order represents the MES-level execution breakdown: how and where operations are carried out, with shop-floor execution tracking. A Production Order may have multiple Work Orders for tasks or stages across resource or departments.

The flat model stores the parent Production Order as one flat document and each Work Order as its own flat document. Each Work Order carries `productionOrderId` + `productionOrderName` pointing to the parent. The parent does not embed a Work Order array. Dispatch and operation execution attach to the Work Order execution scope. Sales-order/demand-forecast linkage, exact decomposition cardinality, and quantity reconciliation are not specified and remain open.

## 3.2 Andon case and event boundary

The current Andon model uses one `mes_prod_andonCase` record as the stable identity and current-state record for a condition. Every activation, escalation, acknowledgement, resolution, or cancellation is a separate `mes_prod_andonEvent` record with `andonCaseId` + `andonCaseName` and an `eventSequenceNo`. A case may therefore be queried for its current status, while its related events provide the ordered transition history. No event array is stored on the case. The case `status` represents its current lifecycle and uses its current collection-specific allowed values; event `eventType` classifies each recorded transition using the current vocabulary and meanings in `DEC-012`. The event collection has no separate `status` because no independent event-record lifecycle is currently defined. Permitted transitions, reopen behavior, recipient cardinality, event sequence allocation, and event/case update consistency remain open under `DEC-005`.

## 3.3 Work-instruction content registry

Under current `DEC-009`, each `mes_prod_workInstruction` references one record in `mes_prod_instructionContent` using `instructionContentId` + `instructionContentName`. The registry record represents one exact content version and holds its required opaque `instructionContentLocator`. The content payload remains external; MES does not prescribe a provider, URI scheme, or locator syntax. A content reference must be active when assigned to a new Work Instruction revision, and retired references remain available for historical resolution.

## 3.4 Scrap-reason reference

Under current `DEC-008`, `mes_prod_scrapReason` owns controlled reason codes and display names. Every `mes_prod_scrapRecord` requires `scrapReasonId` + `scrapReasonName` to identify one reason. Active reasons may be selected for new Scrap Records; retired reasons remain resolvable for historical records.

## 4. Repeated-item representation

A routing does not contain an embedded list of operations. For a routing with multiple steps, there is one routing record and one routing-operation document per step. Each routing-operation document contains one `routingId`/`routingName` pair and one `operationId`/`operationName` pair.

`sequenceNo` defines the step order within the routing. The proposed uniqueness rule is one `sequenceNo` per `routingId`; its unique compound index must be confirmed in the routing-operation specification.

Other collections may refer to the reusable operation directly when the business meaning is operation-wide, or to a routing operation when the meaning is specific to a routing occurrence. A collection specification must state which concept it references.

## 5. Query and index considerations

- Find a routing's steps by `routingId`, ordered by `sequenceNo`; the proposed index is `{ routingId: 1, sequenceNo: 1 }` with uniqueness OPEN until its scope is defined.
- Find routings that use an operation by `operationId`; a non-unique index on `operationId` is recommended if this is a documented access pattern.
- Resolve an operation reference by `operationId` to `mes_prod_operation._id` and verify the stored display snapshot against the current name-synchronization rule.
- Related records may be retrieved through separate queries or aggregation. Their collection designs must specify query patterns and indexes; no backend implementation or mandatory `$lookup` strategy is selected here.

## 6. Consistency and lifecycle

- Every routing-operation record must reference one existing operation and one existing routing.
- The referenced operation must be valid for creation of a new routing step. Whether retired operations remain valid for historical routing steps requires a lifecycle decision.
- `operationName` in a routing-operation record is intentionally copied from the operation master. Whether it remains a historical snapshot or is synchronized after renaming is still an open decision in `DocumentIdAndReferenceConvention.md`.
- If a work instruction directly references both `operationId` and `routingOperationId`, the routing-operation record's operation must match `operationId`.
- New Work Instruction revisions reference an active `mes_prod_instructionContent` record and preserve the exact content-version reference. Provider-specific locator resolution remains an integration concern.
- New Scrap Records reference an active `mes_prod_scrapReason` record; retired reasons remain resolvable for history.
- No cross-document transaction behavior is prescribed by this logical model; consistency and concurrency requirements must be specified where relevant.

## 7. Open issues

1. Confirm `operationCode` uniqueness scope and index requirement.
2. Confirm operation retirement behavior for existing routings and new routing assignments.
3. Confirm the `routingOperationName` display rule (for example, whether it is the operation name alone or includes routing sequence context).
4. Confirm whether product/operation applicability in work instructions should allow both a direct `operationId` and a routing-specific `routingOperationId`.
5. Confirm the unique index and applicable status/effectivity rules for routing sequence steps.

## 8. Revision history

| Version | Date | Change | Approval |
|---|---|---|---|
| 0.1 | 2026-09-28 | Initial logical model for reusable operations and routing-specific steps, aligned with DEC-003. | APPROVED |
| 0.2 | 2026-09-28 | Added the Production Order planning/batch scope and Work Order execution scope under DEC-004; documented separate flat child records and unresolved upstream/reconciliation details. | APPROVED |
| 0.3 | 2026-09-28 | Added APPROVED Andon Case and Andon Event logical entities with a flat case-reference relationship under pending DEC-005. | APPROVED |
| 0.4 | 2026-09-28 | Classified Work Instruction content as an external scalar locator under approved DEC-007; owner and format remain open. | APPROVED |

| 0.5 | 2026-09-28 | Clarified collection-specific status semantics and the Andon case lifecycle status versus event transition type; no shared status vocabulary imposed. | APPROVED |



| 0.6 | 2026-10-07 | Added approved Scrap Reason and Instruction Content Reference entities; updated required ID/name relationships and superseded DEC-007 via DEC-008/DEC-009. | APPROVED |
| 0.7 | 2026-10-07 | Recorded the approved Andon event type vocabulary and meanings under DEC-012; retained unresolved transition and sequencing rules. | APPROVED |
| 0.8 | 2026-10-08 | Align scalar reference-key terminology with singular entity stems under DATA-003 / DEC-015. | APPROVED |





Documentation update — 2026-10-11: Apply GEN-001 / DEC-018; remove separate documentation sign-off gates, retain open requirements and operational controls, and use current artifact status. See CHG-022.
