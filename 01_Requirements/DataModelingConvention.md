# Data Modelling Convention

**Status:** APPROVED  
**Version:** 1.1  
**Approved By / Date:** Project Owner on 2026-10-08  
**Last Updated:** 2026-10-08

## 1. Critical data-modeling requirement — strategically denormalized flat data

Design the `[CollectionName]` collection as a **flat, strategically denormalized data model** optimized for fast UI rendering and reporting in MongoDB without the need of a lookup operation.

### Absolute structure rule

There must be **no nested documents, embedded documents, or arrays of any kind**. Every field value must be a scalar (or a MongoDB scalar constructor such as `ISODate()`, `NumberInt()`, or `Double()`).

Do not create nested entity objects. Use separate flat ID, name/description, and other applicable fields.

### Repeated Items and Relationships

Because arrays and nested documents are prohibited, each repeated item or relationship shall be represented by its own flat document in an appropriate collection. A parent document shall not contain an embedded list of its child records.

Examples of this pattern include:

- One BOM component record for each component in a bill of material.
- One routing-operation record for each operation in a routing.
- One schedule-assignment record for each scheduled assignment.
- One inspection-characteristic or inspection-result record for each characteristic or result.
- One checklist-item or checklist-result record for each checklist item or result.
- One evidence-reference record for each evidence item or link when multiple items are associated with a record.

Each related record shall reference its parent using the project’s approved ID and display-value pairing convention. The exact collection boundaries and field definitions shall be specified in the relevant collection design; these examples do not approve particular collection names or fields.

Collection specifications shall describe how related records are retrieved and identify the indexes needed for the documented query patterns, including indexes on parent-reference fields where appropriate. Retrieval may require separate queries or aggregation. The flat-document rule does not prohibit `$lookup`, but collection designs shall not assume it is required for routine access. Specifications shall also identify any consistency, ordering, or lifecycle rules needed across the separate records.





### Data-modeling principles

1. Keep every document flat. No nested objects, embedded documents, or arrays.
2. Use strategic denormalization, not strict normalization. Intentionally copy referenced entity names or descriptions alongside IDs to avoid costly routine `$lookup` operations and support fast screens and reports.
3. Every included reference ID must have a matching flat name/description field, such as `relatedEntityId` + `relatedEntityName` or `relatedEntityDescription`.
4. Apply the same pairing rule to audit references: `createdById` requires `createdBy`, and `updatedById` requires `updatedBy`. Never include only one side. If an optional reference is unavailable, both fields may be null or both omitted.
5. Do not use MongoDB ObjectId values. 
6. Keep collection-specific attributes in the target collection and include useful reference pairs.
7. Reuse IDs across documents for the same entity and keep its paired name/description consistent. Use different IDs for distinct entities.
8. Each field must have a clear purpose. Do not add unrelated fields merely to enlarge the document.
9. This design is **flat, strategically denormalized data with reference integrity**, not a strictly normalized model. Decide whether copied names are synchronized after master-data changes or retained as historical snapshots.
10. Reference-key entity stems shall be grammatically singular for one scalar reference. Use the matching singular ID/name pair, such as `workOrderId` + `workOrderName`; do not use plural keys such as `workOrdersId` + `workOrdersName`. Irregular plurals take their standard singular form (`traceableEntitiesId` becomes `traceableEntityId`; `traceabilityIdentitiesId` becomes `traceabilityIdentityId`). Repeated references belong in separate flat relationship records under DATA-001, not pluralized scalar keys or arrays.

---

## 2. Output purpose

The documents will be directly uploaded/executed in **Studio 3T**. Do not generate strict RFC JSON. Use MongoDB/JavaScript constructors such as `ISODate("2026-04-30T06:53:53.699+0000")`, `NumberInt(10)`, and `Double(10.5)` where required by the approved BSON type. These expressions are intentionally not strict RFC JSON.

---

## 3. Required output format

Return exactly 10 documents as one MongoDB `insertMany()` statement executable in Studio 3T IntelliShell:

```javascript
db.[CollectionName].insertMany([
    { ... },
    { ... }
])
```

The code must execute without manual modification.

---

## 4. Collection data model

Design the collection named in the current request to fit that collection's purpose, project, and domain. 
Determine its appropriate fields from the user's requirements; 
Do not assume a particular entity type, workflow, or industry. 
Include only relevant identifiers, attributes, references, dates, status or lifecycle fields, and audit fields. 
For every reference ID included, also include its matching name or description as a flat field. 
Do not carry fields or examples over from an unrelated collection or project.
Only include fields that logically belong to the collection.
Do not add unnecessary fields merely to make the document larger.
---

## 5. Flat-document requirement

Each document should use flat scalar fields, for example:

```javascript
{
    "_id": "1000293847561029",
    "recordNo": "REC-2026-000001",
    "status": "InProgress",
    "relatedEntityId": "1122334455667788",
    "relatedEntityName": "Example Entity",
    "createdAt": ISODate("2026-04-28T10:20:00.000+0000"),
    "createdById": "5566778899001122",
    "createdBy": "Rajesh Kumar",
    "updatedAt": ISODate("2026-04-30T12:15:00.000+0000"),
    "updatedById": "6677889900112233",
    "updatedBy": "Priya Sharma"
}
```

This is an example only. Design the final schema appropriately. Do not put any fields inside another object or array.

---

## 6. MongoDB data types

- Use `ISODate()` for all date/time fields.
- Use `NumberInt()` for normal integer quantities and counts.
- Specify a concrete BSON type for every numeric field in its collection specification. Do not use the ambiguous type label `Decimal`.
- The Production Execution numeric fields covered by approved `DEC-010` use BSON `Double`. In executable `mongosh`/Studio 3T sample documents, use `Double(value)` for these fields so integral values are not inferred as BSON `Int32`.
- BSON `Double` uses binary floating-point and can approximate some decimal fractions. Collection specifications must define field units and any approved precision, rounding, and range rules. Do not invent those rules where requirements have not established them; record them as open issues.
- Do not use decimal constructors in project sample documents. Do not represent numeric quantities as strings. Scaled integer storage requires a separate approved decision defining scale, units, range, and conversion rules.
- Use strings for textual values and all IDs.
- Use `true` / `false` for Boolean values.
- Use `null` where an optional value is genuinely unavailable.
- Do not store dates or numeric quantities as strings.
- Do not quote MongoDB constructor expressions.

Examples: `"plannedQuantity": NumberInt(500)`, `"reportedQuantity": Double(125.75)`, and `"plannedStart": ISODate("2026-04-30T06:53:53.699+0000")`.

### Revision history

| Version | Date | Change | Status |
|---|---|---|---|
| 1.0 | 2026-10-07 | Established concrete BSON numeric-type requirements; approved BSON `Double` for the Production Execution fields previously labeled `Decimal` under DEC-010. | APPROVED |
| 1.1 | 2026-10-08 | Added singular entity-stem rules for scalar reference pairs and aligned repeated references with DATA-001 separate-record modeling. | APPROVED |

---

## 7. Realistic project data

Create exactly 10 different documents using realistic scenarios appropriate to the project, collection, and domain specified by the user. Use realistic identifiers, reference entities, attribute values, dates, and statuses as applicable. Do not merely change one identifier between documents.

---

## 8. Entity lifecycle

Where the target collection has a lifecycle, represent a realistic mixture of appropriate lifecycle states (for example, Draft, Active, OnHold, Completed, or Cancelled). Choose states that fit the domain. Statuses must agree with the other fields:

- Draft: normally no execution activity has occurred.
- Active or InProgress: relevant start or activation information normally exists.
- Completed: relevant completion information normally exists.
- OnHold: a hold reason is populated when the domain tracks one.
- Cancelled: any prior activity is reflected accurately.

---

## 9. Internal consistency

Ensure all documents are internally consistent according to their selected schema and domain:

- Domain-specific counts, totals, and quantities obey their logical limits and relationships.
- End dates occur after start dates; completion dates align with lifecycle status.
- Draft records normally have no execution activity.
- Progress or completion fields agree with the underlying data, when applicable.
- Related statuses and hold reasons agree with one another, when applicable.
- Every reference ID has a matching name/description, including creator and updater audit references.

---

## 10. Date requirements

Use dates between **April 2026 and September 2026**, with realistic schedules for the specified domain. All MongoDB dates must use `ISODate("YYYY-MM-DDTHH:mm:ss.sss+0000")`, never plain date strings.

---

## 11. ID requirements

The id naming convention is defined in '01_Requirements\DocumentIdAndReferenceConvention.md'.

---

## 12. Mandatory strategic denormalization — ID/name pairs

To avoid costly routine `$lookup` operations and support responsive user interfaces and reports, include referenced entities as flat scalar ID and display-value pairs in each document. This intentional duplication means the model is not strictly normalized.

Apply the pairing rule to every applicable reference in the target collection and domain, including creator and updater audit references. Use meaningful identifying descriptions where a conventional name does not exist. For unavailable optional references, either omit both fields or set both to null. Never include only one side.

---

## 13. Audit fields

Include practical, flat audit fields:

```javascript
"createdAt": ISODate("2026-04-28T10:20:00.000+0000"),
"createdById": "5566778899001122",
"createdBy": "Rajesh Kumar",
"updatedAt": ISODate("2026-04-30T12:15:00.000+0000"),
"updatedById": "6677889900112233",
"updatedBy": "Priya Sharma"
```

Whenever an audit user ID is present, its paired user display field is mandatory.

---

## 14. Schema consistency

All documents should follow the same overall schema. Optional fields may contain `null` where appropriate. Do not create nested objects or arrays to represent optional information. When an optional reference is null or omitted, apply that choice consistently to both its ID and descriptive field.

---

## 15. No nested documents — final validation

Before producing the answer, logically validate every document. Maximum document nesting depth is 1: the document itself, with every field containing a scalar value (including MongoDB scalar constructors). No nested documents and no arrays of any kind are allowed.

Allowed: `"quantity": NumberInt(100)`, `"reportedQuantity": Double(12.5)`, `"plannedStart": ISODate("...")`, `"status": "Released"`, `"active": true`, `"holdReason": null`.

Not allowed: nested entity or schedule objects, or arrays of child records or items.

---

## 16. Output requirements

Your response must contain:

### A. Schema summary

Provide a concise table with Field, MongoDB Type, and Purpose. Clearly mark fields denormalized for performance.

### B. Denormalization explanation

Briefly explain which referenced entity display values are intentionally copied into the target collection, why (avoid costly routine `$lookup` operations and improve UI speed), and how copied values are synchronized or retained as historical snapshots to preserve consistency.

### C. Validation checklist

Confirm:

- Exactly 10 documents.
- Every document is flat, with no nested documents and no arrays.
- Every reference ID, including `createdById` and `updatedById`, has its matching name/description field.
- IDs are custom 16-digit strings.
- Dates use `ISODate()`; integers use `NumberInt()`.
- Fractional numeric fields use the concrete BSON type in the approved specification; the Production Execution fields under DEC-010 use `Double(value)`.
- No dates or numeric quantities are stored as strings.
- Quantities, statuses, and dates are internally consistent.
- The entire `insertMany()` statement is directly executable in Studio 3T IntelliShell.

---

## 17. Important restrictions

Do not:

- Generate strict RFC JSON.
- Store dates or numeric quantities as strings.
- Use ObjectId or 24-character hexadecimal IDs; .
- Embed documents or create arrays of any kind.
- Leave any reference ID without its matching name/description, including audit user references.
- Use placeholders such as `"..."`, `"xxx"`, or `"ObjectIdHere"`.
- Generate fewer or more than 10 documents.
- Create additional collections or provide only a schema without actual documents.
- Use unrealistic placeholder data such as `"Entity 1"` or `"Record 1"`.

The final `insertMany()` statement must be directly copy-pasteable into Studio 3T IntelliShell and executable without manual modification.

> **The collection uses flat, strategically denormalized documents with an ID-plus-name/description pair for every reference. No nested documents or arrays are permitted.**
