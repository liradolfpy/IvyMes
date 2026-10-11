Perform a cross-collection MongoDB data-model consistency review.

Do NOT redesign the collections yet.

Review all approved collection specifications currently available in the project.

Check specifically for:

1. Duplicate business entities
2. Duplicate collections
3. Inconsistent collection names
4. Inconsistent field names
5. Inconsistent ID field names
6. Inconsistent Name field names
7. Missing ID–Name pairs
8. Different data types for the same concept
9. Different meanings assigned to the same field
10. Inconsistent audit fields
11. Inconsistent status values
12. Broken references
13. Missing reference collections
14. Circular dependencies
15. Missing indexes
16. Fields that should be shared but are independently defined
17. Fields that should be collection-specific but have been incorrectly shared
18. Violations of the flat-document rule
19. Nested documents
20. Embedded arrays
21. Violations of the strategic denormalization rule

Pay particular attention to:

<entity>Id + <entity>Name

For example:

productId + productName
plantId + plantName
workCenterId + workCenterName
createdById + createdBy
updatedById + updatedBy

Produce a discrepancy report.

Do not silently correct anything.

Classify every discrepancy as:

CRITICAL
HIGH
MEDIUM
LOW

Then propose corrections.