**FILE TYPE: PROJECT SPECIFIC — REFERENCE-ONLY PROMPT**

# Stage 4 — Design One MongoDB Collection

Read `121_Collection_Design_Common_Rules.md` first. Design only the requested collection, using the exact name and scope in the current inventory. Do not redesign MES architecture or generate the final 10 sample documents.

## Inputs

- Collection: `[COLLECTION_NAME]`
- Module: `[MODULE_CODE]`
- Stage 1 inventory baseline: `[STAGE_1_OUTPUT]`
- Stage 2 research: `[STAGE_2_OUTPUT]`
- Stage 3 dependency mapping: `[STAGE_3_OUTPUT]`
- Any current user instructions that refine scope.

If the collection name/module is not supplied, identify the exact collection only when it is unambiguous from the current task and inventory; otherwise ask. If a material dependency is unresolved, stop before finalizing the affected schema and report the blocker. Do independent work that is not blocked.

## Read before design

- Project Instructions, Context, Decisions Log, current Functional Structure and Project Scope Boundary.
- Current data, naming, identifier/reference, and collection-specification organization requirements.
- Current Collection Inventory and `05_Design/Database/Dependancy_Map.md`.
- The selected collection's existing specification, if any, and specifications for referenced/dependent collections.
- Relevant approved research, requirements, architecture/interface decisions, Data Model, and Entity Relationships.

Use the inherited field-discovery, functional-completeness, justification, modeling-depth, business-rule, cross-collection-integrity, missing-field, and completeness-metric criteria below. Resolve conflicts using current project authority; do not use prompt examples as authority.

---
# 7. MANDATORY FIELD DISCOVERY PROCESS

For every collection, derive fields systematically from the following categories.

## 7.1 Identification

Evaluate:

- System ID
- Business number
- External IDs
- Transaction number
- Reference number
- Sequence number
- Revision/version
- Source-system identifier

## 7.2 Organizational context

Evaluate:

- Enterprise
- Company
- Site
- Plant
- Shop
- Department
- Production line
- Work center
- Station
- Machine
- Equipment

## 7.3 Product and manufacturing context

Evaluate:

- Product
- Part number
- Part revision
- BOM
- Routing
- Operation
- Process
- Manufacturing order
- Work order
- Batch
- Lot
- Serial number
- Customer
- Supplier

## 7.4 Planning and scheduling

Evaluate:

- Planned quantity
- Scheduled quantity
- Planned start/end
- Scheduled start/end
- Actual start/end
- Priority
- Sequence
- Resource allocation
- Capacity
- Cycle time
- Takt time
- Setup time
- Changeover time
- Schedule status
- Dispatch status
- Planning constraints

## 7.5 Execution

Evaluate:

- Execution status
- Start/stop timestamps
- Quantity processed
- Good quantity
- Rejected quantity
- Reworked quantity
- Scrap quantity
- Material consumed
- Machine used
- Operator assigned
- Operation completed
- Hold/release information

## 7.6 Quality

Evaluate:

- Inspection status
- Inspection result
- Specification
- Tolerance
- Measurement
- Defect
- Rejection reason
- Non-conformance
- Disposition
- Rework
- Scrap
- Quality hold
- Approval/release
- Traceability

## 7.7 Equipment and tooling

Evaluate:

- Equipment
- Machine
- Tool
- Fixture
- Die
- Mold
- Tool life
- Tool usage
- Maintenance status
- Calibration status
- Breakdown
- Condition monitoring

## 7.8 Manpower

Evaluate:

- Employee
- Operator
- Supervisor
- Skill
- Qualification
- Certification
- Attendance
- Shift assignment
- Training
- Labor hours
- Operator authorization

## 7.9 Materials

Evaluate:

- Material
- Inventory location
- Stock status
- Lot
- Batch
- Serial number
- Reservation
- Allocation
- Issue
- Consumption
- Return
- Transfer
- Expiry
- Shelf life
- FIFO/FEFO
- Material genealogy

## 7.10 Operational controls

Evaluate:

- Status
- Hold
- Release
- Approval
- Authorization
- Reason code
- Exception
- Deviation
- Override
- Cancellation
- Reversal
- Correction
- Retry
- Reconciliation

## 7.11 Integration

Evaluate:

- ERP references
- PLM references
- QMS references
- CMMS references
- WMS references
- SCADA references
- PLC references
- Industrial IoT event references
- Integration timestamps
- Source system
- Message correlation
- Synchronization status
- Error status

## 7.12 Audit and history

Evaluate:

- Created timestamp
- Created by ID and Name
- Updated timestamp
- Updated by ID and Name
- Approved by
- Released by
- Closed by
- Change reason
- Revision
- Source of modification
- Lifecycle timestamps

Not every category belongs in every collection.

For each candidate field, determine whether it belongs in:

1. The current collection.
2. Another existing collection.
3. A proposed missing collection.
4. A derived calculation rather than persistent storage.

Avoid both missing fields and unnecessary field duplication.

---


---

# 9. COLLECTION-LEVEL FUNCTIONAL COMPLETENESS

For every collection, produce a **Functional Coverage Matrix** before finalizing the field dictionary.

Use:

| Manufacturing Scenario | Required Information | Supporting Fields | Covered? | Gap |
|---|---|---|---|---|

Evaluate at least:

- Normal process
- Partial transaction
- Cancellation
- Hold/release
- Rework
- Correction
- Multi-shift
- Multi-plant
- External integration
- Audit/history
- Exception handling

Use `Not Applicable` where a scenario genuinely does not apply.

If a scenario cannot be supported by the proposed fields, identify the gap.

Do not claim completeness while material functional gaps remain.

---


---

# 10. FIELD-LEVEL JUSTIFICATION

For every proposed field, record:

| Attribute | Requirement |
|---|---|
| Field name | Approved naming convention |
| Data type | MongoDB BSON type |
| Required | Yes/No/Conditional |
| Default | Where applicable |
| Business meaning | Precise manufacturing definition |
| Source | Operator/PLC/ERP/MES/Calculated/etc. |
| Data owner | Authoritative owning system or module |
| Update trigger | What changes the field |
| Validation | Allowed values and constraints |
| Reference | Related collection where applicable |
| ID–Name pairing | Applicable/Not applicable |
| Index | Recommended where applicable |
| Justification | Why the field is needed |
| Evidence | Research source or project requirement |
| Scenario | Manufacturing scenario supported |

**No field should be added without a clear purpose.**

**No important operational field should be omitted merely to keep the schema small.**

---


---

# 12. DATA MODELING DEPTH

Design collections at a level suitable for an actual commercial MES.

Distinguish carefully between:

- Master records
- Configuration records
- Transaction headers
- Transaction details
- Event records
- State records
- Relationship records
- History records
- Calculation records
- Summary records
- Integration records

Do not overload one collection with information that logically belongs to multiple collections.

For example, a WorkOrder should not contain all its operations as embedded objects when `WorkOrderOperation` is separately modeled.

Similarly, an inspection header should not contain all inspection measurements as nested objects when an inspection result detail collection is required.

Evaluate high-volume collection behavior, including:

- Expected write frequency
- Expected document growth
- Retention requirements
- Common query filters
- Query sort patterns
- Index cardinality
- Archival requirements
- Time-series suitability
- Change history
- Data correction patterns

Do not recommend indexes without considering the intended query patterns and write costs.

---


---

# 13. BUSINESS RULES AND LIFECYCLE

For every collection, identify:

- Creation event
- Initial status
- Allowed statuses
- Allowed state transitions
- Update events
- Approval requirements
- Cancellation behavior
- Hold/release behavior
- Closure behavior
- Reopening behavior
- Retirement behavior
- Deletion restrictions
- Historical retention
- Referential validation

Provide a state-transition matrix where applicable.

Do not invent state transitions that contradict approved MES workflows.

---


---

# 14. CROSS-COLLECTION INTEGRITY

Before finalizing a collection:

1. Identify all incoming references.
2. Identify all outgoing references.
3. Check that referenced collections exist in the approved inventory.
4. Check that ID field types are consistent.
5. Check that Name fields use the correct naming convention.
6. Check business-number relationships.
7. Check lifecycle compatibility.
8. Check ownership of shared data.
9. Check for redundant collections.
10. Check for missing relationship collections.

Where a required entity or collection is missing, document:

- Missing entity
- Manufacturing requirement
- Affected collection
- Recommended new collection
- Impact of not adding it

Do not silently create the missing collection.

---


---

# 15. MISSING-FIELD DETECTION

After creating the initial field dictionary, conduct a second independent review specifically to discover omissions.

Assume the first design is incomplete.

Review the proposed fields from the perspectives of:

1. Shop-floor operator
2. Production supervisor
3. Production planner
4. Quality engineer
5. Maintenance engineer
6. Tool-room engineer
7. Material controller
8. Plant manager
9. MES integration engineer
10. MES database architect

For each perspective, ask:

- What operational questions must this person answer?
- What actions must this person perform?
- What information is required to perform those actions?
- What information must be retained for audit?
- Which fields are missing?
- Which missing fields belong in another collection?

Create a **Missing Field Assessment** table:

| Missing Field | Manufacturing Scenario | Why Required | Target Collection | Priority | Evidence |
|---|---|---|---|---|---|

Classify priority as:

- Critical
- High
- Medium
- Low

Resolve critical and high gaps before declaring the collection ready for review.

Do not invent a field solely to increase field count.

---


---

# 16. FIELD COMPLETENESS METRICS

For every collection, report:

- Number of proposed fields
- Number of required fields
- Number of conditional fields
- Number of optional fields
- Number of ID–Name reference pairs
- Number of status/control fields
- Number of audit fields
- Number of external integration fields
- Number of applicable scenarios evaluated
- Number of scenarios fully covered
- Number of unresolved functional gaps
- Number of research-backed fields
- Number of project-specific inferred fields

Also calculate:

**Scenario Coverage Percentage**

= Applicable scenarios fully covered / Total applicable scenarios × 100

This is a design review metric, not proof of real-world completeness.

Do not establish an arbitrary minimum number of fields.

A collection with 15 appropriate fields may be correct, while another collection may legitimately require 80 or more.

Completeness is determined by manufacturing functionality, not field count.

---


---

# 17. OUTPUT FOR EACH COLLECTION

Produce a detailed Markdown specification containing:

## A. Collection Overview

- Collection name
- Module
- Collection type
- Purpose
- Business definition
- Manufacturing process supported
- Authoritative owning module

## B. Industry Research Summary

- Commercial MES products researched
- Relevant functional capabilities
- Applicable standards
- Source references
- Research limitations

## C. Real-World Manufacturing Scenarios

- Normal scenarios
- Exception scenarios
- Complex scenarios
- Lifecycle scenarios

## D. Functional Coverage Matrix

## E. Complete Field Dictionary

Include all field-level attributes defined in Section 10.

## F. Field Groups

Organize fields logically by purpose without introducing nested MongoDB structures.

## G. ID–Name Reference Pairs

## H. System IDs and Business Numbers

## I. Data Types

## J. Required, Optional and Conditional Fields

## K. Validation Rules

## L. Status Values and State Transitions

## M. Relationships with Other Collections

## N. External System Integration

## O. Recommended Indexes

For each index include:

- Fields
- Order
- Unique/non-unique
- Scope
- Query supported
- Reason
- Expected write overhead considerations

## P. Audit and Historical Data

## Q. Real-World Manufacturing Exceptions

## R. Missing Field Assessment

## S. Missing Collection Assessment

## T. Cross-Collection Consistency Check

## U. Field Completeness Metrics

## V. One Representative MongoDB Document

Generate only ONE representative sample document showing the approved field structure and correct BSON constructors.

Do not generate the final 10 sample documents.

## W. Assumptions

## X. Open Issues

## Y. Research Sources

Provide source URLs and explain the specific design decisions each source supports.

## Z. Final Design Review

Identify:

- Covered requirements
- Remaining gaps
- Unverified assumptions
- Decisions requiring approval
- Readiness for review

---


---

# 20. STRICT RESTRICTIONS

Do NOT:

- Generate generic textbook schemas.
- Design collections solely from their names.
- Assume all MES products use the same data model.
- Invent proprietary vendor database structures.
- Invent research sources.
- Claim research was performed without examining sources.
- Omit exception-handling fields.
- Omit lifecycle/status requirements.
- Omit external-system references where applicable.
- Omit traceability information where applicable.
- Omit required audit information.
- Omit mandatory ID–Name pairs.
- Introduce nested documents.
- Introduce embedded object arrays.
- Use MongoDB ObjectId or 24-character hexadecimal IDs.
- Change collection names currently specified by the inventory without an explicitly authorized inventory change.
- Change approved field naming conventions.
- Add a collection silently. Report a collection gap as a proposal and follow current scope/change-control instructions; do not imply a separate sign-off gate unless a current requirement explicitly requires one.
- Introduce unrelated fields.
- Generate the final 10 sample documents.
- Declare a collection production-ready merely because its schema is syntactically valid.

---


---

# 21. FINAL INSTRUCTION

**The objective is not to generate MongoDB fields. The objective is to model actual manufacturing operations accurately and completely.**

For every collection, work in this sequence:

**Research real-world MES functionality → Understand manufacturing transactions → Identify normal and exception scenarios → Derive required data → Assign data to the correct collections → Design MongoDB fields → Validate completeness → Check cross-collection consistency.**

Do not reverse this sequence.

A collection design is acceptable only when its fields can be justified through real manufacturing requirements, established MES functionality, applicable standards, or explicitly documented project-specific assumptions.

This is the Stage 4 prompt. Verify that Stage 1 scope, Stage 2 research, and Stage 3 dependency mapping are available and current before designing. If an earlier stage is missing, do not claim it was completed; perform only checks that are authorized and possible, and report the limitation.


## Current storage rule

Save the specification at:

`05_Design/Database/CollectionDesign/<moduleCode>/<exactCollectionName>.md`

Use the exact module code and name from the current Functional Structure and Collection Inventory. Follow the existing specification format when one is authoritative. Do not save under the CollectionDesign root or use stale example names.

## Stage 4 completion checks

- Every field is justified by a requirement, approved decision, research source, or explicitly labeled assumption.
- The document is flat and uses separate flat records for repeated entities/items; specify parent/reference, ordering, query, index, and consistency behavior.
- All references and required ID/display pairs, audit fields, types, status values, IDs, and business-number rules match current authority.
- No unknown lifecycle, number rule, ownership boundary, data type, or external source is silently filled in.
- The representative example, if included, is one illustrative structure only, uses the approved types and flat fields, and is not a sample-data set.
- List cross-collection consistency results and every unresolved dependency/open issue.
- Follow GEN-001: record material file changes in Change_Log; create/update Decisions_Log only for an actual decision. Do not label the specification APPROVED unless the current user explicitly asks for that change.

