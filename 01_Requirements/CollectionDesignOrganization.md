# Collection Design Specification Organization

**Document ID:** MES-REQ-DATA-ORG-001  
**Version:** 1.0  
**Status:** APPROVED  
**Approved By / Date:** Project Owner on 2026-10-08  
**Last Updated:** 2026-10-08

## 1. Purpose

This requirement defines the mandatory folder and file organization for MongoDB collection design specifications. It implements DATA-004 and DEC-016.

## 2. Required path and filename

Store each collection specification at:

`05_Design/Database/CollectionDesign/<moduleCode>/<collectionName>.md`

The `<moduleCode>` is the functional module code embedded in the collection name. The `<collectionName>` is the exact full MongoDB collection name, including its `mes_` prefix and prescribed casing. Each collection has one specification file. Current specifications must not remain directly in the `CollectionDesign` root.

Example:

`mes_prod_workOrder` → `05_Design/Database/CollectionDesign/prod/mes_prod_workOrder.md`

Sample documents are stored separately under `05_Design/Database/Collections/<moduleCode>/` as governed by `SampleDataOrganization.md` and DATA-005 / DEC-017. This requirement defines collection specification storage only.

## 3. Approved module folders

| Module code | Module |
|---|---|
| `prod` | Production Execution |
| `trace` | Traceability |
| `qa` | Quality Management |
| `perf` | Performance |
| `plan` | Planning & Scheduling |
| `matl` | Material Management |
| `maint` | Maintenance |
| `man` | Manpower Management |
| `tool` | Tool Management |
| `ai` | Analytics |

Use the module code from the collection name as the folder name. Do not create ad hoc aliases or duplicate a collection specification in more than one module folder.

## 4. Status and change control

This folder organization is an approved mandatory project requirement. New collection specifications and revisions shall follow it. When an existing specification is moved or renamed, update every project link or path reference in the same change and record the change in the project change log.

## Revision history

| Version | Date | Change | Approval |
|---|---|---|---|
| 1.0 | 2026-10-08 | Established mandatory module-code subfolders for all collection design specifications under DATA-004 / DEC-016. | APPROVED |
