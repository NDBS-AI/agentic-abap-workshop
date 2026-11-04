# Booster - Business Rules Acceptance Criteria

Use only if the developer needs a precise recovery target.

## Defaults
- On new Service Request: Status = OPEN when initial.
- On new Service Request: Priority = MEDIUM when initial.

## Validation
- Title must not be initial on save.
- Resolve must not complete if AssignedTo is initial.

## Action AssignToMe
- Determine the current technical user using an API/object verified for the connected S/4HANA target. Use released-objects target auto when release/Clean Core classification is applicable.
- Set AssignedTo to that value.
- If Status is OPEN, transition to IN_PROGRESS.

## Action Resolve
- Reject when AssignedTo is initial.
- Set Status to RESOLVED when valid.

## Testable helper
- Put deterministic rule logic that can be unit-tested outside the RAP handler in ZAI###_CL_SREQ_RULES (or the approved equivalent).
- Do not put COMMIT WORK / COMMIT ENTITIES in behavior logic.
