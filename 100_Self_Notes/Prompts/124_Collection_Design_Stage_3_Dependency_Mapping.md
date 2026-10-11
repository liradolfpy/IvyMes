**FILE TYPE: PROJECT SPECIFIC — REFERENCE-ONLY PROMPT**

# Stage 3 — Dependency Mapping

Read `121_Collection_Design_Common_Rules.md` first. This stage establishes a reviewable design order; it does not design collections or resolve unapproved business decisions.

## Inputs

- Stage 1 inventory scope: `[STAGE_1_OUTPUT]`
- Stage 2 research: `[STAGE_2_OUTPUT]`
- Current dependency map: `05_Design/Database/Dependancy_Map.md`

## Read

- Current project instructions, context, decisions, functional modules/sections, relevant requirements, Collection Inventory, Data Model, Entity Relationships, and existing specifications.
- The current Dependency Map, including its open dependency questions.
- Research outputs relevant to the selected scope.

## Work

1. Compare every relevant reference/dependency in the inventory and existing specifications. Identify upstream identity/master dependencies, reusable definitions, parent transactions, operational records, detail/relationship records, events, and derived/reporting consumers.
2. Group collections into a proposed dependency order, explaining each edge and wave. Confirm exact names and complete coverage against the inventory; do not invent a collection to break a cycle.
3. Identify cycles, ambiguous ownership/direction/cardinality, missing collection owners, repeated concepts needing separate flat records, and any dependency that blocks a valid schema.
4. Reconcile findings with approved decisions. A proposed sequence cannot approve a decision. Keep unresolved items OPEN and say what file/decision must settle each one.
5. If updating the map is within the current user request, update `05_Design/Database/Dependancy_Map.md`, point affected inventory/prompt artifacts to it, and record material changes in `00_Project_Control/Change_Log.md`. Do not add a Decisions Log entry unless an actual project decision is made. Otherwise provide the proposed map content without writing files.

## Output

Provide the dependency graph/order, collection counts and exact-name coverage, reasons for the ordering, cross-module flows, blockers/cycles, and an open-question register with impacts and the authoritative file/decision needed for resolution. Distinguish verified dependency edges from inferred or proposed edges.

## Completion handoff

State which collection(s) are ready for Stage 4 and which require a project decision or additional requirement first. A collection may proceed with non-blocking OPEN items only when the specification clearly labels them and does not invent a value.
