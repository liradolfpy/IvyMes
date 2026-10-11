# MongoDB Collection Naming & Design Instructions

**Version:** 1.2  
**Status:** APPROVED  
**Last Updated:** 2026-10-08

## 1. Overview & Scope
This document defines the mandatory naming conventions, structural guidelines, and file mapping rules for MongoDB collections.

---

## 2. General Naming Conventions

* **Casing:** Strictly use **lowerCamelCase** for all document keys and field names. Avoid spaces, hyphens, and special characters.
* **Singular Naming:** Strictly use **singular nouns** for collection names (e.g., `workOrder`, `workCenter`, `operation`) rather than plural forms.
* **Readability:** Names must explicitly reflect the domain context and purpose (e.g., avoiding vague terms like `data1` or `logs`).
* **App-Module Prefix Structure:** Every collection name must be prefixed using the format: `[Application]_[Module]_[Collection]`.
  * **Application Code:** `mes` (Manufacturing Execution System)
  * **Module Code:** Derived from `03_Functional_Structure\FunctionalStructureModules.md` (e.g., "Production Execution" maps to the module code `prod`).
  * **Example:** `mes_prod_workOrder`

## 3. ID Standards (Strict Constraint)
* **No ObjectIds:** Do **NOT** use MongoDB’s default `ObjectId()` data type for `_id` fields or reference fields.
* **Custom Format:** Every document must use a **16-digit string ID** for its primary identifier (`_id`) and any relational string lookups (foreign keys). 
  * *Example:* `"_id": "1000293847561029"`

## 4. File-to-Collection Seeding Rule

- **Storage location:** Save each sample collection file in `05_Design\Database\Collections\<moduleCode>\`, using the module code embedded in its collection name.
- **File matching:** Create one dedicated file for each collection. The filename must match the collection name exactly, including capitalization, and use the `.js` extension.
  - *Example:* Collection name: `mes_prod_workOrder` → File path: `05_Design\Database\Collections\prod\mes_prod_workOrder.js`
- **File format:** Each file must contain JSON-compatible MongoDB documents. Because the documents may use MongoDB constructors such as `ISODate()` and `NumberInt()`, strict RFC JSON is not required.
- **Dataset size:** Each file must contain **exactly 10 realistic documents** for its collection, with varied scenarios appropriate to the project and domain.

## 5. Collection Design Specification Organization

- Store design specifications separately from sample-data files.
- Store each collection specification at `05_Design\Database\CollectionDesign\<moduleCode>\<collectionName>.md`.
- `<moduleCode>` is the module code in the collection name and must match one of: `prod`, `trace`, `qa`, `perf`, `plan`, `matl`, `maint`, `man`, `tool`, or `ai`.
- `<collectionName>` must exactly match the complete MongoDB collection name, including its `mes_` prefix and prescribed casing. For example, `mes_prod_workOrder` is stored at `05_Design\Database\CollectionDesign\prod\mes_prod_workOrder.md`.
- Keep the `CollectionDesign` root free of current collection specification files; it contains only module subfolders and approved cross-module design guidance.
- The approved module mapping and organization rule are maintained in `CollectionDesignOrganization.md` and mandated by DATA-004 / DEC-016.

| Version | Date | Change | Approval |
|---|---|---|---|
| 1.1 | 2026-10-08 | Added the approved module-code subfolder organization for collection design specifications under DATA-004 / DEC-016. | APPROVED |
| 1.2 | 2026-10-08 | Added the approved module-code subfolder organization for sample collection documents under DATA-005 / DEC-017. | APPROVED |
