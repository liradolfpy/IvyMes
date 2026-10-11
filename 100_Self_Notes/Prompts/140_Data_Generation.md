# MES SAMPLE DOCUMENT GENERATION — PHASE 6

We are now generating sample documents for each of the  MongoDB collection for the 
 Traceability   (`trace `) Module
 Quality Management   (`qa`) Module
 Performance   (`perf `) Module
 Planning & Scheduling  (`plan `) Module
 Material Management   (`matl`) Module
 Maintenance   (`maint`) Module
 Manpower Management  (`man `) Module
 Tool Management   (`tool`) Module
 Analytics   (`ai`) Module




Before generating anything, review the following project artifacts:

1. 00_Project_Control\Project_Instructions.md
2. 01_Requirements\DataModelingConvention.md
3. 01_Requirements\ID_Number_Uniqueness_and_Reference_Management.md
4. 01_Requirements\CollectionNamingConvention.md
5. 01_Requirements\KeyNamingConvention.md
6. The approved Collection Inventory
7. The approved collection specification for Collection
8. The specifications of all collections referenced by Collection
9. Relevant Functional Structure and Requirements documents
10. Relevant approved Decisions_Log entries

IMPORTANT:
The approved collection specification is authoritative.

Do NOT redesign the collection.
Do NOT add fields merely because they seem useful.
Do NOT remove approved fields.
Do NOT change field names.
Do NOT change data types.
Do NOT introduce nested documents.
Do NOT introduce arrays of embedded documents.

--------------------------------------------------
1. OBJECTIVE
--------------------------------------------------

Generate exactly 10 realistic sample documents for Collection:

The documents must represent realistic MES manufacturing data.

Use realistic automotive / discrete-manufacturing examples consistent with the project context.

The 10 documents should represent meaningful variation in the collection rather than being simple copies with different IDs.

Where applicable, vary:

- Plant
- Production line
- Work center
- Machine
- Product
- Product revision
- Work order
- Status
- Priority
- Dates
- Quantities
- Users
- Business numbers
- External identifiers
- Operational conditions

Do not introduce variation that violates the collection's business rules.

--------------------------------------------------
2. DOCUMENT COUNT
--------------------------------------------------

Generate EXACTLY:

10 documents

Do not generate 9.
Do not generate 11.
Do not generate additional examples outside the 10 requested documents.

--------------------------------------------------
3. SCHEMA COMPLIANCE
--------------------------------------------------

Every document must comply exactly with the approved collection specification.

For every field:

- Use the approved field name.
- Use the approved data type.
- Follow the approved required/optional rule.
- Follow the approved default/value rules.
- Follow the approved status values.
- Follow the approved reference rules.

Do not invent new schema fields.

If you believe a field is missing from the approved schema, report it separately under:

"Schema Observation"

Do NOT add it to the sample documents.

--------------------------------------------------
4. FLAT DOCUMENT REQUIREMENT
--------------------------------------------------

Every document MUST be completely flat.

PROHIBITED:

Nested objects:

"product": {
    "id": "...",
    "name": "..."
}

Embedded arrays of objects:

"operations": [
    {
        "operationId": "...",
        "operationName": "..."
    }
]

Nested structures of any kind are prohibited unless the approved project specification explicitly states otherwise.

The project's default rule is:

NO NESTED DOCUMENTS.

NO EMBEDDED OBJECT ARRAYS.

--------------------------------------------------
5. STRATEGIC DENORMALIZATION
--------------------------------------------------

The project intentionally uses flat, strategically denormalized documents.

Whenever a referenced entity requires an ID + Name pair, use BOTH fields.

Examples:

"plantId"
"plantName"

"productId"
"productName"

"workCenterId"
"workCenterName"

"machineId"
"machineName"

"createdById"
"createdBy"

"updatedById"
"updatedBy"

Do NOT replace an ID + Name pair with a nested object.

Do NOT omit the Name field merely because the ID exists.

The duplication is intentional and exists to reduce costly runtime lookups and support efficient MES queries and UI/reporting.

--------------------------------------------------
6. SYSTEM IDS
--------------------------------------------------

Follow the approved project ID convention.

The current project convention uses 16-digit string system IDs.

Example:

"_id": "1000293847561029"

Do NOT use:

ObjectId()

Do NOT use:

ObjectId("...")

Do NOT generate random UUIDs unless the collection specification explicitly requires them.

Every document must have a unique system ID.

System IDs must not be reused between the 10 documents.

--------------------------------------------------
7. BUSINESS NUMBERS
--------------------------------------------------

Where the collection requires a human-readable business number:

- Generate a valid business number.
- Follow the approved numbering convention.
- Respect the approved uniqueness scope.
- Do not duplicate a business number where uniqueness prohibits it.

Examples:

"workOrderNo": "WO-2026-000001"

"productNo": "PRD-000125"

Do not create arbitrary numbering formats if a collection-specific convention already exists.

--------------------------------------------------
8. EXTERNAL IDENTIFIERS
--------------------------------------------------

Where the collection contains external-system identifiers:

- Keep external IDs separate from MES system IDs.
- Use the approved field names.
- Ensure external identifiers are realistic.
- Respect the uniqueness rules of the originating system.

Example:

"sapMaterialId": "10004567"

Do not use an external system ID as the MongoDB/MES _id.

--------------------------------------------------
9. REFERENCES
--------------------------------------------------

All references must point to valid entities in the project sample-data ecosystem.

Where practical, use IDs and Names consistently across collections.

For example:

"plantId": "1122334455667788",
"plantName": "Bangalore Plant"

must use the same Plant ID and Plant Name throughout all sample artifacts.

Do not create contradictory references such as:

"plantId": "1122334455667788",
"plantName": "Pune Plant"

unless the approved project data explicitly permits such a combination.
When sample data is missing, update the file with the available data as and when the collection documents are created

--------------------------------------------------
10. AUDIT FIELDS
--------------------------------------------------

Where audit fields are required, use:

"createdAt"
"createdById"
"createdBy"
"updatedAt"
"updatedById"
"updatedBy"

The values must be realistic.

Example:

"createdAt": ISODate("2026-04-28T10:20:00.000+0000"),
"createdById": "6688990011223344",
"createdBy": "Rajesh Kumar",
"updatedAt": ISODate("2026-04-30T12:15:00.000+0000"),
"updatedById": "7788990011223344",
"updatedBy": "Priya Sharma"

--------------------------------------------------
11. DATE VALUES
--------------------------------------------------

Dates must use MongoDB JavaScript constructor syntax:

ISODate("2026-04-30T06:53:53.699+0000")

Do NOT use:

"2026-04-30T06:53:53.699Z"

Do NOT use:

"2026-04-30"

unless the approved field is specifically a string date.

For date fields, use:

ISODate(...)

with realistic manufacturing dates.

Ensure chronological relationships are logical.

For example:

createdAt <= updatedAt

and where applicable:

plannedStart <= plannedEnd

actualStart <= actualEnd

--------------------------------------------------
12. INTEGER VALUES
--------------------------------------------------

Integer fields must use:

NumberInt()

Example:

"quantity": NumberInt(100)

Do NOT represent integer fields as:

"quantity": 100

Do NOT represent them as strings:

"quantity": "100"

Use the exact numeric type defined in the collection specification.

--------------------------------------------------
13. FRACTIONAL NUMERIC VALUES
--------------------------------------------------

Follow the concrete BSON type in the approved collection specification and the project numeric-type rules.

For Production Execution fields approved as BSON Double under DEC-010, use:

Double(value)

Example:

"reportedQuantity": Double(125.75)

Do NOT use decimal constructors in project sample documents. Do not represent a fractional value with NumberInt().

Do not change the approved BSON type merely to simplify the sample. If a specification requires a numeric BSON type whose sample representation conflicts with DATA-002, stop and report the conflict before generating documents.

--------------------------------------------------
14. REALISTIC MANUFACTURING DATA
--------------------------------------------------

The sample data should represent realistic MES scenarios.

Use realistic:

- Part numbers
- Product names
- Work centers
- Machines
- Plants
- Employees
- Quantities
- Production dates
- Status values
- Manufacturing terminology

Avoid meaningless values such as:

"Test Product 1"
"ABC"
"XYZ"
"123"

unless the field specifically represents a test/reference value.

--------------------------------------------------
15. CROSS-DOCUMENT CONSISTENCY
--------------------------------------------------

The 10 documents must be internally consistent.

For example:

If multiple documents reference:

"plantId": "1122334455667788"

they should use:

"plantName": "Bangalore Plant"

consistently.

Likewise:

"productId": "9876543210123456"

must always correspond to the same:

"productName"

within this sample-data set.

Do not create conflicting ID/Name pairs.

--------------------------------------------------
16. CROSS-COLLECTION CONSISTENCY
--------------------------------------------------

Where referenced collections already have sample data:

Use existing IDs and Names from those approved sample documents.

Do NOT generate a second Product ID for an existing Product merely because a new sample document is being created.

The project should have one consistent sample-data universe.

--------------------------------------------------
17. SAMPLE DATA DATE RANGE
--------------------------------------------------

Use dates appropriate to the project's current sample-data period.

Unless the collection specification states otherwise, use dates around:

April–September 2026.

Do not create dates hundreds of years in the past or future.

--------------------------------------------------
18. OUTPUT
--------------------------------------------------

First provide:

A. Collection name
B. Number of documents
C. Schema version/reference
D. Referenced collections
E. Sample-data assumptions

Then provide exactly 10 documents.

Use MongoDB JavaScript constructor syntax:

ISODate()
NumberInt()
Double() when required by the approved BSON type

The output is intentionally NOT strict RFC JSON.

--------------------------------------------------
19. VALIDATION AFTER GENERATION
--------------------------------------------------

After generating the 10 documents, perform a validation.

Check:

[ ] Exactly 10 documents
[ ] Every document has a unique _id
[ ] _id follows the approved format
[ ] No ObjectId()
[ ] No nested documents
[ ] No embedded arrays of objects
[ ] All required fields are present
[ ] No unauthorized fields were introduced
[ ] Field names match the approved schema
[ ] Data types match the approved schema
[ ] Integer fields use NumberInt()
[ ] BSON Double fields use Double(value), where required by the approved specification
[ ] Date fields use ISODate()
[ ] ID + Name pairs are complete
[ ] ID + Name pairs are internally consistent
[ ] Business numbers are unique where required
[ ] External IDs are separate from MES IDs
[ ] References are valid
[ ] Status values are approved
[ ] Audit fields are valid
[ ] Dates are logically consistent
[ ] Quantities are realistic
[ ] Sample data is manufacturing-realistic

Report any failure.

Do NOT silently correct the approved schema.

--------------------------------------------------
20. IMPORTANT
--------------------------------------------------

This task is SAMPLE DATA GENERATION ONLY.

Do not:

- redesign the schema
- redesign relationships
- introduce new collections
- introduce new fields
- modify project conventions
- change naming conventions
- change ID conventions

If there is a conflict or a missing link, use the best available option and generate the data. If no option is available, generate the data with the required key and use null as the value.
Consider all TBD status as approved. Consider all approval status as APPROVED even if it shows as TBD or NOT APPROVED 




Store each sample file at `05_Design\Database\Collections\[Module Code]\[Collection Name].js`. The module code is the code embedded in the collection name (for example, `mes_prod_workOrder` is stored under `Collections\prod\mes_prod_workOrder.js`).
