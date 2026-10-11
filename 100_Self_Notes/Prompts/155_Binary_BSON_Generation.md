# MES STUDIO 3T SAMPLE DATA GENERATION — PHASE 13

We are now preparing the approved sample documents for direct insertion into MongoDB using Studio 3T.

Collection:

[COLLECTION_NAME]

The authoritative source is:

10_Sample_Documents\
[COLLECTION_NAME]\
[COLLECTION_NAME]_Sample_Documents.md

and/or the approved Phase 6 sample-document set.

The Phase 6 sample documents are the SOURCE OF TRUTH.

Do NOT generate new sample data.

Do NOT modify the schema.

Do NOT modify any values.

Do NOT modify IDs.

Do NOT modify business numbers.

Do NOT modify references.

Do NOT modify dates.

==================================================
1. OUTPUT FILE
==================================================

Create:

10_Sample_Documents\
[COLLECTION_NAME]\
[COLLECTION_NAME]_InsertMany.js

The file must be directly executable in Studio 3T IntelliShell.

==================================================
2. INSERT FORMAT
==================================================

Use:

db.[COLLECTION_NAME].insertMany([
    {
        ...
    },
    {
        ...
    }
]);

There must be exactly 10 documents inside the array.

Do not use:

db.[COLLECTION_NAME].save()

Do not use:

insert()

Do not use:

replaceOne()

Do not use:

updateOne()

Do not use:

bulkWrite()

unless specifically requested.

The objective is a clean initial sample-data load.

==================================================
3. MONGODB JAVASCRIPT CONSTRUCTORS
==================================================

The file is MongoDB JavaScript syntax and is intentionally NOT strict RFC JSON.

Dates MUST use:

ISODate("2026-04-30T06:53:53.699+0000")

Integers MUST use:

NumberInt(10)

Example:

"quantity": NumberInt(10)

Do NOT convert these to quoted strings.

Do NOT convert:

NumberInt(10)

into:

10

Do NOT convert:

ISODate("...")

into:

"2026-04-30T..."

==================================================
4. SYSTEM IDS
==================================================

Use the exact approved 16-digit string IDs.

Example:

"_id": "1000293847561029"

Do NOT use ObjectId().

Do NOT regenerate IDs.

Do NOT change IDs merely to make them look more realistic.

Every document must have a unique _id.

==================================================
5. BUSINESS NUMBERS
==================================================

Use the exact business numbers from the approved sample documents.

Do not regenerate sequences.

Do not change prefixes.

Do not change year values.

Do not change numbering.

==================================================
6. REFERENCES
==================================================

Use the exact approved reference IDs and corresponding names.

Example:

"plantId": "1122334455667788",
"plantName": "Bangalore Plant"

Do not create new reference IDs.

Do not alter a referenced entity's name.

Do not substitute a different Plant/Product/WorkCenter/etc.

==================================================
7. FLAT DOCUMENT STRUCTURE
==================================================

Every document must remain flat.

No nested documents.

No arrays of objects.

For example, this is prohibited:

"product": {
    "id": "...",
    "name": "..."
}

Use:

"productId": "...",
"productName": "..."

Similarly:

"workCenterId": "...",
"workCenterName": "..."

==================================================
8. AUDIT FIELDS
==================================================

Preserve the approved audit fields exactly.

Example:

"createdAt": ISODate("2026-04-28T10:20:00.000+0000"),
"createdById": "6688990011223344",
"createdBy": "Rajesh Kumar",
"updatedAt": ISODate("2026-04-30T12:15:00.000+0000"),
"updatedById": "7788990011223344",
"updatedBy": "Priya Sharma"

==================================================
9. FIELD ORDER
==================================================

Preserve the field ordering from the approved sample-document artifact wherever practical.

Recommended order:

1. _id
2. Business number
3. Core business fields
4. Organizational references
5. Product/material references
6. Operational fields
7. Status
8. External identifiers
9. Audit fields

Do not reorder fields in a way that makes the sample data difficult to compare against the approved artifact.

==================================================
10. COMMENTS
==================================================

Keep comments minimal.

The generated file should primarily contain executable MongoDB JavaScript.

Do not insert explanatory comments between documents.

A short header is acceptable:

// MES Sample Data
// Collection: WorkOrder
// Sample Version: 1.0

==================================================
11. SYNTAX VALIDATION
==================================================

Before finalizing, verify that the script has:

[ ] Correct db.collection.insertMany() syntax
[ ] Opening [ and closing ]
[ ] Correct commas between documents
[ ] Correct commas between fields
[ ] No trailing syntax errors
[ ] Balanced braces
[ ] Balanced brackets
[ ] Correct quoted field names
[ ] Correct string quoting
[ ] Valid ISODate() expressions
[ ] Valid NumberInt() expressions
[ ] Exactly 10 documents

==================================================
12. SCHEMA VALIDATION
==================================================

Verify:

[ ] Every field exists in the approved collection schema
[ ] No approved required field is missing
[ ] No unauthorized field has been added
[ ] Data types match the approved schema
[ ] ID + Name pairs are present where required
[ ] External IDs remain separate from MES IDs
[ ] Business-number rules are respected
[ ] Status values are valid
[ ] References are valid
[ ] Audit fields are valid
[ ] Dates are logically consistent

==================================================
13. CROSS-COLLECTION VALIDATION
==================================================

For every referenced entity, verify against the project's approved sample-data set where available.

For example:

productId → Product
plantId → Plant
workCenterId → WorkCenter
machineId → Machine
employeeId → Employee

Verify that:

<entity>Id

and:

<entity>Name

refer to the same entity.

Do not silently create missing master records.

If a required referenced sample entity does not exist, report it separately.

==================================================
14. DATA QUALITY VALIDATION
==================================================

Check that the 10 records:

- Are realistic
- Are not duplicates
- Have meaningful variation
- Follow the business rules
- Use realistic manufacturing terminology
- Have logically consistent dates
- Have reasonable quantities
- Have valid statuses
- Maintain consistent references

==================================================
15. IMPORTANT: DO NOT CALL THESE FILES BSON
==================================================

The output file uses:

ISODate()
NumberInt()

and is therefore MongoDB JavaScript syntax.

It is intended for:

Studio 3T IntelliShell

or an equivalent MongoDB shell-compatible environment.

The file should therefore use the extension:

.js

Recommended filename:

[COLLECTION_NAME]_InsertMany.js

Do not give the file a .json extension.

Do not give the file a .bson extension.

A true BSON binary file is a different artifact and is NOT being generated in this task.

==================================================
16. FINAL OUTPUT
==================================================

Provide:

1. The generated .js file.
2. A concise validation report containing:
   - Collection
   - Number of documents
   - Number of fields per document
   - ID validation result
   - Reference validation result
   - Constructor validation result
   - Flat-document validation result
   - Studio 3T syntax validation result

Do not modify the approved sample data during validation.

If a problem is found, report it and correct only the generated script formatting/syntax where possible.

If the problem originates in the approved schema or sample data, STOP and report it rather than inventing a correction.