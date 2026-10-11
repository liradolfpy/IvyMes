**FILE TYPE: PROJECT SPECIFIC**

Perform the FINAL MES MongoDB DATABASE MODEL AUDIT.

Review every collection specification and sample document in the project.

Do not create new collections.

Do not modify anything yet.

Audit the complete database model for:

ARCHITECTURE
- Module coverage
- Functional coverage
- Domain boundaries
- Collection responsibilities

NAMING
- Collection names
- Field names
- ID naming
- Name/description naming
- Audit field naming

DATA MODEL
- Flat documents
- No nested documents
- No embedded object arrays
- Strategic denormalization
- Mandatory ID + Name pairing
- Consistent data types

RELATIONSHIPS
- References
- Referential integrity
- Missing master collections
- Broken references
- Duplicate entities
- Circular dependencies

PERFORMANCE
- Query patterns
- Index requirements
- Lookup avoidance
- High-volume collections
- Time-series/event collections

AUDITABILITY
- createdAt
- createdById
- createdBy
- updatedAt
- updatedById
- updatedBy

SAMPLE DATA
- Valid IDs
- Consistent references
- Realistic values
- Valid dates
- Valid NumberInt usage
- Valid ISODate usage
- No orphan references

OUTPUT:

1. Executive summary
2. Critical issues
3. High issues
4. Medium issues
5. Low issues
6. Missing collections
7. Duplicate concepts
8. Naming inconsistencies
9. Data-type inconsistencies
10. Reference inconsistencies
11. Performance concerns
12. Recommended corrections

Do not silently modify any project artifact.