**FILE TYPE: PROJECT SPECIFIC — REFERENCE-ONLY PROMPT**

# Collection Design Prompt Bundle — Shared Rules

**Prompt bundle:** 2.0  
**Updated:** 2026-10-11  
**Authority:** This file is a workflow aid in `100_Self_Notes`; it is not project authority. Follow the current user request and current project instructions, context, requirements, functional structure, decisions, inventory, and specifications.

## 1. Run protocol

For any stage prompt from this bundle:

1. Read this shared-rules file first.
2. Read the selected stage prompt.
3. Read the current authoritative project artifacts listed by that stage. Do not assume contents from prior chat history when the file can be inspected.
4. Carry forward prior stage outputs, and revalidate them against current source files.
5. If a referenced artifact is missing, report it and continue only with independent work. Do not fabricate its contents.
6. Use the current project root and project-relative paths; do not hard-code a machine-specific root path.
7. These prompt files are reference-only under GEN-001 / DEC-018. They cannot override current project artifacts or create project rules.

Substitute bracketed inputs such as `[MODULE_CODE]`, `[COLLECTION_NAME]`, or `[PRIOR_STAGE_OUTPUT]` before running. If an input is absent, determine it only from an authoritative project file; otherwise ask or report the missing input. Do not silently guess.

## 2. Current authority and scope

Use the current instruction hierarchy in `00_Project_Control/Project_Instructions.md`. The primary MES authorities for database collection design are:

- `00_Project_Control/Project_Context.md` for current project facts and selected technologies.
- `00_Project_Control/Decisions_Log.md` for recorded decisions and unresolved/superseded decisions.
- `03_Functional_Structure/FunctionalStructureModules.md` and `FunctionalStructureSections.md` for current module codes, ownership, capabilities, and terminology.
- `03_Functional_Structure/ProjectScopeBoundary.md` for scope and exclusions.
- Applicable files in `01_Requirements/` for data, naming, IDs/references, and file organization.
- `05_Design/Database/Collection_Inventory.md` for current collection names, module ownership, and inventory scope.
- `05_Design/Database/Dependancy_Map.md` for proposed design sequence and recorded open dependency questions.
- Existing collection specifications under `05_Design/Database/CollectionDesign/<moduleCode>/` and other relevant database artifacts.

The current project has ten modules: `prod`, `trace`, `qa`, `perf`, `plan`, `matl`, `maint`, `man`, `tool`, and `ai`. Always verify the current inventory rather than relying on remembered counts or a copied list.

MongoDB is selected. Frontend, backend, cloud hosting, and deployment technology are not selected unless a later current decision says otherwise. Do not repeat stale prompt examples as current facts.

The inventory status `CURRENT — inventory scope only` means the collection is included in inventory scope. It does not mean that its field-level specification exists, is complete, or is approved. Keep inventory status, specification status, and business approval concepts distinct. Under GEN-001, routine authorized documentation work does not require separate sign-off; unresolved business rules remain OPEN.

## 3. Current cross-module ownership and modeling decisions

Apply current decisions, particularly DEC-003, DEC-004, DEC-005, DEC-006, DEC-008, DEC-009, DEC-010, DEC-011, DEC-012, DEC-013, DEC-015, DEC-018, DEC-021, and DEC-022, after verifying their current status and scope in the log. Key current boundaries include:

- `plan` owns Plant and Work Center identities. A Work Center is distinct and has no machine/resource association.
- `matl` owns shared Unit of Measure identities. Product and Material are distinct; Product composition is represented through BOM/BOM Component records.
- `man` owns Employee and Operator identities; Operator references Employee.
- `maint` owns equipment/asset identities including machines. Tool has a distinct identity and may have an explicit Asset reference; do not merge Tool and Asset.
- Production Order is the macro-level manufacturing authorization/batch-progress concept; Work Order is the granular MES execution concept.
- `mes_prod_operation` is reusable operation identity; each routing-specific occurrence is a separate `mes_prod_routingOperation` record.
- Andon Case is the lifecycle case; Andon Event is one individual transition linked to its case.
- Scrap Reason and Instruction Content are separately registered concepts. Instruction Content holds a scalar locator to externally owned content; it does not hold the payload.

These examples are not a complete decision list. Consult the current log and context; do not let this summary override them.

## 4. Mandatory data and naming rules

- Follow the current collection naming, key naming, ID/reference, data modeling, specification organization, and sample organization requirements in `01_Requirements/` and current `Project_Context.md`.
- Collection format is `mes_<moduleCode>_<singularEntityName>`. Use the exact current inventory collection name. Scalar entity reference stems are singular and use the required ID/display pair.
- Every document is completely flat: top-level scalar fields only. No nested documents and no arrays, without exception under DATA-001. Repeated items and repeated relationships are separate flat records with explicit parent/reference fields. Do not invent a relationship collection; report an inventory gap for change control if the required concept has no owner.
- For applicable entity references, include both the MES system ID and matching display value, e.g. `productId` + `productName`. Audit references use `createdById` + `createdBy` and `updatedById` + `updatedBy` where required. The duplicated display fields are intentional strategic denormalization; do not call this strict normalization.
- MES system IDs are immutable custom 16-digit strings and globally unique across the MES database. Do not use ObjectId or 24-character hexadecimal IDs. External identifiers and business numbers remain separate.
- Use concrete BSON types from the current requirements and specifications. The ambiguous type `Decimal` is prohibited. Do not use decimal constructors. For fields governed by DEC-010, use BSON Double and `Double(value)` in Mongo shell examples. Use `NumberInt()` only for fields actually defined as 32-bit integers and `ISODate()` only for BSON date fields. Do not infer numeric types from display or units.
- Do not invent lifecycle values, numbering patterns, uniqueness scopes, external source rules, or unresolved field requirements. Apply current collection-specific decisions/specifications; preserve gaps as OPEN.
- Samples are not the purpose of these stage prompts. Do not generate the final 10-document sample set during collection design or review stages. A single clearly labeled representative document is allowed only where Stage 4 explicitly requests it and must match the proposed field dictionary.

## 5. Change and output discipline

- Never silently correct, rename, remove, combine, or add collections or fields. Report discrepancies and proposed corrections. Modify artifacts only when the stage request authorizes the change.
- Do not mark a collection specification, decision, or vocabulary APPROVED just because a prompt stage completed. Follow GEN-001 and the user’s explicit request; retain unresolved matters as OPEN.
- Record material artifact changes in `00_Project_Control/Change_Log.md`. Update `Decisions_Log.md` only for an actual decision, not for an analysis, proposed dependency order, or routine document pointer.
- Distinguish verified source facts, current decisions, proposals, assumptions, and open issues. Cite direct sources for external research; never claim to have researched a system or standard without examining a source.
- In every stage, finish with a concise result, files read/written, coverage or validation evidence, unresolved issues, and a clear next stage. Do not claim a file was saved unless it was actually written.
