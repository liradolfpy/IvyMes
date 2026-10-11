**FILE TYPE: GENERIC**

# MongoDB Collection Naming & Design Instructions

Documentation workflow: GEN-001 in the master instructions applies; routine authorized work requires consistency review and recorded material changes, without separate sign-off. Historical approval records remain historical.

**Version:** 1.4  
**Status:** CURRENT  
**Last Updated:** 2026-10-11

## 1. Overview & Scope
This document defines the mandatory naming conventions, structural guidelines, and file mapping rules for MongoDB collections.

---

## 2. General Naming Conventions

* **Casing:** Strictly use **lowerCamelCase** for all document keys and field names. Avoid spaces, hyphens, and special characters.
* **Singular Naming:** Strictly use **singular nouns** for collection names (e.g., `workOrder`, `workCenter`, `operation`) rather than plural forms.
* **Readability:** Names must explicitly reflect the domain context and purpose (e.g., avoiding vague terms like `data1` or `logs`).
* **App-Module Prefix Structure:** Every collection name must be prefixed using the format: `[Application]_[Module]_[Collection]`.
  * **Application Code:** The `<projectPrefix>` recorded in project context.
  * **Module Code:** Defined in `03_Functional_Structure/FunctionalStructureModules.md`.
  * **Pattern:** `<projectPrefix>_<moduleCode>_<singularEntityName>`.

## 3. ID Standards

Use the primary and reference ID type and format in `00_Project_Control/Project_Context.md` and `DocumentIdAndReferenceConvention.md`. Do not select an ID format from an unrelated project.

## 4. File-to-Collection Seeding Rule

- **Storage location:** Save each sample collection file in `05_Design\Database\Collections\<moduleCode>\`, using the module code embedded in its collection name.
- **File matching:** Create one dedicated file for each collection. The filename must match the collection name exactly, including capitalization, and use the project-defined sample extension.
- **File format:** Use the output format defined by project context. For a MongoDB shell workflow, constructors are permitted and strict RFC JSON is not required; for JSON export, use the selected JSON serialization format.
- **Dataset size:** Use the quantity defined in project context or the explicit current request.

## 5. Collection Design Specification Organization

- Store design specifications separately from sample-data files.
- Store each collection specification at `05_Design\Database\CollectionDesign\<moduleCode>\<collectionName>.md`.
- `<moduleCode>` is defined in the project functional structure and embedded in the collection name.
- `<collectionName>` exactly matches the full collection name, including its project prefix and prescribed casing.
- Keep the `CollectionDesign` root free of current collection specification files; it contains only module subfolders and current cross-module design guidance.
- The current module mapping and organization rule are maintained in `CollectionDesignOrganization.md` and mandated by DATA-004.

| Version | Date | Change | Approval |
|---|---|---|---|
| 1.1 | 2026-10-08 | Added the current module-code subfolder organization for collection design specifications under DATA-004. | APPROVED |
| 1.2 | 2026-10-08 | Added the current module-code subfolder organization for sample collection documents under DATA-005. | APPROVED |


Documentation update — 2026-10-11: Apply the reusable workflow in GEN-001. Consult project context for domain settings. Historical revisions are preserved in the project change log.

Reference update — 2026-10-11: Project Context is the authoritative home for selected project settings; specialized module membership remains in functional structure. See the project change log.
