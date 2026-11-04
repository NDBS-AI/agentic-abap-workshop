# Booster - Service Request Design Brief

Use this only if the architect needs a tighter requirement.

Target: SAP S/4HANA (connected workshop system; do not assume Public Cloud)
Parent package: ZAI_DEMO
Package: ZAI### (participant subpackage)
Hand-written object prefix: ZAI###_
RAP generator: prefix AI, suffix ###

Business object: Service Request

Required fields:
- RequestUUID - technical key
- RequestID - business-visible identifier
- Title - mandatory
- Description
- Priority - LOW / MEDIUM / HIGH / CRITICAL
- Status - OPEN / IN_PROGRESS / RESOLVED
- RequestedBy
- AssignedTo
- DueDate
- standard created/changed audit fields suitable for managed RAP

Required behavior:
- managed RAP
- create/update/delete
- action AssignToMe
- action Resolve
- default status OPEN
- default priority MEDIUM
- Resolve must fail if AssignedTo is initial
- use a small testable helper class for business rules that are easier to unit-test outside the handler
- expose an OData V4 UI service for Fiori elements
- use SAP standard APIs compatible with the connected S/4HANA target; if ABAP Cloud / Clean Core rules apply, use released APIs and evidence
