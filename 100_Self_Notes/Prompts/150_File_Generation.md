**FILE TYPE: PROJECT SPECIFIC**

# MES SAMPLE DATA ARTIFACT GENERATION — PHASE 12

We have completed the approved sample-document generation for:

[COLLECTION_NAME]

The 10 sample documents generated in Phase 6 are authoritative for this task.

Do NOT generate new sample data.

Do NOT change the values.

Do NOT change the schema.

Do NOT change IDs.

Do NOT change business numbers.

Do NOT change dates.

Do NOT change references.

Your task is to create TWO synchronized sample-data artifacts from the approved 10 documents.

==================================================
ARTIFACT 1 — HUMAN-READABLE MARKDOWN
==================================================

Create:

10_Sample_Documents\
[COLLECTION_NAME]\
[COLLECTION_NAME]_Sample_Documents.md

Purpose:

This file is the human-readable reference representation of the 10 approved sample documents.

It must contain:

1. Collection name
2. Purpose
3. Source collection specification
4. Sample-data version
5. Number of documents
6. Sample-data assumptions
7. Referenced collections
8. The 10 complete sample documents
9. Validation summary

Preserve the complete document structure.

Do not omit fields merely to make the Markdown shorter.

Use code blocks for the actual MongoDB document examples.

Use the project's MongoDB constructor syntax:

ISODate()
NumberInt()

The Markdown file should therefore show the documents exactly as they are intended to exist in the MongoDB sample-data script.

==================================================
ARTIFACT 2 — EXECUTABLE MONGODB SCRIPT
==================================================

Create:

10_Sample_Documents\
[COLLECTION_NAME]\
[COLLECTION_NAME]_InsertMany.js

The script must be directly usable in MongoDB/Studio 3T IntelliShell.

Use:

db.[COLLECTION_NAME].insertMany([
    {...},
    {...}
])

The script must contain exactly the same 10 documents as the Markdown artifact.

Do not create different values between the two artifacts.

==================================================
DATA CONSISTENCY RULE
==================================================

Artifact 1 and Artifact 2 must be generated from the SAME authoritative sample-document set.

The following must match exactly between both files:

- _id
- business numbers
- field names
- field values
- dates
- quantities
- references
- ID + Name pairs
- status values
- audit fields
- external IDs

Only formatting may differ.

==================================================
MONGODB CONSTRUCTOR RULES
==================================================

The executable script is NOT strict RFC JSON.

Use MongoDB JavaScript expressions.

Dates:

ISODate("2026-04-30T06:53:53.699+0000")

Integers:

NumberInt(10)

If another MongoDB constructor is required by the approved schema, use the appropriate constructor.

Do NOT convert:

ISODate()

to a quoted string.

Do NOT convert:

NumberInt(10)

to:

10

==================================================
NO NESTED DOCUMENTS
==================================================

Maintain the project's flat-document rule.

No nested documents.

No embedded arrays of objects.

The only structures expected in the script should be:

db.collection.insertMany([
    document,
    document,
    document
])

Each document itself must remain flat.

==================================================
ID + NAME RULE
==================================================

Maintain the mandatory ID + Name convention.

For example:

"productId": "9876543210123456",
"productName": "EV Transmission Housing"

and:

"plantId": "1122334455667788",
"plantName": "Bangalore Plant"

Audit:

"createdById": "6688990011223344",
"createdBy": "Rajesh Kumar"

Do not remove the Name field.

Do not replace the pair with a nested object.

==================================================
FILE CONTENT — MARKDOWN
==================================================

The Markdown file should follow this structure:

# [COLLECTION_NAME] — Sample Documents

## 1. Collection

[COLLECTION_NAME]

## 2. Purpose

[Collection purpose]

## 3. Source Specification

[Reference to approved collection specification]

## 4. Sample Data Version

[Version]

## 5. Number of Documents

10

## 6. Referenced Collections

[List]

## 7. Sample Documents

### Document 01

```javascript
{
    ...
}

The collections to be stored in the `05_Design\Database\BsonFiles\` location