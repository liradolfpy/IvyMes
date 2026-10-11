# Sample Collection Document Organization

Documentation workflow: GEN-001 / DEC-018 applies; routine authorized work requires consistency review and recorded material changes, without separate sign-off. Historical approval records remain historical.

**Document ID:** MES-REQ-DATA-SAMPLE-ORG-001  
**Version:** 1.1  
**Status:** CURRENT  
**Updated By / Date:** Project Owner direction on 2026-10-11
**Last Updated:** 2026-10-11

## 1. Purpose

This requirement defines the mandatory folder and file organization for MongoDB sample collection documents. It implements DATA-005 and DEC-017.

## 2. Required path and filename

Store each sample file at:

`05_Design/Database/Collections/<moduleCode>/<collectionName>.js`

The `<moduleCode>` is the functional module code embedded in the collection name. The `<collectionName>` is the exact full MongoDB collection name, including its `mes_` prefix and prescribed casing. Each collection has one sample file. Current sample collection files must not remain directly in the `Collections` root.

Example:

`mes_prod_workOrder` → `05_Design/Database/Collections/prod/mes_prod_workOrder.js`

## 3. Current module folders

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

Use the module code from the collection name as the folder name. Do not create ad hoc aliases or duplicate a collection's sample file in multiple module folders.

## 4. Sample status and change control

This folder organization is a current mandatory project requirement. The organization rule does not establish sample values as production data. Sample files shall retain the sample/demonstration status required by the Master Instructions with no implication of production readiness. When a sample file is moved or renamed, update every project link or path reference in the same change and record the change in the project change log.

## Revision history

| Version | Date | Change | Approval |
|---|---|---|---|
| 1.0 | 2026-10-08 | Established mandatory module-code subfolders for all sample collection documents under DATA-005 / DEC-017. | APPROVED |


Documentation update — 2026-10-11: Apply GEN-001 / DEC-018; remove separate documentation sign-off gates, retain open requirements and operational controls, and use current artifact status. See CHG-022.
