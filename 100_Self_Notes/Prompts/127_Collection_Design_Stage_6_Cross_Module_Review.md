**FILE TYPE: PROJECT SPECIFIC — REFERENCE-ONLY PROMPT**

# Stage 6 — Cross-Module Consistency Review

Read `121_Collection_Design_Common_Rules.md` first. Audit all currently available MES collection specifications against the full current project. This stage reviews; it does not redesign or silently modify collections.

## Read

- Current Project Instructions, Context, Decisions Log, all ten Functional Structure module/section definitions, Project Scope Boundary, and relevant requirements.
- Current Collection Inventory and Dependency Map.
- Every available collection specification across all ten module folders under `05_Design/Database/CollectionDesign/`.
- Data Model, Entity Relationships, relevant architecture/interface artifacts, and existing samples only where applicable to verify declared types/references.
- Prior module-review outputs and open issues.

## Review areas

Check architecture/module and functional coverage; domain boundaries and ownership; duplicate concepts/collections; collection, field, ID, display-name, and audit naming; types and field semantics; required ID/display pairs; flat-document and repeated-record rules; lifecycle vocabulary; business-number and uniqueness consistency; reference integrity, missing owners, and cycles; field reuse vs over-sharing; indexes and query patterns; event/time-series/high-volume requirements; auditability; and available sample ID/reference/type/date consistency.

Treat unresolved decisions as OPEN. Do not classify a proposed map edge as approved; do not treat inventory scope status as spec approval. Separate demonstrated defects from potential risks and incomplete evidence.

## Output

Produce an executive summary and discrepancy report. Classify each finding CRITICAL/HIGH/MEDIUM/LOW and provide artifact/field evidence, functional impact, and a proposed correction. Include module coverage, missing collections (report only; do not create), duplicate concepts, naming/type/reference inconsistencies, dependency cycles, performance concerns, and prioritized correction order. Do not silently correct files or declare the model approved/production-ready.

If the user separately authorizes corrections, apply only those within scope, record material changes in `00_Project_Control/Change_Log.md`, and report files changed. Add a Decisions Log record only if an explicit project decision is made.
