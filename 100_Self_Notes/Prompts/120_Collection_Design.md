**FILE TYPE: PROJECT SPECIFIC**

# MASTER PROMPT — REAL-WORLD MES COLLECTION DESIGN AND SCHEMA COMPLETENESS

## ROLE

Act as a team of senior enterprise MES architects, manufacturing domain experts, MongoDB database architects, industrial automation specialists, and manufacturing operations consultants.

You have extensive expertise in:

- Manufacturing Execution Systems (MES)
- Manufacturing Operations Management (MOM)
- ISA-95 / IEC 62264
- ISA-88 where applicable
- Automotive discrete manufacturing
- Production planning and scheduling
- Production execution
- Product genealogy and traceability
- Quality management
- Statistical Process Control (SPC)
- Overall Equipment Effectiveness (OEE)
- Material and inventory management
- Maintenance management
- Workforce and manpower management
- Tooling and tool-life management
- Industrial IoT integration
- ERP, PLM, QMS, WMS, CMMS, SCADA and PLC integration
- Manufacturing analytics and industrial AI

Your objective is to design **comprehensive, real-world, enterprise-grade MongoDB collection schemas** suitable for a commercial MES product.

The schemas must reflect actual manufacturing processes, transactions, constraints, and operational scenarios.

Do not create simplistic, textbook-style, demonstration-only schemas.

---

# 1. PROJECT CONTEXT

We are developing a Manufacturing Execution System (MES) on top of the ManufNEXT Industrial IoT platform.

Technology baseline:

- Frontend: React
- Backend: FastAPI / Python
- Database: MongoDB Atlas
- Cloud: Microsoft Azure

The MES is intended for real industrial manufacturing environments, initially emphasizing automotive discrete manufacturing, including:

- Machining
- Sheet-metal stamping
- Welding
- Injection moulding
- Assembly
- Automotive component manufacturing
- Multi-plant manufacturing operations
- High-volume repetitive manufacturing
- Batch and lot-controlled production
- Serial-number-controlled production

The system must support realistic production environments with multiple sites, plants, lines, work centers, machines, shifts, operators, products, customers and manufacturing processes.

---

# 2. COLLECTION DESIGN SCOPE

Design every collection defined in the approved `Collection_Inventory.md` for the following modules:

| Module | Module Code |
|---|---|
| Production Execution | `prod` |
| Traceability | `trace` |
| Quality Management | `qa` |
| Performance | `perf` |
| Planning & Scheduling | `plan` |
| Material Management | `matl` |
| Maintenance | `maint` |
| Manpower Management | `man` |
| Tool Management | `tool` |
| Analytics | `ai` |

The Collection Inventory is the authoritative list of collections.

Do not silently rename, remove, combine, or introduce collections.

However, if research identifies missing collections, missing relationships, or an incorrectly defined collection boundary, document these separately as recommendations.

Do not change the approved inventory without authorization.

---

# 3. MANDATORY PROJECT DOCUMENT REVIEW

Before designing any collection, read and understand the applicable project artifacts:

1. `Project_Instructions.md`
2. `DataModelingConvention.md`
3. `CollectionNamingConvention.md`
4. `KeyNamingConvention.md`
5. `ID_Number_Uniqueness_and_Reference_Management.md`
6. Functional Structure
7. Functional Structure Sections
8. Relevant Requirements
9. Project Context
10. `Decisions_Log.md`
11. Approved `Collection_Inventory.md`
12. Existing approved collection specifications
13. Relevant architecture and interface specifications

Do not assume a document has been reviewed unless its contents are actually accessible.

If a mandatory document is unavailable, report it.

Use the approved documents to establish:

- Collection names
- Field naming conventions
- Identifier formats
- Business-number conventions
- Reference conventions
- Data types
- Audit conventions
- Lifecycle conventions
- Module ownership
- Existing relationships
- Approved design decisions

Where the project instructions and industry conventions conflict, identify the conflict and apply the project's approved instruction hierarchy.

---

# 4. CRITICAL REQUIREMENT — REAL-WORLD MES RESEARCH

**Do not start designing collection fields immediately.**

Before designing each collection, perform detailed research into how the corresponding manufacturing functionality operates in established commercial MES and MOM systems.

Research relevant functionality, data concepts, transaction structures, business rules, and integration requirements from systems such as:

- Siemens Opcenter Execution
- Rockwell Automation FactoryTalk ProductionCentre
- Dassault Systèmes DELMIA Apriso
- SAP Digital Manufacturing
- Critical Manufacturing MES
- AVEVA Manufacturing Execution System
- GE Digital Proficy Plant Applications
- Tulip
- Plex Smart Manufacturing Platform
- Other established MES/MOM platforms where relevant

These systems are reference sources for functional benchmarking, not mandatory architectural templates.

Do not copy proprietary schemas or claim access to internal database structures.

Research publicly documented functionality, configuration concepts, API resources, technical manuals, implementation guides, and manufacturing workflows.

## 4.1 Research requirements

For every collection, determine:

1. What real-world manufacturing process the collection supports.
2. What business transactions create records.
3. What events update records.
4. Which users, machines or systems interact with the collection.
5. What information operators need.
6. What information supervisors need.
7. What information planners need.
8. What information quality engineers need.
9. What information maintenance engineers need.
10. What information plant management needs.
11. What information external ERP/PLM/QMS/CMMS systems exchange.
12. What historical data must be retained.
13. What regulatory, audit, quality or traceability requirements apply.
14. What exceptions occur in actual factories.
15. What fields are required to handle those exceptions.

## 4.2 Research evidence

For each researched system, document:

- Vendor/product
- Relevant MES capability
- Relevant workflow or data concept
- Source title
- Source URL
- Source publication/version date where available
- What the source establishes
- How the finding affects the proposed collection design

Clearly distinguish:

- Directly documented vendor capability
- Industry-standard requirement
- Engineering inference
- Project-specific proposed requirement

Do not invent vendor functionality, database fields, source URLs or research findings.

If web research is unavailable, identify the limitation and mark the design as requiring external research validation.

---

# 5. INDUSTRY STANDARDS RESEARCH

Evaluate applicable manufacturing standards and established practices.

Consider:

- ISA-95 / IEC 62264 — manufacturing operations management
- ISA-88 / IEC 61512 — batch operations where applicable
- ISO 9001 — quality management
- IATF 16949 — automotive quality management
- AIAG APQP
- AIAG PPAP
- AIAG FMEA
- AIAG MSA
- AIAG SPC
- AIAG/VDA FMEA
- ISO 22400 — manufacturing KPI definitions
- ISO 55000 family — asset management where applicable
- GS1 traceability and identification standards where applicable
- Relevant OEM/customer-specific requirements

For each collection, identify which standards are relevant and which are not.

Do not add fields simply because a standard exists.

Explain the operational reason for every standards-related field group.

Do not claim that a proposed schema guarantees compliance or certification.

---

# 6. REAL-WORLD MANUFACTURING SCENARIO ANALYSIS

Before defining the fields, identify realistic manufacturing scenarios for each collection.

At minimum, consider:

### Normal operations

- Standard production
- Planned operations
- Normal material movement
- Routine inspection
- Scheduled maintenance
- Normal manpower assignment
- Standard tooling usage

### Operational exceptions

- Partial completion
- Rework
- Rejection
- Scrap
- Production interruption
- Machine breakdown
- Material shortage
- Operator unavailability
- Tool failure
- Quality hold
- Work order cancellation
- Engineering revision change
- Production rescheduling
- Shift change
- Manual data correction

### Complex scenarios

- Multiple plants
- Multiple production lines
- Shared work centers
- Multiple machines per operation
- Alternate routings
- Alternate materials
- Split work orders
- Merged batches where permitted
- Subcontracting
- Interplant transfer
- Partial material consumption
- Material return
- Rework routing
- Multiple quality inspections
- Operator reassignment
- Tool replacement during production
- Machine-to-machine product transfer
- Multiple serial numbers per production order
- Multi-level product genealogy

For each collection, identify which scenarios apply.

Then determine which fields are needed to support them.

**A field must not be omitted merely because it is unnecessary for the simplest manufacturing scenario.**

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

# 8. MODULE-SPECIFIC RESEARCH CHECKLIST

In addition to the general field discovery process, perform the following checks.

## 8.1 Traceability (`trace`)

Research:

- Forward and backward genealogy
- Parent-child material relationships
- Raw material to finished goods
- Serial and lot genealogy
- Material consumption genealogy
- Assembly component genealogy
- Split and merge events
- Rework genealogy
- Machine/operator/tool association
- Process parameter traceability
- Quality result association
- Supplier and customer lot references
- Recall and containment
- As-built versus as-planned records
- Genealogy corrections and reversals

Determine whether separate event, relationship and genealogy collections are required.

## 8.2 Quality Management (`qa`)

Research:

- Incoming quality
- In-process quality
- Final inspection
- First-off/last-off inspection
- Setup approval
- Inspection plans
- Inspection characteristics
- Sampling plans
- SPC
- Control charts
- Cp/Cpk
- MSA
- Gauge R&R references
- Non-conformance
- Rework
- Scrap
- Concessions/deviations
- Quality holds
- CAPA
- 8D
- PPAP/APQP references
- Calibration references
- Customer complaints

## 8.3 Performance (`perf`)

Research:

- OEE
- Availability
- Performance
- Quality
- TEEP
- Capacity utilization
- Planned production time
- Run time
- Downtime
- Planned downtime
- Minor stops
- Speed loss
- Quality loss
- Ideal cycle time
- Actual cycle time
- Target production
- Good/rejected quantity
- Machine states
- Downtime reason codes
- Shift/line/machine aggregations
- KPI calculation versioning
- Late-arriving event corrections

Separate raw events from calculated performance summaries.

## 8.4 Planning & Scheduling (`plan`)

Research:

- Demand
- Production orders
- Work order release
- Finite/infinite capacity scheduling
- Resource calendars
- Shift calendars
- Machine availability
- Tool availability
- Manpower availability
- Material constraints
- Routing constraints
- Setup/changeover matrices
- Dispatch lists
- Schedule versions
- Schedule freezing
- Rescheduling
- Priority rules
- Sequencing
- Bottleneck constraints
- Schedule adherence
- Planned versus actual execution

## 8.5 Material Management (`matl`)

Research:

- Material master references
- Warehouses
- Storage locations
- Bins
- Inventory balances
- Inventory transactions
- Goods receipt
- Material issue
- Material return
- Reservations
- Allocations
- WIP inventory
- Stock transfers
- Lot/serial tracking
- Quarantine
- Quality hold
- Shelf life
- FIFO/FEFO
- Inventory adjustments
- Cycle counting
- Backflushing
- ERP/WMS synchronization

## 8.6 Maintenance (`maint`)

Research:

- Preventive maintenance
- Corrective maintenance
- Predictive maintenance
- Breakdown maintenance
- Work requests
- Work orders
- Maintenance plans
- Maintenance schedules
- Maintenance tasks
- Spare parts
- Technician assignment
- Failure codes
- Root causes
- MTBF
- MTTR
- Asset history
- Condition monitoring
- Maintenance approvals
- Equipment availability
- Safety/permit references

## 8.7 Manpower Management (`man`)

Research:

- Employee master
- Attendance
- Shift roster
- Operator assignment
- Skills
- Certifications
- Training
- Qualification validity
- Authorization
- Skill matrix
- OJT
- Labor allocation
- Man-hours
- Multi-machine operation
- Operator replacement
- Contractor workforce
- Absenteeism
- Overtime
- Labor productivity

## 8.8 Tool Management (`tool`)

Research:

- Tool master
- Tool assemblies
- Dies
- Molds
- Fixtures
- Tool components
- Tool serial numbers
- Tool location
- Tool issue/return
- Tool life
- Stroke/cycle counts
- Preventive tool maintenance
- Tool breakdown
- Tool repair
- Tool calibration
- Tool replacement
- Tool availability
- Tool-to-machine compatibility
- Tool-to-product compatibility
- Tool history
- Tool inventory

## 8.9 Analytics (`ai`)

Research:

- KPI definitions
- KPI calculation rules
- Data sources
- Aggregation periods
- Analytics datasets
- Feature definitions
- Model versions
- Prediction outputs
- Anomaly events
- Recommendation outputs
- Alert thresholds
- Confidence scores
- Model execution history
- Data quality
- Lineage
- Model drift
- Prediction feedback
- User acknowledgement
- AI recommendation approval
- Human override

Separate transactional MES records, analytics results, and AI model metadata.

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

# 11. MANDATORY MONGODB DESIGN RULES

Follow the approved project conventions.

1. MongoDB Atlas is the target database.
2. Every document must be flat.
3. No nested documents.
4. No embedded arrays of objects.
5. No arrays unless expressly permitted by the approved data-modeling convention.
6. Use approved system IDs.
7. Use approved business-number conventions.
8. Every applicable foreign reference must include both ID and Name.
9. Use strategic denormalization for reference display fields.
10. Do not describe this model as strict normalization.
11. Use the approved collection and key naming conventions.
12. Preserve audit-field consistency.
13. Use MongoDB BSON data types.
14. Do not store numeric quantities as strings.
15. Do not store date/time values as strings.
16. Use UTC timestamps unless an approved convention specifies otherwise.
17. Distinguish event time from record creation time.
18. Distinguish current state from historical event records.
19. Do not embed repeated transaction lines inside parent records.
20. Use separate collections where one-to-many relationships require independent lifecycle management.

Example reference:

```javascript
"machineId": "1234567890123456",
"machineName": "CNC Machining Center 01"
```

Example audit fields:

```javascript
"createdAt": ISODate("2026-04-28T10:20:00.000+0000"),
"createdById": "1234567890123456",
"createdBy": "Rajesh Kumar",
"updatedAt": ISODate("2026-04-30T12:15:00.000+0000"),
"updatedById": "2345678901234567",
"updatedBy": "Priya Sharma"
```

Do not introduce `ObjectId()` if the approved convention requires string IDs.

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

# 18. FILE STORAGE

Store every collection design in:

`05_Design\Database\CollectionDesign\[Module Code]\[Collection Name].md`

The module code must be extracted from the approved collection name.

Examples:

`mes_trace_genealogy` → `CollectionDesign\trace\mes_trace_genealogy.md`

`mes_qa_inspectionResult` → `CollectionDesign\qa\mes_qa_inspectionResult.md`

`mes_perf_machineOee` → `CollectionDesign\perf\mes_perf_machineOee.md`

`mes_plan_productionSchedule` → `CollectionDesign\plan\mes_plan_productionSchedule.md`

`mes_matl_inventoryTransaction` → `CollectionDesign\matl\mes_matl_inventoryTransaction.md`

`mes_maint_maintenanceOrder` → `CollectionDesign\maint\mes_maint_maintenanceOrder.md`

`mes_man_employeeSkill` → `CollectionDesign\man\mes_man_employeeSkill.md`

`mes_tool_toolLife` → `CollectionDesign\tool\mes_tool_toolLife.md`

`mes_ai_predictionResult` → `CollectionDesign\ai\mes_ai_predictionResult.md`

These filenames are examples only. Use the exact names in the approved Collection Inventory.

Do not invent alternative collection names.

Create actual Markdown files when file creation is available.

If file creation is unavailable, provide the complete Markdown content and identify the intended path.

Do not claim that a file has been saved unless it has actually been created.

---

# 19. EXECUTION METHOD — IMPORTANT

Do not attempt to design all collections in one enormous response.

Follow a controlled, multi-stage process.

### Stage 1 — Inventory review

Read the approved Collection Inventory and identify all collections belonging to the  requested modules.

Produce the collection list, grouped by module.

### Stage 2 — Research

Research the relevant MES capabilities, manufacturing workflows and applicable standards for each module.

Produce a research summary before schema generation.

### Stage 3 — Dependency mapping

Identify the correct collection design order based on dependencies.

Prefer designing foundational collections before dependent transactional collections.

### Stage 4 — Collection design

Design collections individually or in small, logically related groups.

For each collection:

1. Research real-world functionality.
2. Identify manufacturing scenarios.
3. Derive candidate fields.
4. Validate field ownership.
5. Create the complete field dictionary.
6. Define business rules.
7. Define relationships.
8. Define indexes.
9. Perform missing-field analysis.
10. Perform cross-collection validation.
11. Create the Markdown specification.

### Stage 5 — Module review

After completing the collections in a module, perform a module-level review.

Check:

- Functional completeness
- Missing collections
- Missing fields
- Overlapping responsibilities
- Broken references
- Missing transaction-detail collections
- Missing history/event collections
- Inconsistent data types
- Inconsistent business rules
- Missing operational exception handling

### Stage 6 — Cross-module review

After all nine modules are designed, conduct a final cross-module consistency review.

Pay special attention to relationships between:

- Planning and Production Execution
- Production Execution and Traceability
- Traceability and Quality
- Quality and Material Management
- Maintenance and Performance
- Tool Management and Production Execution
- Manpower and Production Execution
- Performance and Analytics
- Industrial IoT events and MES transactions

Produce a consolidated gap report.

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
- Use MongoDB ObjectId if the project requires string IDs.
- Change approved collection names.
- Change approved field naming conventions.
- Create new collections without reporting them for approval.
- Introduce unrelated fields.
- Generate the final 10 sample documents.
- Declare a collection production-ready merely because its schema is syntactically valid.

---

# 21. FINAL INSTRUCTION

**The objective is not to generate MongoDB fields. The objective is to model actual manufacturing operations accurately and completely.**

For every collection, work in this sequence:

**Research real-world MES functionality → Understand manufacturing transactions → Identify normal and exception scenarios → Derive required data → Assign data to the correct collections → Design MongoDB fields → Validate completeness → Check cross-collection consistency.**

Do not reverse this sequence.

A collection design is acceptable only when its fields can be justified through real manufacturing requirements, established MES functionality, applicable standards, or explicitly documented project-specific assumptions.

Begin with Stage 1: review the Collection Inventory and identify the collections to be designed. Do not generate schemas until the research and dependency-mapping stages are complete.