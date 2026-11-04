[Home - Agentic ABAP Workshop](../../README.md)

# Exercise 5: Test, ATC, and Independent Review

## Introduction

In this exercise, the developer will generate and run ABAP Unit tests, execute ATC, and then hand the completed change to **`abap-kebap-reviewer`** for an independent read-only review.

If the reviewer finds a blocking issue, you will perform **one** controlled developer → reviewer fix loop.

### Exercises

- [5.1 - Generate and Run ABAP Unit Tests](#exercise-51-generate-and-run-abap-unit-tests)
- [5.2 - Run ATC](#exercise-52-run-atc)
- [5.3 - Review with `abap-kebap-reviewer`](#exercise-53-review-with-abap-kebap-reviewer)
- [5.4 - Close One Fix Loop](#exercise-54-close-one-fix-loop)
- [Summary](#summary)

> [!NOTE]
> Review generated test code before running it. A green test result is useful evidence only when the test actually covers the intended rule.

---

## Exercise 5.1: Generate and Run ABAP Unit Tests
[^Top of page](#)

> Use the developer to create focused tests for the rules helper and run them until they pass.

<details>
  <summary>🔵 Click to expand!</summary>

Select **`abap-kebap-developer`** and run:

```text
Read workshop/participant.json and workshop/design-approved.md.

Generate ABAP Unit tests for the deterministic business-rule logic in the Service Request rules helper.
Cover at minimum:
- allowed / rejected resolve decision based on assignment
- any deterministic status-transition rule implemented by the helper
- any deterministic default/rule calculation implemented by the helper

Review the production API before writing tests.
Run the tests through the available ADT capability, fix only legitimate implementation/test issues inside my participant package, and repeat until all intended tests pass.
Report the final test evidence.
```

Verify the test result in the VS Code Testing view or the output returned by ADT MCP.

</details>

---

## Exercise 5.2: Run ATC
[^Top of page](#)

> Run the available ATC check for the participant package or changed objects.

<details>
  <summary>🔵 Click to expand!</summary>

Continue with the developer:

```text
Run ATC for the Service Request artifacts created or changed in this workshop using the ADT capabilities available in this session.
Do not suppress findings automatically.
Classify the results and propose fixes for findings caused by our code.
```

If an ATC profile/variant is preconfigured by the instructor, use it. Record any finding that cannot be fixed within the workshop scope.

> [!NOTE]
> With the default variant ATC reported only 8 of 16 objects as checked in the acceptance run (behavior definitions, access controls and service artifacts appear not to be covered). "0 findings" therefore does not cover every object; say so in your evidence.

</details>

---

## Exercise 5.3: Review with `abap-kebap-reviewer`
[^Top of page](#)

> Switch roles. The reviewer must inspect the result independently and must not silently repair it.

<details>
  <summary>🔵 Click to expand!</summary>

1. Select **`abap-kebap-reviewer`**.
2. Run:

```text
Read workshop/participant.json and workshop/design-approved.md.
Review the completed Service Request implementation in my participant package.

Use read-only ADT MCP evidence and the relevant skills.
Use released-objects for any SAP standard release-status claim.
Consider the final ABAP Unit and ATC evidence.

Review through these lenses:
- correctness and Clean ABAP
- security
- performance risk

Do not modify the SAP system.
Do not claim a runtime bottleneck unless you have runtime measurement evidence; static concerns must be labeled as potential risks.
Give the final verdict in the reviewer agent's normal output format.
```

> 💡 **Booster**
>
> If the review is too superficial, ask the reviewer to reconcile it with [sources/ex05_reviewer-checklist.md](sources/ex05_reviewer-checklist.md).

</details>

---

## Exercise 5.4: Close One Fix Loop
[^Top of page](#)

> If the reviewer reports a Critical or workshop-relevant Important finding, hand that finding back to the developer once.

<details>
  <summary>🔵 Click to expand!</summary>

If a fix is required:

1. Copy only the relevant reviewer finding.
2. Switch to **`abap-kebap-developer`**.
3. Prompt:

```text
Fix the following independent reviewer finding in my participant package only:

<paste finding>

Preserve the approved design unless the finding proves the design unsafe.
After the fix: check/activate, rerun the affected ABAP Unit tests, and rerun the relevant ATC check.
Return evidence only after all steps complete.
```

4. Switch back to **`abap-kebap-reviewer`** and ask for a focused re-review of the fixed finding:

```text
Re-review only the fix for this finding in my participant package:

<paste finding>

Is the finding closed? Use read-only ADT evidence. Do not modify the SAP system.
```

> [!TIP]
> Stop after one loop during the two-hour workshop. Additional refactoring belongs in follow-up work unless the app is unsafe or broken.

</details>

---

## Summary
[^Top of page](#)

Congratulations! You have completed the core workshop.

You have:

- used the developer to generate and execute ABAP Unit tests,
- run ATC instead of relying on model confidence,
- performed an independent three-lens review, and
- closed an evidence-based developer/reviewer fix loop.

If time remains, continue with **[Exercise 6: Intent-Only Team Challenge](../ex06/README.md)**.
