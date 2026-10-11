# Collection Design Specification Organization

Documentation workflow: GEN-001 in the master instructions applies; routine authorized work requires consistency review and recorded material changes, without separate sign-off. Historical approval records remain historical.

**Document ID:** REQ-DATA-ORG-001  
**Version:** 1.2  
**Status:** CURRENT  
**Updated By / Date:** Project Owner direction on 2026-10-11
**Last Updated:** 2026-10-11

## 1. Purpose

This requirement defines the mandatory folder and file organization for MongoDB collection design specifications. It implements DATA-004.

## 2. Required path and filename

Store each collection specification at:

`05_Design/Database/CollectionDesign/<moduleCode>/<collectionName>.md`

The `<moduleCode>` is the functional module code embedded in the collection name. The `<collectionName>` is the exact full MongoDB collection name, including its project-defined prefix and prescribed casing. Each collection has one specification file. Current specifications must not remain directly in the `CollectionDesign` root.

Example path pattern: use the exact collection name and its project-defined module code.

Sample documents are stored separately under `05_Design/Database/Collections/<moduleCode>/` as governed by `SampleDataOrganization.md` and DATA-005. This requirement defines collection specification storage only.

## 3. Module folders

Use module codes from `03_Functional_Structure/FunctionalStructureModules.md`; there is no universal module list. Module membership has one authoritative home in the functional structure; context references that mapping. Do not create aliases or duplicate a collection artifact across modules.

## 4. Status and change control

This folder organization is a current mandatory project requirement. New collection specifications and revisions shall follow it. When an existing specification is moved or renamed, update every project link or path reference in the same change and record the change in the project change log.

## Revision history

| Version | Date | Change | Status |
|---|---|---|---|
| Current | 2026-10-11 | Extract reusable rules; retain project settings separately. Earlier revision records are preserved in the project change log. | CURRENT |

Reference update — 2026-10-11: Project Context is the authoritative home for selected project settings; specialized module membership remains in functional structure. See the project change log.
