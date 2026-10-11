CHG-001

Date: 2026-09-27
Instruction ID: DATA-001
Previous: The data-modeling convention required flat documents; the master instructions did not state this as a universal mandatory rule.
New: All MES MongoDB collection documents must be flat, with no embedded documents or arrays. Repeated items and relationships must use separate flat documents.
Reason: Establish the flat-document model as a permanent mandatory project rule and define how repeated concepts are represented.
Requested By: Project Owner
Approved By: Project Owner


CHG-002

Date: 2026-09-28
Instruction ID: N/A — design artifact alignment under approved DEC-003
Previous: The approved operation master existed in the collection inventory, but the routing-operation and related Production Execution specifications and logical model artifacts did not consistently document the master reference and relationships.
New: Align the inventory guidance, routing-operation and dependent specifications, Data Model, and Entity Relationships with `mes_prod_operation` as the reusable operation master and `operationId` + `operationName` as its reference pair.
Reason: Resolve H-02 and keep operation identity distinct from a routing-specific operation step.
Requested By: Project Owner (current request)
Approved By: See approved DEC-003 in `Decisions_Log.md`; this change does not approve the remaining proposed collection specifications.


CHG-003

Date: 2026-09-28
Instruction / decision: Clarification and artifact alignment under approved DEC-004 — Production Order / Work Order boundary
Previous: DEC-004 identified Production Order and Work Order as separate entities but contained an incomplete Work Order definition and reversed its stated reference direction. The inventory and proposed specifications left the business boundary unresolved and modeled Production Order primarily as a release transaction.
New: Define Production Order as the macro-level authorization and batch-progress identity; define Work Order as the MES-managed granular execution task; represent one-to-many decomposition as separate flat records with Work Order referencing Production Order; align dispatch with Work Order execution; preserve the supplied product, quantity, due-date, resource-assignment, and tracking distinctions.
Reason: Apply the project owner's explicit Production Order / Work Order requirements consistently while respecting DATA-001. Sales-order/demand-forecast linkage details and child quantity reconciliation remain open because their reference conventions are not established.
Requested By: Project Owner
Approved By: Project Owner instruction dated 2026-09-28; proposed collection artifacts remain subject to their own approval.


CHG-004

Date: 2026-09-28
Instruction / decision: Proposed Andon case/event separation under DEC-005
Previous: `mes_prod_andonEvent` was described both as an Andon lifecycle case and as an individual transition event; individual events had no owning case/group reference.
New: Add proposed `mes_prod_andonCase` as the owner of case identity, current lifecycle state, and affected production/resource context. Define `mes_prod_andonEvent` as one transition per flat document, with required `andonCaseId` + `andonCaseName` and a per-case sequence. Align the inventory, both collection specifications, Data Model, and Entity Relationships.
Reason: Give activation, escalation, acknowledgement, resolution, and cancellation events a stable case owner without embedding repeated events or arrays.
Requested By: Project Owner
Approved By: The project owner directed implementation of the changes; DEC-005 and the inventory/specification amendments remain proposed or pending approval as stated in those artifacts.


CHG-005

Date: 2026-09-28
Instruction / decision: Shift Handover scalar-summary scope under approved DEC-006
Previous: `mes_prod_shiftHandover` had one optional work-order reference and a scalar summary, while the inventory described open work and issues by reference. The specification left open whether multiple work orders/issues required separately queryable relationship records.
New: Clarify that one optional primary work-order ID/name pair plus the scalar handover summary is sufficient. The summary may narratively cover multiple open items, but those mentions are not individually addressable references; no handover-item collection is added.
Reason: Apply the project owner's explicit confirmation and avoid adding structure the handover requirements do not need.
Requested By: Project Owner
Approved By: Project Owner on 2026-09-28; see DEC-006.


CHG-006

Date: 2026-09-28
Instruction / decision: External locator classification for work-instruction content under approved DEC-007
Previous: `instructionContentReference` was described as a possible scalar locator, but its classification as an external locator versus an entity reference was unclear; content ownership and storage approach were unresolved.
New: Rename it `instructionContentLocator` and define it as a scalar external locator, not an entity reference. No MongoDB ID/name pair is required. Keep owning content source, locator format, and lifecycle unresolved until established by project requirements/decision.
Reason: Apply the project owner's confirmation while avoiding invention of an external content system or locator format.
Requested By: Project Owner
Approved By: Project Owner on 2026-09-28; see DEC-007.




CHG-007

Date: 2026-09-28
Instruction / decision: M-06 status semantics clarification
Previous: Collection specifications named `status` consistently but did not consistently define its collection-specific meaning and the meaning of each allowed value. The Andon event/status distinction was not explicit in the current specification.
New: Amend `KeyNamingConvention.md` to require per-collection status meaning, requiredness, allowed values, and value definitions, with state-dependent rules/transitions limited to approved evidence. Add collection-specific proposed status value meanings to every Production Execution collection specification that contains `status`. Clarify that `mes_prod_andonCase.status` is current case lifecycle and `mes_prod_andonEvent.eventType` is transition classification; no Andon event `status` is defined absent a separate approved record lifecycle. Update the inventory and Data Model accordingly.
Reason: Resolve M-06 without imposing one shared status vocabulary across unrelated entities or inventing unapproved lifecycle transitions.
Requested By: Project Owner
Approval: Documentation change requested by Project Owner on 2026-09-28; collection status vocabularies and designs remain at their individually stated approval status.

CHG-008

Date: 2026-10-07
Decision references: DEC-008 and DEC-009
Previous: `mes_prod_scrapRecord.scrapReasonId` + `scrapReasonName` had TBD requiredness and no owning collection. `mes_prod_workInstruction.instructionContentLocator` had TBD requiredness and DEC-007 prohibited a MongoDB content-reference entity, leaving no approved owner for the locator metadata.
New: Add approved `mes_prod_scrapReason` and `mes_prod_instructionContent` collection specifications and inventory entries. Make the scrap-reason ID/name pair required in Scrap Record. Replace the Work Instruction scalar locator with a required instruction-content ID/name reference; store the opaque external locator on the registered content-version record. Update the Production Execution inventory, Data Model, and Entity Relationships. Mark DEC-007 deprecated as superseded by DEC-009.
Reason: Resolve the missing reference owners and field requiredness while preserving flat documents, strategic ID/name pairs, and external ownership of content payloads.
Requested By: Project Owner
Approval: Project Owner on 2026-10-07; see approved DEC-008 and DEC-009.


CHG-009

Date: 2026-10-07
Instruction / decision: DATA-002 and approved DEC-010 — concrete numeric BSON representation
Previous: Seven Production Execution field dictionaries used the ambiguous type label `Decimal`, without specifying the concrete BSON type or sample-document representation. The data-modeling convention did not state numeric precision implications or prohibit decimal constructors in samples.
New: Replace those field types with BSON `Double`; require concrete BSON types for numeric fields; use `Double(value)` in sample documents for the affected fields; prohibit decimal constructors in project sample documents. Update the master instruction version to 0.4, the data-modeling and key-naming conventions, and all seven affected collection specifications. Precision, rounding, range, and units remain subject to approved field requirements and are not inferred here.
Reason: Resolve BSON type ambiguity according to the project owner's direction to avoid decimal constructors.
Requested By: Project Owner
Approval: Project Owner on 2026-10-07; see approved DEC-010.


CHG-010

Date: 2026-10-07
Decision: Approved status vocabularies under DEC-011
Previous: The 14 listed Production Execution collection specifications labeled their status values and meanings proposed or for review; the `mes_prod_operation` lifecycle list was hidden inside an HTML comment.
New: Mark the existing collection-specific status values and meanings approved under DEC-011 in `mes_prod_andonCase`, `mes_prod_dispatchAssignment`, `mes_prod_downtimeEvent`, `mes_prod_operation`, `mes_prod_operationExecution`, `mes_prod_productionOrder`, `mes_prod_productionReport`, `mes_prod_reworkRecord`, `mes_prod_routing`, `mes_prod_routingOperation`, `mes_prod_scrapRecord`, `mes_prod_shiftHandover`, `mes_prod_workInstruction`, and `mes_prod_workOrder`. Move the Operation lifecycle list into visible specification text. No status values were added or removed; remaining transition and validation questions stay open as described in the specifications and DEC-011.
Reason: Record the project owner's approval of the previously proposed lifecycle vocabularies for use in collection design and sample data.
Requested By: Project Owner
Approval: Project Owner on 2026-10-07; see approved DEC-011.


CHG-011

Date: 2026-10-07
Decision: Approved Andon event type vocabulary under DEC-012
Previous: `mes_prod_andonEvent.eventType` values and meanings were proposed and for review; related Data Model, Entity Relationships, Inventory, and DEC-005 text described event taxonomy as unresolved.
New: Approve `Activated`, `Escalated`, `Acknowledged`, `Resolved`, and `Cancelled` with their existing meanings. Update the Andon Event specification, DEC-005 clarification, Collection Inventory, Data Model, and Entity Relationships to reflect DEC-012. Confirm that Andon Event has no `status` field. Leave state transitions, reopening, recipients, sequence allocation, immutability, retention, and case/event consistency open.
Reason: Resolve the event taxonomy so Andon sample data can use approved event types without adding an independent event lifecycle.
Requested By: Project Owner
Approval: Project Owner on 2026-10-07; see approved DEC-012.


CHG-012

Date: 2026-10-07
Decision: Production Execution business-number rules approved under DEC-013
Previous: Production Execution collection specifications left numbering patterns or uniqueness scopes unresolved. `mes_prod_downtimeEvent.downtimeEventNo` and `mes_prod_operationExecution.operationExecutionNo` had proposed requiredness; `mes_prod_dispatchAssignment.dispatchAssignmentNo` had a nonstandard requiredness marker. Several number indexes were conditional on future scope decisions.
New: Approve the 15 Production Execution business-number/code policies in `01_Requirements/DocumentIdAndReferenceConvention.md` Section 8.5 and align the affected collection field dictionaries, requiredness, uniqueness statements, and indexes. Set `dispatchAssignmentNo` required; set `downtimeEventNo` and `operationExecutionNo` optional and unique when present. Approve global collection-level uniqueness, the routing/work-instruction lineage-plus-revision scopes, and controlled-code rules as specified in DEC-013. Update convention version 0.3 and correct the stale Andon case/event approval wording in its collection specification. Keep the concrete concurrent sequence allocator mechanism open.
Reason: Resolve per-collection business-number patterns, requiredness, and uniqueness so samples and implementation can follow one approved rule set.
Requested By: Project Owner
Approval: Project Owner on 2026-10-07; see approved DEC-013.


CHG-013

Date: 2026-10-07
Decision: Sample-generation numeric representation aligned under DEC-014 with DATA-002 and DEC-010
Previous: The Phase 6 sample-generation prompt directed users to use a MongoDB constructor when a specification required Decimal128 and gave no concrete syntax for BSON Double fields. This conflicted with DATA-002's prohibition on decimal constructors and DEC-010's approved `Double(value)` representation for Production Execution fields.
New: Update `100_Self_Notes/Prompts/140_Data_Generation.md` to require `Double(value)` for applicable BSON Double fields, prohibit decimal constructors, add `Double()` to the output syntax guidance, and add a validation check for BSON Double representation. Require reporting and stopping when an approved specification conflicts with DATA-002. No collection schemas or BSON field types changed.
Reason: Make the sample-generation prompt consistent with the approved project numeric-type rule.
Requested By: Project Owner
Approval: Project Owner on 2026-10-07; see approved DEC-014.


CHG-014

Date: 2026-10-08
Instruction / decision: DATA-003 and approved DEC-015 — singular entity stems in scalar reference keys
Previous: Some requirements and design outputs used pluralized scalar reference keys, including `workOrdersId` + `workOrdersName`, `traceableEntitiesId` + `traceableEntitiesName`, and `traceabilityIdentitiesId` + `traceabilityIdentitiesName`. The key convention stated a general singular-noun preference but did not explicitly prohibit plural reference stems or specify irregular singularization.
New: Add DATA-003 to the approved Project Instructions; update `KeyNamingConvention.md`, `DataModelingConvention.md`, and `DocumentIdAndReferenceConvention.md`; rename affected reference pairs consistently in 36 `05_Design` artifacts (20 collection specifications, 13 sample-document files, and the Collection Inventory, Data Model, and Entity Relationships). Use conventional singulars (`workOrder`, `traceableEntity`, `traceabilityIdentity`) rather than mechanical suffix deletion. Update affected design-artifact revisions and verify paired keys remain matched. All updated requirement, decision, change-log, inventory, and model approval/status metadata is APPROVED; sample lifecycle values were not changed by this naming decision.
Reason: Make one-entity reference keys singular and consistent while retaining separate-record modeling for repeated relationships.
Requested By: Project Owner
Approval: Project Owner on 2026-10-08; see approved DEC-015.


CHG-015

Date: 2026-10-08
Instruction / decision: DATA-004 and approved DEC-016 — module-based collection specification folders
Previous: The 118 collection specifications were stored together in the `05_Design/Database/CollectionDesign` root, making module-specific review and maintenance difficult. The requirements and collection-design prompt did not define a module subfolder convention.
New: Add `01_Requirements/CollectionDesignOrganization.md`; update `CollectionNamingConvention.md`, `Project_Instructions.md`, `Folder_Guide.md`, and `100_Self_Notes/Prompts/120_Collection_Design.md`; create the ten approved module folders and move all 118 specifications according to their `mes_<moduleCode>_...` collection names. Update all project path references. All new/updated requirement, instruction, decision, and change-control approval statuses are APPROVED.
Reason: Organize the collection design library by MES functional module while preserving collection names and file contents.
Requested By: Project Owner
Approval: Project Owner on 2026-10-08; see approved DEC-016.


CHG-016

Date: 2026-10-08
Instruction / decision: DATA-005 and approved DEC-017 — module-based sample collection folders
Previous: The 118 sample collection files were stored together in the `05_Design/Database/Collections` root. The sample-generation prompt and requirements directed new files to that flat folder.
New: Add `01_Requirements/SampleDataOrganization.md`; update `CollectionNamingConvention.md`, `CollectionDesignOrganization.md`, `Project_Instructions.md`, `Folder_Guide.md`, and `100_Self_Notes/Prompts/140_Data_Generation.md`; create the ten approved module folders and move all 118 sample files according to their `mes_<moduleCode>_...` collection names. Update all current project path references. All new/updated requirement, instruction, decision, and change-control approval statuses are APPROVED. Sample files remain marked as demonstration data, not approved production data.
Reason: Organize the sample collection library by MES functional module while preserving collection names and sample contents.
Requested By: Project Owner
Approval: Project Owner on 2026-10-08; see approved DEC-017.


CHG-017

Date: 2026-10-08
Instruction / decision: Initial ten-module collection-design work under the current user request; no new project decision
Previous: The approved 118-collection inventory and Production Execution logical model identified `mes_prod_operation`, but the module collection-specification folders contained no accessible collection specifications.
New: Add the first proposed collection specification at `05_Design/Database/CollectionDesign/prod/mes_prod_operation.md`. It preserves the inventory boundary and approved operation identity/code/status decisions, documents public research evidence, and lists unresolved ownership, status-meaning, lifecycle, and reference-display questions. No collection was added and no project decision or status approval was created.
Reason: Begin the controlled collection-design sequence with a foundational operation master after reviewing all ten modules' research context and inventory dependencies.
Requested By: Project Owner (current request)
Approval: Initially prepared as proposed. On 2026-10-08, the Project Owner explicitly directed that each collection schema be marked APPROVED; `mes_prod_operation.md` now records that approval while retaining unresolved items. Existing decisions DEC-003, DEC-011, DEC-013, DEC-015, and DEC-016 remain authoritative within their stated scopes.


CHG-018

Date: 2026-10-08
Instruction / decision: Project Owner direction to mark each collection schema APPROVED; no new domain decision
Previous: `mes_prod_operation` was marked proposed, and no specifications existed for the Production Execution scrap-reason and instruction-content reference collections.
New: Record `mes_prod_operation` as APPROVED per the Project Owner's explicit instruction. Add `mes_prod_scrapReason.md` and `mes_prod_instructionContent.md` under `CollectionDesign/prod/`, each with collection-specific research, flat field definitions, relationships, validation, indexes, metrics, examples, assumptions, and visible open issues; mark each APPROVED under that same instruction. No collection names, inventory rows, status vocabularies, locator technology, or other project decisions were added or changed.
Reason: Continue collection design in dependency order while applying the Project Owner's requested schema-approval status and retaining unresolved details for traceability.
Requested By: Project Owner (current request)
Approval: Project Owner explicitly directed on 2026-10-08 that each schema be marked APPROVED. Open issues remain documented and are not treated as decided.


CHG-019

Date: 2026-10-09
Instruction / decision: Continue ten-module collection design in dependency order; apply Project Owner direction that each schema is APPROVED
Previous: The approved inventory contained foundational plant, work-center, and unit-of-measure concepts, but no collection specifications were available for those dependencies. Their ownership, code uniqueness, lifecycle vocabularies, and some integration boundaries were not defined.
New: Add approved design baselines for `mes_plan_plant`, `mes_plan_workCenter`, and `mes_matl_unitOfMeasure` under their module folders. Each specification includes collection-specific public MES/standards research, functional scenarios, field dictionary, flat ID/name references, validation, status gaps, relationship boundaries, index recommendations, audit rules, completeness metrics, one representative structure, and explicit open issues. No collection names or inventory rows were changed; no new project decisions or status vocabularies were created.
Reason: Define high-dependency organizational and quantity reference concepts before designing dependent planning, material, execution, quality, traceability, performance, and maintenance collections.
Requested By: Project Owner
Approval: Project Owner directed on 2026-10-08 that each schema be marked APPROVED. These three specifications are marked APPROVED under that instruction; unresolved rules remain open and are not treated as approved business decisions. No Decisions_Log entry was added because no new project decision was made.


CHG-020

Date: 2026-10-10
Instruction / decision: Explicit Project Owner approval of the current collection-design batch
Previous: `mes_plan_plant`, `mes_plan_workCenter`, and `mes_matl_unitOfMeasure` were labeled APPROVED under the Project Owner's general 2026-10-08 instruction to keep each schema approved; this batch had not yet been separately recorded as explicitly reviewed and approved.
New: Record the Project Owner's explicit approval, received 2026-10-10, for the current batch consisting of `mes_plan_plant`, `mes_plan_workCenter`, and `mes_matl_unitOfMeasure`. Update each specification's approval line and final design review to identify this direct approval. Existing open issues remain open and are not resolved by this approval.
Reason: Preserve a clear audit trail identifying which collection specifications the Project Owner explicitly approved as a batch.
Requested By: Project Owner
Approval: Explicit Project Owner approval in the current conversation on 2026-10-10.


CHG-021

Date: 2026-10-10
Instruction / decision: Explicit Project Owner approval of all collection entries in the approved Collection Inventory
Previous: The inventory was approved as a whole, but its 118 rows did not show a per-collection approval label. The inventory's authority note correctly distinguished collection scope approval from field-level schema approval. Only three field-level specifications are currently present in `CollectionDesign`.
New: Update `05_Design/Database/Collection_Inventory.md` to version 1.3. Record the Project Owner's explicit approval dated 2026-10-10 on all 118 collection rows as `APPROVED — inventory scope only`; clarify that this approves each collection's inventory-level inclusion and descriptive scope, not an absent field-level schema. The three existing field-level specifications remain directly approved as recorded in their files and CHG-020. Note that CHG-017 and CHG-018 refer to `prod` specification files that are not present in the current workspace; this change does not recreate or infer those schemas.
Reason: Make the Project Owner's collection-scope approval visible per inventory row while preserving the distinction between an approved collection inventory and a reviewed field-level schema.
Requested By: Project Owner
Approval: Explicit Project Owner approval in the current conversation on 2026-10-10.


CHG-022
Date: 2026-10-11
Instruction / decision: GEN-001 / DEC-018 — simplified documentation approval workflow
Previous: Project artifacts used repeated approval gates for inventory, schemas, design rules, and sample generation; the instruction hierarchy placed the current user request below earlier artifacts.
New: Permit routine work within user-authorized scope with consistency checks and traceable material changes, without separate stage sign-off. Give explicit current user instructions precedence within their scope. Use CURRENT documentation and decision status; keep missing rules OPEN and preserve operational MES approvals and historical records. Correct the self-supersession status of DEC-009 by an explicit log clarification.
Reason: Remove unnecessary documentation approvals without inventing missing requirements or changing manufacturing controls.
Requested By: Project Owner
Recorded By / Date: Project Owner direction on 2026-10-11
Affected files:
- 00_Project_Control\Project_Instructions.md
- 01_Requirements\CollectionDesignOrganization.md
- 01_Requirements\CollectionNamingConvention.md
- 01_Requirements\DataModelingConvention.md
- 01_Requirements\DocumentIdAndReferenceConvention.md
- 01_Requirements\KeyNamingConvention.md
- 01_Requirements\SampleDataOrganization.md
- 03_Functional_Structure\ProjectScopeBoundary.md
- 05_Design\Database\Collection_Inventory.md
- 05_Design\Database\CollectionDesign\matl\mes_matl_unitOfMeasure.md
- 05_Design\Database\CollectionDesign\plan\mes_plan_plant.md
- 05_Design\Database\CollectionDesign\plan\mes_plan_workCenter.md
- 05_Design\Database\Data_Model.md
- 05_Design\Database\Entity_Relationships.md
- 00_Project_Control/Decisions_Log.md
- 00_Project_Control/Change_Log.md
- 00_Project_Control/Folder_Guide.md
- 03_Functional_Structure/FunctionalStructureModules.md
