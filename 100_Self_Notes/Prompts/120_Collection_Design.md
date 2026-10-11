**FILE TYPE: PROJECT SPECIFIC — REFERENCE-ONLY PROMPT**

# MES Collection Design Prompt — Stage Runner

**Prompt bundle:** 2.0  
**Updated:** 2026-10-11  
**Authority:** Reference-only workflow aid. Current project artifacts and the current user instruction govern.

## Purpose

The former all-in-one collection-design prompt has been divided into independent stage prompts. Run one stage at a time, in order, and carry each stage’s result into the next. Read `121_Collection_Design_Common_Rules.md` before running any stage prompt.

## Stage files

| Stage | Prompt file | Main outcome |
|---|---|---|
| 1 — Inventory review | `122_Collection_Design_Stage_1_Inventory_Review.md` | Verified collection scope, existing specification/status baseline, and gaps. |
| 2 — Research | `123_Collection_Design_Stage_2_Research.md` | Evidence-based manufacturing workflow and scenario research for selected module(s)/collection(s). |
| 3 — Dependency mapping | `124_Collection_Design_Stage_3_Dependency_Mapping.md` | Verified dependency order and open dependency questions, registered in the dependency map when authorized. |
| 4 — Collection design | `125_Collection_Design_Stage_4_Collection_Design.md` | One collection specification at a time, based on research, inventory, decisions, and dependencies. |
| 5 — Module review | `126_Collection_Design_Stage_5_Module_Review.md` | Within-module coverage and consistency report. |
| 6 — Cross-module review | `127_Collection_Design_Stage_6_Cross_Module_Review.md` | MES-wide cross-module consistency and gap report. |

## How to run

1. Identify the current task and provide the stage prompt with its required inputs, for example `[MODULE_CODE]`, `[COLLECTION_NAME]`, or prior-stage output.
2. Read the common rules and that stage’s source-file checklist.
3. Run only that stage. Do not skip ahead or perform work belonging to later stages.
4. Store a design only at its defined project path. If a stage has no established artifact path, report in the response or use a user-specified path; do not invent a project artifact location.
5. Recheck the source artifacts at each stage. A prior prompt output never outranks a current requirements/decision/specification artifact.

## Expected sequence

**Inventory review → Research → Dependency mapping → Collection design → Module review → Cross-module review**

Stage completion is workflow progress, not schema approval or production readiness. The common rules and prompt files are kept under `100_Self_Notes/Prompts` for reference only and must not be used as MES business logic.
