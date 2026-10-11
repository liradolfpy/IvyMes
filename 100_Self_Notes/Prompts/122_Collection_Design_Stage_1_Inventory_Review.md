**FILE TYPE: PROJECT SPECIFIC — REFERENCE-ONLY PROMPT**

# Stage 1 — Inventory Review

Read `121_Collection_Design_Common_Rules.md` first. This stage verifies scope only; do not research or design schemas yet.

## Inputs

- Requested module(s): `[MODULE_CODE(S)]` (if omitted, ask whether the user intends all ten modules; do not assume scope).
- Current project root.

## Read

- `00_Project_Control/Project_Instructions.md`
- `00_Project_Control/Project_Context.md`
- `00_Project_Control/Decisions_Log.md`
- `03_Functional_Structure/FunctionalStructureModules.md`
- `03_Functional_Structure/FunctionalStructureSections.md`
- `03_Functional_Structure/ProjectScopeBoundary.md`
- Relevant `01_Requirements/` files, including Collection Naming, Key Naming, Data Modeling, Document ID/Reference, and Collection Design Organization.
- `05_Design/Database/Collection_Inventory.md`
- Existing specifications in each requested module folder under `05_Design/Database/CollectionDesign/`.

## Work

1. Resolve module codes from the current Functional Structure.
2. Extract exact collection names and purposes from the current inventory; do not rename or add entries.
3. For each collection, identify its existing specification path and actual document status/content. Do not infer specification approval from `CURRENT — inventory scope only`.
4. Identify missing specifications, missing requirements, dependency references, duplicate names, and apparent scope conflicts without correcting them.
5. Map inventory collections to functional sections and report uncovered sections or collections without a clear functional owner.

## Output

Provide a verified list grouped by module, with exact collection name, inventory purpose, expected specification path, specification existence/status, and known dependency notes. Summarize counts only after validating them from the current files. List gaps and conflicts separately. Do not create a schema, sample document, research report, or approval record.

## Completion handoff

State the exact verified scope that Stage 2 should research. If a scope or authority ambiguity prevents a reliable list, state the question and proceed only with the unambiguous portion.
