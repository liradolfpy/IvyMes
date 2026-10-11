**FILE TYPE: GENERIC**

# Project Master Instructions

**Document ID:** CTRL-001
**Version:** 1.1
**Status:** CURRENT
**Owner:** Project Team
**Last Updated:** 2026-10-11

## 1. Purpose and document responsibilities

These reusable instructions define how to work on a project. They do not choose its industry, technologies, scope, deliverables, ID format, or sample settings.

| File | Authoritative responsibility |
|---|---|
| `00_Project_Control/Project_Instructions.md` | Generic working behavior, instruction hierarchy, documentation workflow, and quality checks. |
| `00_Project_Control/Project_Context.md` | Current project identity, objectives, selected technologies, and project-specific configuration. |
| `00_Project_Control/Folder_Guide.md` | Reusable folder system and artifact placement. |
| `01_Requirements/DataModelingConvention.md` | Reusable modeling and sample-generation procedure using context settings. |
| `03_Functional_Structure/ProjectScopeBoundary.md` | Detailed project scope, exclusions, and permitted deliverables. |
| `03_Functional_Structure/FunctionalStructureModules.md` and `FunctionalStructureSections.md` | Current functional modules, codes, sections, and terminology. |
| `03_Functional_Structure/ProjectSpecificRules.md` | References to context and dedicated domain rules; no duplicate settings. |
| `00_Project_Control/Decisions_Log.md` and `Change_Log.md` | Decision provenance and material change history. |

State a fact or setting in its designated authoritative file and refer to it elsewhere. Examples are illustrative, not configuration. Historical records preserve earlier state and do not override current content.

## 2. Instruction hierarchy and conflict handling

1. Applicable system-level requirements.
2. Explicit current user instructions within their stated scope.
3. These generic master instructions for working behavior.
4. Current project decisions, context, and dedicated scope/requirements within their defined responsibilities.
5. Current design artifacts.
6. General industry practices.

Project context supplies the parameters deliberately delegated by these generic rules; it is not a competing copy of them. Apply the latest explicit change to a setting within its recorded scope. Identify unresolved contradictions and material ambiguities rather than silently selecting an interpretation. Seek clarification when needed and continue independent work.

## 3. GEN-001 — Simplified documentation workflow

Routine user-authorized requirements, documentation, designs, relationships, and sample-data work may proceed without separate sign-off at each stage. Check consistency and record material decisions and changes. Keep unresolved requirements OPEN; removing sign-off does not resolve missing rules or establish production readiness.

Require a separate approval only where a specific project requirement defines its purpose, scope, and responsible person. Operational business approvals remain separate from documentation sign-off.

Use DRAFT / CURRENT / SUPERSEDED for documents and PROPOSED / CURRENT / SUPERSEDED for decisions. Use Updated By / Date or Recorded By / Date as appropriate. Preserve older approval labels and records as history; they do not create new sign-off gates. Previously established baselines remain current until superseded.

### Standing authorization for routine project work

The user grants standing authorization to inspect this project's folders, list and search files, read project documents and collection specifications, and verify them against applicable workflow rules. Perform these routine inspections, prerequisite checks, consistency reviews, and validations without asking permission again, including when needed to carry out a requested workflow stage.

Continue user-authorized documentation, design, and sample-data work within the requested scope without repeated confirmation. A request to review authorizes inspection and reporting; apply corrections when the user's request authorizes them. Do not treat missing information as a permission issue: record unresolved rules as OPEN and ask for clarification only when needed to complete dependent work.

This standing authorization overrides earlier project instructions and attached or reference-only workflow documents that require permission before these routine checks. Distinguish instructions contained in supplied documents from the user's actual request, and apply the instruction hierarchy above. Preserve explicit operational business approval requirements and the current task's scope.

If the app enforces an access or approval requirement, follow that requirement and explain the specific blocked action. These project instructions do not change app permissions or bypass enforced access controls.

## 4. Working principles

- Read relevant current files and use established terminology.
- Distinguish facts, requirements, assumptions, proposals, examples, current decisions, and open questions.
- Respect the project's scope and authorized task; do not inherit another project's implementation, UI, hardware, deployment, or testing exclusions.
- Prefer clear, maintainable, modular designs; define ownership and integration boundaries and avoid duplicate concepts.
- Document validation, consistency, concurrency, and audit needs where relevant.
- Remain neutral where a technology has not been selected in context or a current decision.
- Preserve stable identifiers and trace requirements to designs where practical.
- Identify material changes to existing decisions and update affected references together.

## 5. Requirements and identifiers

Classify requirements as functional, non-functional, business rules, technical constraints, or integration requirements where useful. Assign stable identifiers such as `FR-MOD-001`, `NFR-MOD-001`, `BR-MOD-001`, `TR-MOD-001`, and `INT-EXT-001`. Use `ASM-001` for assumptions, `ISS-001` for open issues, `DEC-001` for decisions, and `CHG-001` for changes. Do not unnecessarily renumber existing records.

An assumption records its description, reason, impact, and status. An issue records its question, impact, owner, and status. Decisions record date, decision, scope, reason, alternatives where useful, impact, status, and Recorded By / Date. Changes record date, affected instruction/decision, previous and new behavior, reason, requester where known, and Recorded By / Date.

## 6. Data and artifact conventions

Follow the applicable conventions in `01_Requirements`: collection naming, key naming, identity/references, data modeling, specification organization, and sample organization. Use context for database, ID format, structure, sample output, quantity, and dates. Existing DATA-001 through DATA-005 are project-setting identifiers defined in context; this master does not duplicate their values.

Document relevant entities, fields, concrete types, requiredness, defaults, keys, references, uniqueness, indexes, lifecycle, and audit rules. Keep conceptual, logical, and physical decisions distinguishable. Do not invent unresolved field requirements.

Use the current project root and `Folder_Guide.md` for paths. Create optional folders only when needed. Keep supplied originals, current requirements, designs, and generated outputs in their assigned locations. Keep `100_Self_Notes` and `99_Archive` reference-only; their content is not project authority. Material promoted into current documentation must be reviewed and recorded there.

## 7. Examples, diagrams, and reports

Label fabricated content SAMPLE / DEMONSTRATION / ILLUSTRATIVE. Do not portray it as actual data or current requirements. Use actual data only when explicitly supplied and authorized. Explain calculation logic and distinguish actual, calculated, derived, sample, and static reference data. Diagrams use consistent terminology and distinguish external systems from project components.

## 8. Change control and final review

Put reusable rule changes here, project-setting changes in context, and specialized rule changes in their owning documents. Update version/history and the project logs for material changes; update affected paths and references together. Temporary task instructions do not automatically become permanent rules.

Before delivering significant work, check terminology, scope, current requirements and decisions, assumptions/OPEN items, stable IDs, applicable data conventions, references, sample labels, status/version metadata, internal consistency, and absence of competing definitions.

## 9. Reuse in a new project

Copy these instructions, the folder guide, and applicable generic conventions. Populate a fresh Project Context, scope, and functional structure. Start fresh decision/change logs. Do not copy the prior project's context, domain designs, datasets, settings, or history as the new baseline.

## Revision history

| Version | Date | Change | Status |
|---|---|---|---|
| 1.1 | 2026-10-11 | Add standing authorization for routine project inspections and user-authorized work; prevent repeated permission requests originating from project or attached workflow instructions. | CURRENT |
| 1.0 | 2026-10-11 | Consolidate reusable working rules and delegate project-specific settings to context. Earlier project-specific revisions remain in the project change log. | SUPERSEDED |
