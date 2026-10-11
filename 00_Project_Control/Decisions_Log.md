DEC-001
Decision: MongoDB is the primary database platform for the MES architecture and collection/schema design.
Scope: Applies to database and collection design. Frontend, backend, cloud, operating system, and deployment technologies remain undecided.
Status: APPROVED
Approved By / Date: SG on 2026-09-27


Decision: 
    All MES collection documents must contain only top-level scalar fields; no embedded documents or arrays.
    Repeated items and relationships are represented as separate flat records.
    Cross-collection querying and aggregation are acceptable where needed, with indexes and query patterns addressed during collection design.
    No collection-specific exception to the flat-document rule is permitted.

Scope: Applies to database and collection design. 
Status: APPROVED
Approved By / Date: SG on 2026-09-27



DEC-002
Date: 2026-09-27
Decision: Approve DATA-001. All MES MongoDB collection documents shall be flat, with no embedded documents or arrays. Repeated items and relationships shall be represented as separate flat documents. Collection specifications shall document the resulting query patterns and required indexes.
Reason: Apply one consistent document structure across the MES database model.
Alternatives Considered: Allow collection-specific nested documents or arrays.
Impact: Affects all MES collection designs, including BOM, routing, scheduling, inspection, checklist, and evidence-related records. Cross-collection query patterns and indexes must be considered during collection design.
Status: APPROVED
Approved By: Project Owner on 2026-09-27



DEC-003
Date: 2026-09-28
Decision: Add `mes_prod_operation` as the authoritative Production Execution master collection for reusable operation definitions. Each `mes_prod_routingOperation` record shall refer to one operation using `operationId` and `operationName`. A routing operation remains a distinct occurrence of an operation at a particular position in a routing.
Reason: Provide a single reusable identity for operations that can appear in multiple routings and provide an authoritative target for operation references.
Alternatives Considered: Store operation details independently in every routing-operation record.
Impact: Applies to Production Execution collection design and to other collection designs that reference operations or routing operations. Does not change the flat-document rule, collection naming convention, or ID/reference convention.
Status: APPROVED
Approved By: Project Owner on 2026-09-27


DEC-004
Date: 2026-09-27
Decision: Treat Production Order and Work Order as separate business entities with distinct planning and execution scopes. A Production Order is the macro-level manufacturing authorization, normally received from ERP, defining the product, authorized quantity, and required date for a finished good or subassembly and tracking overall batch progress. A Work Order is the MES-managed, micro-level execution task derived from a Production Order, defining how and where production operations are performed and tracking shop-floor execution.

Production Order (Macro-Level / Planning)
	Definition: A Production Order is a high-level authorization from the ERP system to manufacture a specific quantity of a finished good or sub-assembly by a certain date.

	Scope: It answers the questions of what needs to be made, how many, and by when. It represents the entire manufacturing lot or batch.

	Lifecycle: It is usually created in the ERP (Enterprise Resource Planning) system and passed down to the MES. It tracks the overall progress of the finished product batch from start to finish.

Work Order (Micro-Level / Execution)
	Definition: A Work Order breaks down a Production Order into actionable, granular tasks or steps assigned to specific workstations, lines, or operators.

	Scope: It answers how and where specific operations will be executed on the shop floor. In many MES platforms, a single Production Order might spawn multiple Work Orders (or routing steps/job orders) across different departments or assembly stages.

	Lifecycle: It is managed primarily within the MES. It tracks real-time labor, material consumption, machine time, and scrap at the workstation level.

Think of a Production Order as a restaurant's master order for 50 birthday cakes for an event. The Work Orders are the individual tickets sent to different stations in the kitchen: mixing the batter, baking the layers, making the frosting, and final decoration.

Reason: The Production Order is a Higher level Authorisation from a ERP system and a Work Order is the Breakup of the Production Order where the approved MES process requires both.
Impact: A Production Order may be decomposed into multiple Work Orders. Each Work Order references its parent Production Order using `productionOrderId` and `productionOrderName`. Production Orders may originate from ERP and may be associated with a sales order or demand forecast; the external reference model is not specified here. Work Orders are assigned to shop-floor resources and carry execution tracking. The flat-document rule prohibits embedding Work Orders in a Production Order document.
Status: APPROVED
Approved By: Project Owner on 2026-09-27

Clarification recorded 2026-09-28 under CHG-003: Expanded the incomplete Work Order definition and corrected the impact/reference direction to match the approved macro-planning versus micro-execution distinction and the project owner's supplied requirements.


DEC-005
Date: 2026-09-28
Decision: `mes_prod_andonCase` represents one Andon condition and its current lifecycle state; `mes_prod_andonEvent` represents one lifecycle transition and references its case using `andonCaseId` and `andonCaseName`.
Reason: Separate current case identity/state from repeated activation, escalation, acknowledgement, resolution, and cancellation history while complying with DATA-001's flat-document requirement.
Impact: Requires the Collection Inventory entry and aligned case/event collection specifications, Data Model, and Entity Relationships. Event taxonomy and meanings are approved under DEC-012; status transitions, case reopening, recipient cardinality, event sequence allocation, and cross-document consistency remain open.
Status: APPROVED
Approved By / Date: Project Owner on 2026-10-07

DEC-006
Date: 2026-09-28
Decision: For Shift Handover, one optional primary `workOrderId` + `workOrderName` reference and a scalar `handoverSummary` are sufficient to record open work and issues. Do not add a separate handover-item relationship collection solely to make every item in the summary individually queryable.
Scope: Applies to `mes_prod_shiftHandover` and its inventory description. The summary is narrative text; it does not establish structured per-item references or item-level querying.
Reason: Project owner confirmed that the scalar summary is sufficient for this handover use case.
Status: APPROVED
Approved By / Date: Project Owner on 2026-09-28


DEC-007
Date: 2026-09-28
Decision: In `mes_prod_workInstruction`, instruction content is identified by a scalar external locator, not by a MongoDB/project entity reference. The field shall be named `instructionContentLocator`; it does not require an entity ID/display-name pair.
Scope: Applies to the Work Instruction collection specification and related data-model/inventory descriptions. The owning content source, locator syntax, and locator lifecycle are not selected by this decision and remain open.
Reason: Project owner confirmed that instruction content is referenced by an external locator.
Status: DEPRECATED — SUPERSEDED BY DEC-009
Approved By / Date: Project Owner on 2026-09-28

DEC-008
Date: 2026-10-07
Decision: Create `mes_prod_scrapReason` as the authoritative controlled reason reference collection for scrap dispositions. `mes_prod_scrapRecord` must carry a required `scrapReasonId` + `scrapReasonName` pair. Reason codes are unique within `mes_prod_scrapReason`; `Active` reasons may be selected for new records and `Retired` reasons remain resolvable for historical records.
Scope: Production Execution scrap reason reference data and Scrap Record references. This does not define scrap accounting, inventory posting, or nonconformance workflows.
Reason: Resolve the undefined scrap-reason owner and requiredness while keeping controlled reason identity separate from transactional scrap records.
Status: APPROVED
Approved By / Date: Project Owner on 2026-10-07

DEC-009
Date: 2026-10-07
Decision: Create `mes_prod_instructionContent` as the MES registry for exact-version external work-instruction content references. Each `mes_prod_workInstruction` must reference one registry record using `instructionContentId` + `instructionContentName`. Each registry record has a required opaque `instructionContentLocator` that uniquely identifies one exact content version within the registry. The content payload remains outside MongoDB. The locator is stored as supplied; no provider, URI scheme, or storage technology is mandated. `Active` content references may be used for new Work Instruction revisions; `Retired` references remain resolvable historically.
Scope: Production Execution work-instruction metadata and external-content reference management. This decision supersedes DEC-007's rule that no MES content-reference entity be used. External provider ownership and provider-specific locator resolution remain integration concerns.
Reason: Establish a stable MES ID/name reference and resolve `instructionContentLocator` requiredness without embedding content or adding nested structures.
Status: CURRENT — historical approval recorded below; self-supersession corrected under DEC-018
Approved By / Date: Project Owner on 2026-10-07

DEC-010
Date: 2026-10-07
Decision: The Production Execution numeric fields previously labeled `Decimal` shall use BSON `Double`: `mes_prod_downtimeEvent.durationSeconds`, `mes_prod_productionOrder.plannedQuantity`, `mes_prod_productionReport.reportedQuantity`, `mes_prod_reworkRecord.reworkQuantity`, `mes_prod_routingOperation.standardCycleTime`, `mes_prod_scrapRecord.scrapQuantity`, and `mes_prod_workOrder.plannedQuantity`. Collection specifications shall state the concrete BSON type. Sample documents shall use `Double(value)` for these fields; project sample documents shall not use decimal constructors.
Scope: Applies to the listed Production Execution fields and to project-wide collection specification and sample-document numeric typing. BSON Double may approximate some decimal fractions. Units, precision, rounding, and range must come from approved requirements; unresolved values remain open and must not be invented. No scaled-integer alternative is approved by this decision.
Reason: Resolve BSON type ambiguity according to the project owner's direction to avoid decimal constructors in documents.
Status: APPROVED
Approved By / Date: Project Owner on 2026-10-07

DEC-011
Date: 2026-10-07
Decision: Approve the following collection-specific status values and meanings for Production Execution: `mes_prod_andonCase` — `Active`, `Acknowledged`, `Resolved`, `Cancelled`; `mes_prod_dispatchAssignment` — `Planned`, `Dispatched`, `InProgress`, `Completed`, `Cancelled`; `mes_prod_downtimeEvent` — `Open`, `Closed`, `Cancelled`; `mes_prod_operation` — `Draft`, `Active`, `Retired`; `mes_prod_operationExecution` — `NotStarted`, `InProgress`, `Paused`, `Completed`, `Cancelled`; `mes_prod_productionOrder` — `PendingRelease`, `Released`, `OnHold`, `Cancelled`, `Completed`; `mes_prod_productionReport` — `Submitted`, `Voided`, `Corrected`; `mes_prod_reworkRecord` — `PendingAuthorization`, `Authorized`, `InProgress`, `Completed`, `Cancelled`; `mes_prod_routing` — `Draft`, `PendingApproval`, `Active`, `Retired`; `mes_prod_routingOperation` — `Active`, `Inactive`; `mes_prod_scrapRecord` — `Recorded`, `PendingApproval`, `Approved`, `Voided`; `mes_prod_shiftHandover` — `Draft`, `Submitted`, `Acknowledged`, `Cancelled`; `mes_prod_workInstruction` — `Draft`, `PendingApproval`, `Approved`, `Retired`; and `mes_prod_workOrder` — `Draft`, `Released`, `InProgress`, `OnHold`, `Completed`, `Cancelled`. The meanings are the collection-specific definitions recorded in the corresponding approved specifications.
Scope: Approves only the listed status vocabularies and their stated meanings. It does not establish a shared vocabulary, state-transition graphs, state-dependent validation beyond rules already specified, or close the open Production Order/Work Order transition relationship. `mes_prod_andonEvent.eventType` is outside this decision and remains subject to its separate event-taxonomy decision.
Reason: Resolve the proposed lifecycle values so sample documents can use approved, collection-specific status values.
Status: APPROVED
Approved By / Date: Project Owner on 2026-10-07

DEC-012
Date: 2026-10-07
Decision: Approve the `eventType` values and meanings for `mes_prod_andonEvent`: `Activated` — case activation; `Escalated` — case escalation; `Acknowledged` — acknowledgement transition; `Resolved` — resolution transition; `Cancelled` — cancellation transition. The Andon Event remains a transition record referencing its parent Andon Case. It does not gain a `status` field.
Scope: Approves only this event classification vocabulary and its meanings. It does not define permitted state-transition sequences, reopening behavior, recipient cardinality, event-sequence allocation/concurrency, immutability, retention, or case/event synchronization. It resolves the event-taxonomy open item noted by DEC-005 and the DEC-011 scope statement; DEC-011's status vocabularies are unchanged.
Reason: Resolve the proposed Andon event types so event sample documents and collection design can use an approved classification vocabulary.
Status: APPROVED
Approved By / Date: Project Owner on 2026-10-07

DEC-013
Date: 2026-10-07
Decision: Approve the Production Execution business-number patterns, requiredness, and uniqueness scopes in Section 8.5 of `01_Requirements/DocumentIdAndReferenceConvention.md` and the corresponding collection specifications. The approved patterns are: `AC-YYYY-######` for Andon cases; `AE-YYYY-######` for Andon events; `DA-YYYY-######` for dispatch assignments; optional `DT-YYYY-######` for downtime events; `OP-######` for operation codes; optional `OX-YYYY-######` for operation executions; `PO-YYYY-######` for Production Orders; `PR-YYYY-######` for Production Reports; `RW-YYYY-######` for rework; stable `RT-######` routing-lineage numbers; `SCR-######` scrap-reason codes; `SC-YYYY-######` Scrap Records; `HO-YYYY-######` Shift Handovers; stable `WI-######` work-instruction-lineage numbers; and `WO-YYYY-######` Work Orders. `YYYY` is the four-digit assignment year and `######` is a six-digit zero-padded sequence.
Scope: All listed numbers are globally unique within their owning collection and across statuses, except optional `downtimeEventNo` and `operationExecutionNo`, which are unique within their collection when present. `routingNo` and `workInstructionNo` identify global lineages; each `(number, revision)` pair is globally unique. `operationCode` and `scrapReasonCode` are controlled codes and globally unique in their respective collections. Set `dispatchAssignmentNo` to required; set `downtimeEventNo` and `operationExecutionNo` to optional. The sequence storage/allocation mechanism and Production Execution sequence concurrency implementation remain open under the existing convention; no external-system number is defined by this decision.
Reason: Resolve the missing per-collection business-number patterns, requiredness, and uniqueness scope so collection designs and sample data use consistent approved identifiers.
Status: APPROVED
Approved By / Date: Project Owner on 2026-10-07

DEC-014
Date: 2026-10-07
Decision: Align the project-owned Phase 6 sample-document generation prompt, `100_Self_Notes/Prompts/140_Data_Generation.md`, with DATA-002 and DEC-010. For Production Execution fields specified as BSON `Double`, examples shall use `Double(value)`. The prompt shall not direct users to use Decimal128 or any decimal constructor in project sample documents. If an approved specification's numeric type conflicts with DATA-002, sample generation must stop and report the conflict rather than infer or change the type.
Scope: Applies to the project-owned Phase 6 sample-generation prompt and the numeric representation guidance it gives. It does not change collection field types, the seven fields already approved as BSON Double under DEC-010, or any other approved schema.
Reason: Remove prompt guidance that conflicts with the project's approved prohibition on decimal constructors and state the required Double sample syntax.
Status: APPROVED
Approved By / Date: Project Owner on 2026-10-07

Clarification recorded 2026-10-07 under DEC-012: The Andon event type vocabulary and meanings are approved. Allowed transitions, reopening, recipient cardinality, sequence allocation, and case/event consistency remain open.

DEC-015
Date: 2026-10-08
Decision: Approve DATA-003. Entity stems in scalar reference keys shall be grammatically singular and paired consistently as `<entity>Id` + `<entity>Name` (or the approved display-value suffix when a conventional name is not appropriate). Irregular plurals use their conventional singular forms, for example `traceableEntitiesId` → `traceableEntityId`, `traceabilityIdentitiesId` → `traceabilityIdentityId`, and `workOrdersId` + `workOrdersName` → `workOrderId` + `workOrderName`. Repeated references shall use separate flat relationship records, each with a singular reference pair, under DATA-001. Pluralized scalar keys shall not imply multiple references.
Scope: Applies to all MES MongoDB requirements, collection specifications, inventory/model/relationship design artifacts, and sample documents. This approves key naming only; it does not change relationship cardinality, collection ownership, business semantics, or the flat-document rule.
Reason: Eliminate plural scalar reference keys and preserve an unambiguous one-entity-per-reference key convention.
Status: APPROVED
Approved By / Date: Project Owner on 2026-10-08

DEC-016
Date: 2026-10-08
Decision: Approve DATA-004. Store every MongoDB collection specification in `05_Design/Database/CollectionDesign/<moduleCode>/<collectionName>.md`, with the module code derived from the collection name and the filename matching the complete collection name. The approved module folders are `prod`, `trace`, `qa`, `perf`, `plan`, `matl`, `maint`, `man`, `tool`, and `ai`. Current collection specifications shall not remain in the `CollectionDesign` root.
Scope: Applies to all current and future MES collection design specifications. At the time of this decision, sample storage was outside scope; sample collection file organization is separately governed by DATA-005 / DEC-017. This decision does not change collection names or schema.
Reason: Group the collection specifications by functional module to make the 118-file design set easier to navigate and maintain.
Status: APPROVED
Approved By / Date: Project Owner on 2026-10-08

DEC-017
Date: 2026-10-08
Decision: Approve DATA-005. Store every MongoDB sample collection file at `05_Design/Database/Collections/<moduleCode>/<collectionName>.js`, with the module code derived from the collection name and the filename matching the complete collection name. The approved module folders are `prod`, `trace`, `qa`, `perf`, `plan`, `matl`, `maint`, `man`, `tool`, and `ai`. Current sample collection files shall not remain in the `Collections` root.
Scope: Applies to all current and future MES sample collection files. Collection specifications remain under `CollectionDesign/<moduleCode>/` per DATA-004 / DEC-016. This decision approves the file organization only; sample contents remain demonstration data unless separately approved.
Reason: Group the 118 sample files by functional module to make the sample-data library easier to navigate and maintain.
Status: APPROVED
Approved By / Date: Project Owner on 2026-10-08


DEC-018
Date: 2026-10-11
Decision: Establish GEN-001. Routine user-authorized requirements, documentation, schemas, relationships, and sample-data work requires consistency checks and traceable material changes, without separate sign-off at each stage. Explicit current user instructions take precedence over earlier project instructions within their scope.
Scope: Project documentation governance only. Supersedes earlier requirements for separate inventory, field-level schema, vocabulary, index, and sample-generation documentation approval. Unresolved rules remain OPEN. Manufacturing release, quality hold/release, waiver, and operational authorization behavior and status vocabularies are unchanged. Historical approval records remain evidence of earlier decisions; they do not impose new approval gates. Reference-only notes and prompts are not authoritative.
Reason: Simplify the project workflow while preserving consistency, traceability, and visible requirements gaps.
Status: CURRENT
Recorded By / Date: Project Owner direction on 2026-10-11

Record correction: DEC-009's historical status line incorrectly says it is superseded by itself. DEC-009 remains CURRENT and supersedes DEC-007, consistent with its scope and the current data model. This correction changes no business behavior.
