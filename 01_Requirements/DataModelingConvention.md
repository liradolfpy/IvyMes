**FILE TYPE: GENERIC**

# Data Modelling Convention

**Version:** 1.3
**Status:** CURRENT
**Last Updated:** 2026-10-11

## 1. Purpose and source of settings

This reusable procedure covers data design and illustrative sample generation. Read `00_Project_Control/Project_Context.md` for the selected database, structure, ID format, reference-display policy, numeric constraints, sample count, date range, output format, and target tool. Those values are defined only in context. Follow GEN-001 in the master instructions for documentation workflow.

Use current requirements and collection specifications as the field-level authority. If a material rule is missing, record it as OPEN; do not invent a value to make a design appear complete. An explicit current user instruction may change the task parameters within its scope.

## 2. Model the requested entity

Define purpose, owner, scenarios, field names, concrete storage types, meanings, requiredness, defaults, references, uniqueness, indexes, lifecycle, and audit behavior. Include only relevant attributes and keep external identifiers separate from technical IDs and human-facing business numbers.

Use the document structure selected in context. If it requires flat documents, fields must be top-level scalars and repeated items or relationships must use separate records. A batch container in an insertion command is distinct from an array stored in a document. Use singular stems for scalar references; do not imply multiplicity by pluralizing a scalar key.

When context requires ID/display pairs, include both fields for business and audit references. Omit both or null both consistently for unavailable optional pairs. Preserve IDs for the same entity and use distinct IDs for different entities. Define whether copied display values are synchronized or retained as historical snapshots.

## 3. Retrieval, denormalization, and consistency

Document actual retrieval patterns and supporting indexes, including parent references where applicable. Separate queries and aggregation, including `$lookup` when supported, are allowed where appropriate; no universal lookup ban applies. Justify denormalization by the recorded query needs and define ordering, lifecycle, concurrency, and cross-record consistency.

## 4. Types and numeric rules

Match each field's concrete type and context constraints for the selected database. Define units, precision, rounding, valid ranges, and any scaled-integer conversion. Do not store numeric quantities as strings merely to simplify examples or change a type to ease sample generation.

For MongoDB shell output, use `ISODate()` for BSON dates, `NumberInt()` for Int32, and the constructor required by the specified numeric type. Use `Double(value)` for Double fields, including integral values. Apply any decimal-constructor restrictions from context. Other output formats use the serialization defined by context. Do not quote executable constructors. IDs, booleans, nulls, text, and dates must match the schema.

## 5. Sample generation and presentation

Generate the configured quantity and date range using realistic, varied scenarios. Use current collection-specific lifecycle values; do not impose shared states on unrelated entities. Respect documented quantity limits, totals, time ordering, reference validity, optional-field handling, and state-dependent rules.

Match the configured output format and target tool. Executable samples must be complete and usable without placeholder ellipses. Do not introduce unrelated fields, additional collections, or invented requirements. Label sample content illustrative.

Provide a schema summary, explain any copied display values and their consistency policy, and give a concise validation checklist when requested or required by context. Create additional artifacts only within the authorized task.

## 6. Review checklist

- Selected database and context settings applied, without importing another project's assumptions.
- Current specification followed: field spelling, types, requiredness, references, statuses, and audit groups.
- Configured quantity, date range, IDs, structure, and output format satisfied.
- Query patterns and indexes documented; copied display behavior and cross-record rules explicit.
- Quantities, dates, lifecycle states, and optional values internally consistent.
- Missing rules remain OPEN; sample content is clearly illustrative.

## Revision history

| Version | Date | Change | Status |
|---|---|---|---|
| 1.3 | 2026-10-11 | Keep modeling procedure generic; consolidate selected settings in context and resolve conflicting lookup wording. Earlier revisions remain in the project change log. | CURRENT |
