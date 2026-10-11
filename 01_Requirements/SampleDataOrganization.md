# Sample Collection Document Organization

Documentation workflow: GEN-001 in the master instructions applies; routine authorized work requires consistency review and recorded material changes, without separate sign-off. Historical approval records remain historical.

**Document ID:** REQ-DATA-SAMPLE-ORG-001  
**Version:** 1.2  
**Status:** CURRENT  
**Updated By / Date:** Project Owner direction on 2026-10-11
**Last Updated:** 2026-10-11

## 1. Purpose

This requirement defines the mandatory folder and file organization for MongoDB sample collection documents. It implements DATA-005.

## 2. Required path and filename

Store each sample file at:

`05_Design/Database/Collections/<moduleCode>/<collectionName>.<sampleExtension>`

The `<moduleCode>` is the functional module code embedded in the collection name. The `<collectionName>` is the exact full MongoDB collection name, including its project-defined prefix and prescribed casing. Each collection has one sample file. Current sample collection files must not remain directly in the `Collections` root.

Example path pattern: use the exact collection name and its project-defined module code.

The sample extension and serialization format are defined by project-specific rules.

## 3. Module folders

Use module codes from `03_Functional_Structure/FunctionalStructureModules.md`; there is no universal module list. Preserve any existing project mapping in project-specific rules. Do not create aliases or duplicate a collection artifact across modules.

## 4. Sample status and change control

This folder organization is a current mandatory project requirement. The organization rule does not establish sample values as production data. Sample files shall retain the sample/demonstration status required by the Master Instructions with no implication of production readiness. When a sample file is moved or renamed, update every project link or path reference in the same change and record the change in the project change log.

## Revision history

| Version | Date | Change | Status |
|---|---|---|---|
| Current | 2026-10-11 | Extract reusable rules; retain project settings separately. Earlier revision records are preserved in the project change log. | CURRENT |
