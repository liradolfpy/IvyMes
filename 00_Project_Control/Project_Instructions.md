# PROJECT — MASTER INSTRUCTIONS

**Document ID:** MES-CTRL-001
**Document Name:** MES Project Master Instructions
**Version:** 0.8
**Status:** CURRENT
**Owner:** MES Project Team
**Last Updated:** 2026-10-11

---


# 1. PURPOSE

This document defines the mandatory instructions, standards, conventions, and working rules that ChatGPT shall follow when assisting with the MES software-development project.

The purpose of this document is to ensure that all project outputs remain:

* Consistent
* Traceable
* Structured
* Technically coherent
* Reusable
* Maintainable
* Consistent with established decisions
* Suitable for incorporation into formal project documentation

This document is the **authoritative source for project-specific working instructions** unless a later explicit user instruction or current project instruction supersedes a provision contained here.

---

# 2. PROJECT DESCRIPTION

## 2.1 Project Name

MES — Manufacturing Execution System

## 2.2 Project Objective

The objective of this project is to **define and document the architecture, information model, database/schema structure, system schematics, functional specifications, interface specifications, workflows, and sample documents for a Manufacturing Execution System (MES)**.

The project is an **architecture and documentation design exercise**. It is intended to establish a coherent and reusable blueprint for the MES, including its functional structure, entities, relationships, data flows, interfaces, business rules, document structures, and representative sample artifacts.

The project does **not** include implementation or validation of the MES software or physical manufacturing environment.

## 2.3 Project Scope

### Included

* Functional architecture and module decomposition
* System and solution architecture
* Functional and information architecture
* Domain models, entity models, and relationships
* Database and data schemas
* Data dictionaries and field definitions
* Process models and workflow definitions
* System schematics and architecture diagrams
* Data-flow and interaction diagrams
* Interface and integration specifications
* API/interface contracts at the specification level
* Business rules and validation rules at the specification level
* Requirements and functional specifications
* Traceability and genealogy models
* Sample datasets and illustrative records
* Sample forms, reports, labels, certificates, checklists, and other  documents
* Document templates and documentation standards
* Architecture decisions, assumptions, constraints, and open issues
* Representative examples demonstrating intended project behavior

### Explicitly Out of Scope

* Software implementation or production code development
* Frontend/UI development and implementation
* UI/UX implementation
* Hardware design, hardware development, PLC/automation hardware, sensors, and physical device engineering
* Procurement, installation, commissioning, or deployment of hardware
* Software testing, test execution, QA execution, and formal validation
* Performance/load testing
* User acceptance testing (UAT)
* Production deployment and operational rollout
* Actual production-site integration or commissioning
* Training delivery or operational support
* Real production data processing unless explicitly provided as an authorized sample/reference dataset
* Finance, accounting, balance sheets, P&L, and other non-MES enterprise functions unless explicitly required as an integration boundary

> **Scope boundary:** The project may define specifications, schemas, interfaces, workflows, sample UI/document representations, and architecture diagrams where needed to describe the project. It shall not implement software, build production UI, design hardware, or perform testing.

## 2.4 Intended Outputs

The project may produce:

* Functional architecture
* System and solution architecture
* Domain and information models
* Functional specifications
* Requirements specifications
* Technical design specifications
* Process-flow and workflow diagrams
* System schematics
* Architecture diagrams
* Data-flow and interaction diagrams
* Database/entity-relationship models
* Database schemas
* Data dictionaries
* Interface and integration specifications
* API/interface contracts at specification level
* Business rules and validation rules
* Traceability and genealogy models
* Sample datasets
* Sample reports
* Sample forms
* Sample labels and certificates
* Sample documents
* Document templates
* Configuration/data-definition specifications
* Architecture decision records
* Assumption and issue registers
* Other architecture and documentation artifacts required to describe the proposed project

> UI mockups or screen representations may be created **only when required as illustrative documentation of a functional concept**. They are not software/UI implementation deliverables.

> Test specifications, test cases, test execution, and test reports are **not project deliverables**.

---

# 3. AUTHORITY OF THIS DOCUMENT

The following hierarchy shall apply when determining which instruction takes precedence.

## 3.1 Instruction Hierarchy

1. Applicable ChatGPT/system-level requirements
2. Explicit instructions given in the current user request, within their stated scope
3. Current Project Master Instructions
4. Current project decisions
5. Current Project Context
6. Current project artifacts
7. General industry best practices

Where project instructions conflict, explicit current user instructions take precedence within their stated scope; otherwise the more recent current instruction takes precedence.

If the conflict cannot be resolved reliably, ChatGPT shall identify the conflict rather than silently choosing one interpretation.

---

## GEN-001 — Simplified project decision workflow

Routine requirements, documentation, collection designs, relationship models, and sample-data changes may proceed within the current user-authorized scope without separate approval at each stage. Check changes for consistency with current project rules and record material decisions and changes in the project logs.

Explicit current user instructions supersede earlier project instructions within their stated scope. Keep unresolved business rules identified as OPEN; removing an approval step does not resolve missing requirements or establish production readiness. Where a material ambiguity affects the task, identify it and request clarification when necessary; continue work that does not depend on the answer.

Require a separate approval step only where a specific requirement explicitly defines its purpose, scope, and responsible person. Existing manufacturing release, quality hold/release, waiver, and operational authorization concepts remain unchanged. Fields such as `approvalStatus` and `approvedAt`, and operational status values such as `PendingApproval` and `Approved`, describe MES business behavior rather than documentation sign-off.

For new or revised documentation use DRAFT / CURRENT / SUPERSEDED and Updated By / Date. For new decisions use PROPOSED / CURRENT / SUPERSEDED and Recorded By / Date. Earlier APPROVED, PENDING, REJECTED, or DEPRECATED labels and approver records in historical log entries and revision histories remain historical evidence; they do not impose a new sign-off gate. Previously approved baselines remain current unless superseded. Reference-only notes and prompts cannot override current project instructions.

---

# 4. FUNDAMENTAL WORKING PRINCIPLES

ChatGPT shall:

1. Preserve consistency across the project artifacts.
2. Reuse established terminology.
3. Avoid unnecessarily introducing new concepts or technologies.
4. Identify assumptions when information is incomplete.
5. Clearly distinguish:

   * Confirmed requirements
   * Assumptions
   * Proposed solutions
   * Current decisions
   * Open questions
6. Avoid silently changing established project decisions.
7. Maintain traceability between requirements and resulting designs where practical.
8. Prefer modular and reusable solutions.
9. Prefer clear and maintainable designs over unnecessarily complex designs.
10. Highlight contradictions between new requests and existing project rules.
11. Ask for clarification when an ambiguity could materially affect the design.
12. Never represent a proposed design as a current project decision unless it has been explicitly established and recorded.

---

# 5. PROJECT TERMINOLOGY

The following terminology shall be used consistently.

| Term   | Definition                     |
| ------ | ------------------------------ |
| MES    | Manufacturing Execution System |


## 5.1 Terminology Rule

Once a project-specific term has been formally defined, ChatGPT should use that terminology consistently throughout subsequent documentation.

Alternative terminology should not be introduced merely for stylistic variation.

---

# 6. FUNCTIONAL STRUCTURE
1. The FUNCTIONAL STRUCTURE is defined in   '03_Functional_Structure\FunctionalStructureModules.md'.
2. The Sections in the FUNCTIONAL STRUCTURE is defined in   '03_Functional_Structure\FunctionalStructureSections.md'.



# 7. REQUIREMENT CLASSIFICATION

Requirements shall be classified where practical.

### Functional Requirement

Defines what the system shall do.

### Non-Functional Requirement

Defines characteristics such as:

* Performance
* Availability
* Security
* Scalability
* Reliability
* Usability
* Maintainability

### Business Rule

Defines a rule governing business or manufacturing behavior.

### Technical Constraint

Defines a technology, infrastructure, integration, or implementation constraint.

### Assumption

A condition temporarily accepted because confirmed information is unavailable.

### Open Issue

An unresolved matter requiring clarification or decision.

---

# 8. REQUIREMENT IDENTIFICATION

Requirements should use stable unique identifiers.

Recommended format:

```text
FR-XXX-###
NFR-XXX-###
BR-XXX-###
TR-XXX-###
INT-XXX-###
```

Examples:

```text
FR-PROD-001
FR-QUAL-004
NFR-PERF-002
BR-INV-003
INT-ERP-001
```

Requirement identifiers shall not be unnecessarily changed once assigned.

---


# 9. ARCHITECTURE AND DESIGN PRINCIPLES

Unless specifically overridden by a current project decision:

1. Prefer clear separation of domains, responsibilities, and architectural layers.
2. Avoid unnecessary coupling between functional modules and data domains.
3. Prefer reusable domain models, schemas, specifications, and design patterns.
4. Keep interfaces and integration boundaries explicit.
5. Avoid duplicate definitions of business concepts, entities, and business rules.
6. Define data ownership and authoritative sources where applicable.
7. Define validation rules at the specification level.
8. Consider auditability for business-critical project activities.
9. Define transaction and consistency expectations where relevant to the architecture.
10. Identify concurrency and synchronization requirements where multiple actors or systems may modify the same data.
11. Avoid premature commitment to implementation technologies unless explicitly selected and recorded within the authorized scope.
12. Prefer understandable, maintainable, and technology-neutral architecture documentation.
13. Clearly distinguish conceptual, logical, and physical schema decisions.
14. Ensure relationships between functional requirements, business rules, data entities, workflows, and interfaces are traceable where practical.
15. Do not turn an architectural proposal into an implementation commitment unless explicitly selected and recorded within the authorized scope.

---

# 12. DATABASE DESIGN STANDARDS

Database designs shall document, where applicable:

* Entity names
* Table names
* Column names
* Data types
* Primary keys
* Foreign keys
* Unique constraints
* Indexes
* Relationships
* Nullability
* Default values
* Audit fields
* Referential integrity

## 12.1 Standard Audit Fields

Where applicable, consider:

```text
createdAt
createdById
createdBy
updatedAt
updatedById
updatedBy
```

Additional audit fields may be defined where required.

## 12.2 Naming Convention

1. The naming convention of the collections is defined in   '01_Requirements\CollectionNamingConvention.md'.
2. The naming convention of the keys in the collections is defined in   '01_Requirements\KeyNamingConvention.md'.
3. The module subfolder structure for collection design specifications is defined in '01_Requirements\CollectionDesignOrganization.md'.
4. The module subfolder structure for sample collection documents is defined in '01_Requirements\SampleDataOrganization.md'.

## 12.3 Data Modeling Convention
1. The Data Modeling Convention of the collections is defined in   '01_Requirements\DataModelingConvention.md'.


The selected convention must remain consistent.


## 12.4 Flat-Document Rule

**DATA-001 — All MES MongoDB collection documents shall be flat.** Every field shall be a top-level scalar value. Embedded documents and arrays are prohibited in all collections, without exception.

When a concept requires repeated items or relationships, represent each item or relationship as a separate flat document in an appropriate collection. Collection specifications shall identify the resulting cross-collection query patterns and required indexes. This rule does not prescribe a particular application implementation or require routine use of `$lookup`.

## 12.5 Numeric BSON Type Rule

**DATA-002 — Every numeric field in a MongoDB collection specification shall identify a concrete BSON type; the ambiguous type label `Decimal` shall not be used.** For the Production Execution fields listed in current `DEC-010`, use BSON `Double` and `Double(value)` in executable sample documents. Do not use decimal constructors in project sample documents. Because BSON `Double` can approximate some decimal fractions, field units, precision, rounding, and valid range shall be defined by current requirements; do not infer missing rules. A scaled-integer representation requires a documented decision defining its scale, units, range, and conversion rules. Follow `01_Requirements/DataModelingConvention.md` for implementation details.

**DATA-003 — Entity stems in scalar reference keys shall be grammatically singular.** A single referenced entity shall use a singular ID/name pair, such as `workOrderId` + `workOrderName`; plural forms such as `workOrdersId` + `workOrdersName` are prohibited. Use conventional singular forms for irregular plurals, such as `traceableEntityId` and `traceabilityIdentityId` (not `traceableEntitiesId` or mechanically formed `traceabilityIdentitieId`). Repeated references shall be represented as separate flat relationship records under DATA-001, each with its own singular reference pair. Do not use pluralized scalar keys to imply multiplicity. Follow `01_Requirements/KeyNamingConvention.md` and `DataModelingConvention.md` for implementation details.

**DATA-004 — Collection design specifications shall be organized by functional module.** Store each collection specification as `05_Design/Database/CollectionDesign/<moduleCode>/<collectionName>.md`, where `<moduleCode>` is the module code in the collection name and `<collectionName>` exactly matches the MongoDB collection name. Keep the ten current module folders (`prod`, `trace`, `qa`, `perf`, `plan`, `matl`, `maint`, `man`, `tool`, `ai`) distinct. Do not leave current collection specifications in the `CollectionDesign` root. Follow `01_Requirements/CollectionDesignOrganization.md` for the current mapping and details.

**DATA-005 — Sample collection documents shall be organized by functional module.** Store each sample file as `05_Design/Database/Collections/<moduleCode>/<collectionName>.js`, using the module code embedded in the collection name and the exact full collection name as the filename. Use the ten current module folders (`prod`, `trace`, `qa`, `perf`, `plan`, `matl`, `maint`, `man`, `tool`, `ai`); do not leave current sample collection files in the `Collections` root. This rule changes file organization only and does not approve sample data as production data. Follow `01_Requirements/SampleDataOrganization.md` for the current mapping and details.


---


# 18. FILE AND FOLDER NAMING

Recommended project structure:


Base Folder : `E:\AI\ChatGpt\MES\`

The Folder system is defined in `00_Project_Control\Folder_Guide.md`
---

# 19. SAMPLE DOCUMENTS

Sample documents created by ChatGPT shall clearly indicate their status.

For example:

> SAMPLE / DEMONSTRATION DOCUMENT — ILLUSTRATIVE DATA

This label identifies illustrative content and does not establish production readiness.

Sample documents should be realistic enough to demonstrate the intended system behavior but must not be mistaken for actual production data or current requirements.

---

# 20. DATA AND EXAMPLES

Unless actual project data has been explicitly provided and authorized for use, examples shall use clearly identified sample data.

Examples should not accidentally imply that fabricated values are real project data.

Use labels such as:

```text
Example
Sample
Demonstration
Illustrative
TBD
TBC
```

where appropriate.

---

# 21. ASSUMPTIONS

Whenever a material project detail is unknown, document it as an assumption.

Recommended format:

```text
ASSUMPTION-ID: ASM-###
Description:
Impact:
Reason:
Status:
```

Do not silently convert an assumption into a project fact.

---

# 22. OPEN ISSUES

Open issues shall use stable identifiers.

Recommended format:

```text
ISS-###
```

Example:

```text
ISS-014
Question: Which ERP system will be integrated?
Impact: Affects integration architecture.
Owner: TBD
Status: OPEN
```

---

# 23. PROJECT DECISIONS

Important decisions shall be recorded separately in:

```text
Decisions_Log.md
```

Recommended decision format:

```text
DEC-###

Date:
Decision:
Reason:
Alternatives Considered:
Impact:
Status:
Recorded By / Date:
```

Once a decision is current, future designs should respect it unless it is explicitly superseded.

---

# 24. CHANGE CONTROL

Changes to mandatory project rules shall be deliberate and traceable.

When changing an instruction:

1. Identify the instruction.
2. Record the previous rule where useful.
3. Record the new rule.
4. Explain the reason.
5. Update the revision number.
6. Update the change log.

Recommended format:

```text
CHG-###

Date:
Instruction ID:
Previous:
New:
Reason:
Requested By:
Recorded By / Date:
```

---

# 25. MASTER INSTRUCTION IDENTIFIERS

Mandatory instructions should use stable IDs.

Recommended categories:

```text
GEN-###     General
REQ-###     Requirements
ARCH-###    Architecture
ARCH-###    Architecture
DATA-###    Data / Schema
API-###     API / Integration Specification
SCH-###     Schematics
DOC-###     Documentation
FUNC-###    Functional Design
SAMP-###    Sample Documents / Data
SEC-###     Security Architecture
OPS-###     Operational Model
```

Example:

```text
SCH-003
All architecture diagrams shall identify external systems separately from MES components.
```

---

# 26. CONFLICT DETECTION

When a new request appears to conflict with an existing mandatory project instruction:

1. Identify the conflicting instruction.
2. Explain the conflict briefly.
3. Identify the affected artifact or decision.
4. Ask for clarification if necessary.
5. Do not silently discard the existing instruction.

If the user explicitly changes the instruction, update the master instruction set accordingly when appropriate.

---


# 28. QUALITY CHECK BEFORE FINAL OUTPUT

Before producing a significant artifact, verify:

* [ ] Project terminology is consistent.
* [ ] Relevant mandatory instructions were considered.
* [ ] Current decisions were respected.
* [ ] Requirements are not contradicted.
* [ ] Assumptions are identified.
* [ ] TBD items are identified.
* [ ] IDs follow project conventions.
* [ ] Version/status information is appropriate.
* [ ] Diagrams use consistent terminology.
* [ ] Interfaces and integration boundaries are clearly identified.
* [ ] Data entities, relationships, and schemas are clearly defined where applicable.
* [ ] Sample data/documents are clearly identified as illustrative.
* [ ] Proposed content is not represented as current.
* [ ] No unnecessary implementation technologies have been introduced.
* [ ] The artifact remains within the architecture/schema/documentation scope.
* [ ] No software, UI implementation, hardware, or testing deliverable has been inadvertently introduced.
* [ ] No unnecessary new conventions have been introduced.
* [ ] The artifact is internally consistent.

---

# 29. CHATGPT WORKING BEHAVIOR

When working on the project tasks, ChatGPT should:

### DO

* Check relevant project files when available.
* Reuse established project terminology.
* Maintain consistency with current artifacts.
* Point out important contradictions.
* Identify assumptions.
* Suggest improvements when appropriate.
* Maintain traceability.
* Preserve existing decisions.
* Use structured identifiers.
* Keep documentation professional, architecture-oriented, schema-oriented, and implementation-neutral.

### DO NOT

* Invent project decisions.
* Present assumptions as facts.
* Change established terminology without reason.
* Introduce technologies merely because they are fashionable.
* Replace a current design without identifying the change.
* Create contradictory versions of the same requirement.
* Treat sample data as real data.
* Present unresolved requirements or proposed decisions as established facts.
* Silently resolve significant ambiguities.

---

# 30. TECHNOLOGY BASELINE


Database platform: MongoDB is the current primary database platform for the MES database architecture, data modeling, and collection/schema design.
Numeric BSON fields: Every numeric field in a collection specification must name a concrete BSON type; the ambiguous type label `Decimal` is prohibited. Production Execution fields covered by current DEC-010 use BSON `Double` and are shown with `Double(value)` in sample documents. Do not use decimal constructors in project sample documents. Follow `01_Requirements/DataModelingConvention.md` for precision, rounding, range, and scaled-integer rules.
Other technology choices: No frontend, backend, cloud, operating system, PLC, hardware, or deployment technology has been selected. These remain technology-neutral unless separately decided and recorded.

The project shall remain **technology-neutral at the frontend, backend, cloud platform, operating system, PLC platform, hardware platform, or deployment technology** 

The project currently does **not** establish a frontend, backend, cloud platform, operating system, PLC platform, hardware platform, or deployment technology as an implementation baseline.

---


# 32. AUDITABILITY AND TRACEABILITY

For business-critical  activities, consider recording:

* Who performed the action
* What was changed
* When it was changed
* Previous value
* New value
* Reason for change where required
* Source/system responsible for the change

The exact audit requirements shall be determined by project requirements.

---


# 35. REPORTING AND DOCUMENT OUTPUT

Reports and generated documents should clearly distinguish:

* Actual data
* Calculated data
* Derived data
* Sample data
* Static reference data

Where calculations are important, document the calculation logic.

---

# 36. DECISION LOG REFERENCE

The authoritative decision record is:
00_Project_Control\Decisions_Log.md

The format is : 
Decision: [Clear and concise statement of the architectural or technical decision made]
Scope: [Explicitly define what components, modules, or layers the decision applies to, and what remains undecided or out of scope]
Status: [PROPOSED / CURRENT / SUPERSEDED]
Recorded By / Date: [Name] on [YYYY-MM-DD]

Example: 
Decision: MongoDB is the primary database platform for the MES architecture and collection/schema design.
Scope: Applies to database and collection design. Frontend, backend, cloud, operating system, and deployment technologies remain undecided.
Status: CURRENT
Recorded By / Date: SG on 2026-09-27



ChatGPT should consult this file when a task depends on previously made architectural, functional, technical, or process decisions.

---

# 37. PROJECT CONTEXT REFERENCE

The authoritative project context is:

```text
00_Project_Control\Project_Context.md
```

This document should contain relatively stable information describing the system, its objectives, organizational context, functional scope, and major constraints.

---

# 38. MASTER INSTRUCTION UPDATE RULE

This document is intended to evolve.

When a new permanent rule is established:

1. Add it to this document.
2. Give it a unique instruction ID.
3. Update the version.
4. Update the revision history.
5. Record the change in `Change_Log.md`.

Temporary instructions should not automatically become permanent project rules.

---

# 39. PROJECT SCOPE BOUNDARY

1. The current Project Specific Rules are defined in   '03_Functional_Structure\ProjectScopeBoundary.md'.



# 40. CURRENT PROJECT-SPECIFIC RULES

1. The current Project Specific Rules are defined in   '03_Functional_Structure\ProjectSpecificRules.md'.




# 42. REVISION HISTORY

| Version | Date       | Change           | Author       | Approval |
| ------- | ---------- | ---------------- | ------------ | -------- |
| 0.1     | YYYY-MM-DD | Initial template | Project Team | APPROVED    |
| 0.3 | 2026-09-27 | Added DATA-001, requiring flat MES MongoDB documents and separate records for repeated items and relationships. | MES Project Team | APPROVED |
| 0.4 | 2026-10-07 | Added DATA-002, requiring concrete BSON numeric types and prohibiting decimal constructors in project sample documents; DEC-010 approves BSON Double for the identified Production Execution fields. | MES Project Team | APPROVED |
| 0.5 | 2026-10-08 | Added DATA-003, requiring grammatically singular entity stems in scalar reference keys and separate flat records for repeated references; aligned project design outputs under DEC-015. | MES Project Team | APPROVED |
| 0.6 | 2026-10-08 | Added DATA-004, requiring collection design specifications to be stored in module-code subfolders under `CollectionDesign`; aligned requirements, prompts, folder guidance, and all 118 existing specifications under DEC-016. | MES Project Team | APPROVED |
| 0.7 | 2026-10-08 | Added DATA-005, requiring sample collection documents to be stored in module-code subfolders under `Collections`; aligned requirements, sample-generation prompt, folder guidance, and all 118 existing sample files under DEC-017. | MES Project Team | APPROVED |
| 0.8 | 2026-10-11 | Apply GEN-001 / DEC-018; remove separate documentation sign-off gates and preserve unresolved rules and operational controls. | MES Project Team | CURRENT |

---

# 43. FINAL DIRECTIVE

For all  project work:

> **Maintain consistency with this Master Instructions document, the current project context, and current project decisions. Clearly distinguish facts, requirements, assumptions, proposals, examples, and current decisions. Apply GEN-001 and the instruction hierarchy. When instructions conflict or a material ambiguity exists, identify the issue rather than silently making a project decision.**

**END OF PROJECT MASTER INSTRUCTIONS**


Documentation update — 2026-10-11: Apply GEN-001 / DEC-018; remove separate documentation sign-off gates, retain open requirements and operational controls, and use current artifact status. See CHG-022.
