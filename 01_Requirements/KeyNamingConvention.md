# Key Naming Instructions

Documentation workflow: GEN-001 in the master instructions applies; routine authorized work requires consistency review and recorded material changes, without separate sign-off. Historical approval records remain historical.

**Version:** 0.6
**Status:** CURRENT
**Updated:** 2026-10-08

## Purpose

Use these conventions when designing keys and field names. BSON and MongoDB shell instructions apply only when MongoDB is selected. Project-specific rules define ID format and document structure. The goal is predictable, readable fields consistent with the selected project model. The example below illustrates flat denormalization and does not impose its ID format or structure on every project.

## Typical document pattern

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

## 1. General key style

- Use **lower camelCase** for field names: begin with a lowercase letter, then capitalize the first letter of each following word. Examples: `recordNo`, `relatedEntityName`, `createdAt`.
- Use clear, descriptive names that communicate the field's meaning without requiring knowledge of the implementation.
- Prefer established business terminology used consistently across the application.
- Use singular nouns for a field that holds one value, such as `relatedEntityName`.
- Use a grammatically singular entity name in every reference key. Pair singular `<entity>Id` with singular `<entity>Name`; do not pluralize the entity stem to imply a multi-reference. For irregular plurals, use the conventional singular form, such as `traceableEntitiesId` → `traceableEntityId`, `traceabilityIdentitiesName` → `traceabilityIdentityName`, and `workOrdersId` + `workOrdersName` → `workOrderId` + `workOrderName`.
- When DATA-001 flat modeling is selected, a repeated relationship uses multiple documents, each carrying one singular reference pair. Do not encode multiplicity by pluralizing a scalar key. If one document truly must refer to several entities, model the relationship as separate flat records or obtain a documented design decision; arrays remain prohibited when the project adopts DATA-001 flat modeling.
- Use a stable name consistently across every document in the same collection. Do not vary between synonyms such as `recordNo`, `recordNumber`, and `documentNo` for the same concept.
- Avoid spaces, hyphens, punctuation, and special characters in field names, except for MongoDB's conventional `_id` key.
- Do not use vague names such as `data`, `value`, `info`, or `misc` unless the field's purpose is made explicit by its context and documented.

## 2. Document identifier and business number

- Use MongoDB's reserved `_id` key as the unique document identifier.
- Store `_id` using the type and format defined in project-specific rules. The string-ID example above illustrates one possible convention and does not prescribe its length for every project.
- Give the record's human-readable business identifier a separate field, such as `recordNo`.
- Use a consistent business-number pattern within each collection. Include a meaningful prefix and date or sequence component only when required by the business convention, for example `REC-2026-000001`.
- Do not use `_id` and `recordNo` interchangeably: `_id` is the stable database identifier; `recordNo` is the readable business identifier.

## 3. Reference fields and paired display values

- Name reference fields with the referenced entity followed by `Id`, such as `relatedEntityId`, `customerId`, or `createdById`.
- Store reference IDs using the same project-defined type and format as their referenced primary IDs.
- When project rules require display pairs, include the matching display field alongside each reference ID. Usually use the matching entity name, for example `relatedEntityId` with `relatedEntityName`.
- When a conventional name is not appropriate, use a clear descriptor such as `relatedEntityDescription`, `salesOrderNo`, or another stable identifying value.
- When project rules require ID/display pairs, never include a reference ID without its matching display value. Apply the selected pairing rule to audit and business references.
- If an optional reference is unavailable, either omit both its ID and display fields or set both to `null`. Do not populate only one side of the pair.
- Reuse the same ID for the same referenced entity across documents. Keep its accompanying name or description consistent, unless the design intentionally preserves a historical snapshot; document that choice.
- Do not append `Id` to a name field or use ambiguous forms such as `relatedEntity` when it is unclear whether the value is an ID, name, or embedded object.

## 4. Audit field names

Use these standard audit keys consistently:

- `createdAt`: date and time when the document was created.
- `createdById`: project-defined ID of the user or process that created the document.
- `createdBy`: display name of the creator, paired with `createdById`.
- `updatedAt`: date and time when the document was most recently updated.
- `updatedById`: project-defined ID of the user or process that last updated the document.
- `updatedBy`: display name of the updater, paired with `updatedById`.

Use `createdAt`/`createdById`/`createdBy` together when creation metadata is available. Use `updatedAt`/`updatedById`/`updatedBy` together when update metadata is available. If the document has never been updated, follow the application's convention consistently: omit the update fields or set the complete update group to `null` where appropriate. Do not leave an ID without its matching display name.

## 5. Status and state fields

- Use `status` for the document's primary lifecycle state when the collection has one. Do not add `status` to a record that has no independent lifecycle; an event classification such as `eventType` may be sufficient when each record represents an event/transition.
- Define `status` independently for each collection. Do not impose one shared status vocabulary across unrelated business entities.
- For every collection that has `status`, document whether it is required, its collection-specific meaning, its allowed values, and the meaning of each value. Define state-dependent field requirements and permitted transitions only where supported by current requirements/decisions; mark unresolved rules as open rather than inferring them.
- Use a consistent set of readable status values within a collection, selecting only values that fit its domain. Use consistent capitalization and spelling; do not mix forms such as `InProgress`, `in_progress`, and `In-Progress` for the same state.
- For additional independent states, use a descriptive field name such as `qualityStatus` or `approvalStatus` rather than overloading `status`.
- Store status values as strings, not numeric codes, unless the application has an explicitly documented code system.

## 6. Dates, numbers, and other value types

Field names should make the value's purpose clear, while the BSON type should match the kind of data:

- Use names ending in `At` for event timestamps, such as `createdAt`, `updatedAt`, `approvedAt`, or `completedAt`.
- Store dates as BSON dates using `ISODate("YYYY-MM-DDTHH:mm:ss.sss+0000")` in MongoDB shell-style seed documents. Do not store timestamps as strings.
- Use `NumberInt()` for ordinary 32-bit integer counts and quantities, where appropriate. Every numeric field must specify a concrete BSON type as required by DATA-002; use a suitable numeric BSON type for values that need a wider range or fractional precision.
- Do not store numeric quantities as strings.
- Use strings for names, descriptions, status values, and business numbers; use the project-defined type for IDs.
- Use booleans for true/false attributes and `null` only when a value is genuinely unavailable.
- Use units explicitly when a quantity requires them. For example, pair `quantity` with `uom`, or use a domain-specific field such as `weightUom` where needed.

## 7. Flat document and scalar-key conventions

- When flat modeling is required by project-specific rules, represent each value with its own top-level key.
- Do not place fields inside nested documents or arrays when the collection's design requires flat documents.
- Keep each key focused on one value and one purpose.
- Do not create duplicate keys with slightly different names for the same information.
- For intentional denormalization, keep the reference ID and its display name/description as separate top-level fields.

## 8. Consistency and schema review

Before accepting a document or generating sample data, verify that:

- All documents in the collection use the same key spelling and casing for the same concepts.
- `_id` is a unique identifier matching the project-defined type and format, and `recordNo` is the separate business identifier when one is required.
- Reference IDs have paired display fields, including audit references, when required by project-specific rules.
- Related display values are consistent when they refer to the same entity.
- Date, numeric, string, Boolean, and null values use appropriate BSON types.
- The document follows the project-defined structure; where flat modeling is required it contains no embedded objects or arrays.
- Optional fields are handled consistently across documents.

## 9. Naming checklist

For every new key, ask:

1. Is the name clear and lower camelCase?
2. Does it use the collection's established terminology?
3. If it is a reference ID, is the corresponding name or description also present?
4. Does the BSON type match the value being stored?
5. Is this key consistent with the same concept in other documents and collections?
6. Is the key necessary and limited to one clear purpose?


## Revision history

| Version | Date | Change | Status |
|---|---|---|---|
| Current | 2026-10-11 | Extract reusable rules; retain project settings separately. Earlier revision records are preserved in the project change log. | CURRENT |
