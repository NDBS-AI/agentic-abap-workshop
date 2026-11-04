[Home - Agentic ABAP Workshop](../../README.md)

# Exercise 4: Add Business Behavior

## Introduction

In this exercise, you will enhance the baseline RAP business object with business behavior using **`abap-kebap-developer`** and the relevant Abap-Kebap skills.

You will add defaults, a title validation, the **`AssignToMe`** and **`Resolve`** actions, and a small helper class for deterministic rule logic that can be covered by ABAP Unit in the next exercise.

### Exercises

- [4.1 - Add Defaults and Validation](#exercise-41-add-defaults-and-validation)
- [4.2 - Implement the `AssignToMe` Action](#exercise-42-implement-the-assigntome-action)
- [4.3 - Implement the `Resolve` Action](#exercise-43-implement-the-resolve-action)
- [4.4 - Preview and Test the Enhanced App](#exercise-44-preview-and-test-the-enhanced-app)
- [Summary & Next Exercise](#summary--next-exercise)

> [!WARNING]
> Review AI-generated behavior code carefully. RAP transaction rules and ABAP Cloud restrictions are part of the acceptance criteria, not optional style preferences.

---

## Exercise 4.1: Add Defaults and Validation
[^Top of page](#)

> Add safe defaults for new requests and reject an empty title on save.

<details>
  <summary>🔵 Click to expand!</summary>

Select **`abap-kebap-developer`** and run:

```text
Read workshop/participant.json and workshop/design-approved.md.
Enhance the existing Service Request RAP BO only inside my participant package.

Add these rules:
- default Status to OPEN when a new request has no status
- default Priority to MEDIUM when a new request has no priority
- validate on save that Title is not initial and report a user-facing error

Follow the relevant RAP and Clean ABAP skills, and name every hand-written object, variable, constant and type by the naming convention of the abap-clean-style skill.
Check and activate the changed objects after implementation.
Do not implement AssignToMe or Resolve yet.
```

After activation, create a request with an empty title and verify that save is rejected.

</details>

---

## Exercise 4.2: Implement the `AssignToMe` Action
[^Top of page](#)

> Use release evidence before choosing the user-context API, then implement the action.

<details>
  <summary>🔵 Click to expand!</summary>

Run:

```text
Implement the AssignToMe action in the approved RAP BO.

Before coding, identify the connected S/4HANA target and verify the SAP standard API/object used to obtain the current technical user. Use released-objects with target auto when release/Clean Core classification applies; otherwise verify support against the live system. Do not guess an API from memory.

Behavior:
- set AssignedTo to the current technical user
- if Status is OPEN, change it to IN_PROGRESS
- keep the implementation compatible with the connected system and its configured ABAP language version; apply ABAP Cloud restrictions only when that target requires them
- check and activate all changed objects

Report the release evidence you relied on.
```

> [!IMPORTANT]
> If the candidate returned by a general search is not clearly released for your target, call `check_objects` before using it.

</details>

---

## Exercise 4.3: Implement the `Resolve` Action
[^Top of page](#)

> Add a guarded transition to RESOLVED and isolate deterministic rules for unit testing.

<details>
  <summary>🔵 Click to expand!</summary>

Run:

```text
Implement the Resolve action.

Acceptance criteria:
- Resolve must fail with a user-facing error when AssignedTo is initial
- otherwise set Status to RESOLVED
- create or update the approved rules helper class so deterministic status/rule logic can be ABAP Unit tested outside the RAP handler
- do not use COMMIT WORK or COMMIT ENTITIES inside RAP behavior code
- check and activate all changed objects
```

Then make both actions usable in the app. Without this step the preview in Exercise 4.4 has no buttons:

```text
Show the AssignToMe and Resolve actions as buttons on the list report and the object page of my Service Request app.
Make AssignedTo, Status and RequestID read-only for the user (they are set by the actions and on save).
Check and activate all changed objects.
```

> [!NOTE]
> In the acceptance run the developer kept `RequestID` editable and explained why: nothing numbers it on save yet. That is a correct answer. Ask for an on-save Request ID first if you want it read-only.

> 💡 **Booster**
>
> If the implementation drifts, use [sources/ex04_business-rules.md](sources/ex04_business-rules.md) as the acceptance checklist and ask the developer to reconcile the implementation against it.

</details>

---

## Exercise 4.4: Preview and Test the Enhanced App
[^Top of page](#)

> Validate the business behavior from the Fiori elements preview.

<details>
  <summary>🔵 Click to expand!</summary>

Test at least the following scenarios:

1. Create a request with an empty title → save is rejected.
2. Create a valid request without status/priority → defaults are applied.
3. Run **AssignToMe** → assignee is populated; an OPEN item can move to IN_PROGRESS.
4. Create or clear a request without assignee and run **Resolve** → action is rejected.
5. Assign the request and run **Resolve** → status becomes RESOLVED.

> ✅ **Success**: Business behavior works from the UI and all changed objects are active.

</details>

---

## Exercise 4.5 (Stretch): Change Requests from the Business
[^Top of page](#)

> Give each change request to the developer **one at a time**, in plain business language, and verify the result before the next one. All four were run on the workshop system.

<details>
  <summary>🔵 Click to expand!</summary>

| # | Change request (paste as-is) | How to verify |
|---|---|---|
| A | `When the user presses Resolve, open a popup that asks for a comment. The comment is required. Store it on the request.` | Resolve shows a popup; the field has an asterisk; empty input is blocked; the comment is shown on the object page. |
| B | `A request can only be resolved by the user it is assigned to.` | Resolve by another user fails with a clear message. |
| C | `Enable the Resolve button only on lines assigned to the current user. The button must update right after Assign to Me, without a page refresh.` | Resolve is greyed out on unassigned lines and becomes active right after Assign to Me. |
| D | `Highlight list rows: not assigned = red, assigned but not resolved = orange, resolved and assigned = green.` | Rows are coloured; the Status cell shows an icon. |

What the dry run taught us (use these if the agent gets stuck):

- **A — required popup field.** The abstract behavior definition must be written *without* strict mode:
  ```
  abstract;
  define behavior for ZAI###_A_SREQ_RESOLVE
  {
    field ( mandatory ) ResolutionComment;
  }
  ```
  The variant with `strict ( 2 )`, `with hierarchy`, `with control` and `mandatory : execute` activates cleanly but produces **no** mandatory marker. Prove it by searching `$metadata` for `Resolve(...)/ResolutionComment` and `Common.FieldControl`. Keep the server-side check for a blank comment too. Source: [software-heroes.com – RAP Popup Mandatory Fields](https://software-heroes.com/en/blog/abap-rap-popup-mandatory-fields).
- **B — assignee only.** Add a rule method `is_assigned_to_user( iv_assigned_to, iv_current_user )` to the rules class *test first*, then call it in the Resolve handler with `cl_abap_context_info=>get_user_technical_name( )`.
- **C — button state.** `action ( features : instance ) Resolve ...` plus a `get_instance_features` handler that reuses the same rule, and a side effect `action AssignToMe affects permissions ( action Resolve );` so the button refreshes without a reload.
- **D — row colour.** Compute `StatusCriticality` (1 / 2 / 3) with a `case` in the **root** view (a projection view rejects the expression), add the field to the **draft table**, mark it read-only, and put `@UI.lineItem: [{ criticality: 'StatusCriticality' }]` at entity level in the metadata extension.

If you are still stuck, ask the instructor for the reference sources.

</details>

---

## Summary & Next Exercise
[^Top of page](#)

In this exercise, you have:

- added RAP defaults and validation,
- verified the user-context dependency against the connected S/4HANA target,
- implemented two business actions,
- isolated deterministic rule logic for testing, and
- verified the behavior through the Fiori elements preview.

Continue with **[Exercise 5: Test, ATC, and Independent Review](../ex05/README.md)**.
