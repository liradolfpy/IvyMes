**FILE TYPE: PROJECT SPECIFIC — REFERENCE-ONLY PROMPT**

# Stage 2 — Collection and Manufacturing Research

Read `121_Collection_Design_Common_Rules.md` first. Research precedes field design. Do not create or modify collection schemas in this stage.

## Inputs

- Selected module(s): `[MODULE_CODE(S)]`
- Collection(s) or functional capabilities: `[COLLECTIONS_OR_CAPABILITIES]`
- Stage 1 verified scope: `[STAGE_1_OUTPUT]`

## Read

- Current Project Context, Decisions Log, Functional Structure modules/sections, Project Scope Boundary, and relevant requirements.
- Current Collection Inventory and Stage 1 scope output.
- `05_Design/Database/Dependancy_Map.md` for known dependency/open-question context.
- Applicable existing collection specifications, architecture/interface artifacts, and project source materials.

## Research protocol

Use the research instructions in the sections below. Research publicly documented functionality, workflows, configuration concepts, APIs, technical manuals, and implementation guidance from relevant commercial MES/MOM products and applicable standards. These are functional references only: do not copy proprietary schemas or claim access to internal vendor database structures. Prefer primary/official sources when available. Cite sources with direct links and explain what each source supports. If a source is inaccessible, state that; do not fabricate evidence.

For every selected collection/capability, establish the real manufacturing process, record-creation/update triggers, users and systems, operator/supervisor/planner/quality/maintenance needs, external integrations, history/retention considerations, audit/quality/traceability concerns, exceptions, and operational scenarios.

For Production Execution (`prod`), explicitly cover the current functional sections such as Production Order Release, Work Order Management, Dispatch, Operation Sequencing, Electronic Work Instructions, Operator Start/Pause/Complete, Production Reporting, Exception and Downtime Capture, Rework and Scrap Handling, Shift Handover, and Andon/Escalations. Verify the exact current section names in FunctionalStructureSections.md. For the other modules, use the corresponding current functional sections and the inherited module-specific checklist below. Do not omit `prod` merely because it is absent from the legacy checklist headings.

Use the following inherited research checklists and scenario requirements, adapting them to the selected modules and collection scope:

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

## Output

Provide the researched business concepts, workflows, transaction triggers, normal and exception scenarios, stakeholder/integration needs, evidence-backed candidate information categories, source citations, and unresolved questions. Keep candidate fields explicitly provisional and trace them to evidence or project requirements. Do not write a final field dictionary, collection specification, sample documents, inventory changes, or decisions.

## Completion handoff

Provide a concise research package that Stage 3 can use to verify dependencies and that Stage 4 can use to justify field-level design. Separate verified evidence, project facts, assumptions, and OPEN items.

