[Home - Agentic ABAP Workshop](../../README.md)

# Exercise 2: Design the RAP Solution with the Architect

## Introduction

In this exercise, you will use **`abap-kebap-architect`** to design the Service Request application before any code is written.

The architect is expected to inspect the target and requirement, enumerate viable options, verify uncertain SAP standard dependencies with `released-objects`, and recommend one bounded design. **It must not modify the SAP system.**

### Exercises

- [2.1 - Give the Requirement to the Architect](#exercise-21-give-the-requirement-to-the-architect)
- [2.2 - Review the Architecture Options](#exercise-22-review-the-architecture-options)
- [2.3 - Verify SAP Standard Dependencies](#exercise-23-verify-sap-standard-dependencies)
- [2.4 - Approve and Record the Design](#exercise-24-approve-and-record-the-design)
- [Summary & Next Exercise](#summary--next-exercise)

> [!WARNING]
> This is a **design-only** exercise. Do not approve any ADT MCP write operation.

---

## Exercise 2.1: Give the Requirement to the Architect
[^Top of page](#)

> Ask the architect to design a managed RAP application from the business requirement, not from a pre-written implementation recipe.

<details>
  <summary>🔵 Click to expand!</summary>

1. Select **`abap-kebap-architect`**.
2. Enter the following prompt:

```text
Read workshop/participant.json.
Target the SAP S/4HANA system defined in workshop/participant.json. Do not assume Public Cloud.

Design a transactional RAP application for Service Request Management.

Business requirements:
- one root Service Request entity
- Request ID, title, description, priority, status, requested by, assigned to, due date, audit fields
- create/update/delete
- AssignToMe action
- Resolve action
- title is mandatory
- new requests default to OPEN and MEDIUM priority
- a request cannot be resolved without an assignee
- expose an OData V4 UI service suitable for Fiori elements
- business rules should be testable with ABAP Unit

Do not modify the SAP system.
Enumerate 2-3 viable design options, explain trade-offs, and recommend one.
Use target-aware compatibility evidence for any SAP standard API/object you intend to depend on. Use released-objects when its classification applies to the connected target.
Respect the participant package and naming boundary.
```

3. Review the architecture response before continuing.

> 💡 **Booster**
>
> If the response is too generic, use the detailed requirement in [sources/ex02_design-brief.md](sources/ex02_design-brief.md) and ask the architect to refine the design — still without implementation.

</details>

---

## Exercise 2.2: Review the Architecture Options
[^Top of page](#)

> Check that the architect has made real design decisions rather than simply repeating the requirement.

<details>
  <summary>🔵 Click to expand!</summary>

The response should explicitly cover:

| Decision | What to look for |
|---|---|
| RAP type | Managed vs. alternatives and why managed fits this workshop |
| Key strategy | Technical UUID and business-visible identifier strategy |
| CDS layers | Root/interface and projection/consumption responsibilities |
| Behavior | Actions, validations, defaults/determinations |
| UI/API | Service definition/binding and metadata strategy |
| Testing | Which rule logic should be isolated in a helper and unit-tested |
| Compatibility | SAP standard dependencies are appropriate for the connected S/4HANA target; release/Clean Core evidence is shown when applicable |
| Boundaries | Participant package, object prefix, transport scope |

> [!NOTE]
> A good architect can recommend a simpler option for a two-hour workshop while explicitly naming what would change in a production design.

</details>

---

## Exercise 2.3: Verify SAP Standard Dependencies
[^Top of page](#)

> Verify target compatibility before the design is approved; do not assume Public Cloud rules.

<details>
  <summary>🔵 Click to expand!</summary>

Ask the architect:

```text
For every SAP standard object or API in your recommended design, identify the connected S/4HANA target first.
Use released-objects with target auto when release/Clean Core classification is applicable.
If the connected system uses a classic ABAP language version, also verify the API against the live system/repository capabilities instead of imposing Public Cloud-only rules.
If you are uncertain about how AssignToMe should obtain the current technical user, compare supported candidates and recommend one only after checking target compatibility.
Do not modify the SAP system.
```

Expected result: the recommendation distinguishes **custom Z/Y objects** from **SAP standard released APIs** and does not treat `not-found` for a custom object as a release verdict.

</details>

---

## Exercise 2.4: Approve and Record the Design
[^Top of page](#)

> Create the human approval gate between architecture and implementation.

<details>
  <summary>🔵 Click to expand!</summary>

1. Open `workshop/design-record.template.md`.
2. Copy it to `workshop/design-approved.md`.
3. Record the architect's recommended option, naming plan, and released-object decisions.
4. Add your approval and any constraint you want the developer to follow.
5. Do **not** start implementation until the design is approved.

Workshop naming rules:

| Artifact | Rule | Example (`001`) |
|---|---|---|
| Package | `ZAI###` under `ZAI_DEMO` | `ZAI001` |
| RAP generator input | prefix `AI`, suffix `###`, entity `SREQ`, project `SREQ_MGMT` | generator adds the leading `Z` |
| Generated objects (table, views, BDEF, pool, service) | whatever the generator returns | `ZAISREQ001`, `ZAIR_SREQ001`, `ZAIC_SREQ001`, `ZAIBP_R_SREQ001`, `ZAIUI_SREQ_MGMT_O4001` (see Exercise 3) |
| Rules helper class | `ZAI###_CL_SREQ_RULES` | `ZAI001_CL_SREQ_RULES` |
| Resolve parameter entity + abstract BDEF | `ZAI###_A_SREQ_RESOLVE` | `ZAI001_A_SREQ_RESOLVE` |

Naming convention:
- **Generated objects** keep the generator's names. This is a documented exception: the generator fixes their shape.
- **Hand-written objects and every variable, constant, type and parameter** follow the naming convention shipped with the `abap-clean-style` skill (`references/naming-conventions.md`): for example `c_status`, `ty_status`, `iv_title`, `rv_valid`, `lv_user`, `lt_sreq`, `ls_sreq`, `lr_sreq`, `lo_context_error`. RAP framework parameters (`keys`, `result`, `failed`, `reported`) keep their framework names.
- Ask the architect and the developer to apply that convention; the reviewer checks it in Exercise 5.

> [!WARNING]
> Do **not** pass a prefix that already starts with `Z` to the generator. It prepends `Z` itself: prefix `ZAI` would produce `ZZAIR_SREQ###` instead of `ZAIR_SREQ###`. Its prefix and suffix are limited to 3 characters each.

Record the names the generator actually returns in `workshop/design-approved.md` and use them consistently.

</details>

---

## Summary & Next Exercise
[^Top of page](#)

In this exercise, you have:

- used the architect before implementation,
- reviewed multiple design options and trade-offs,
- verified SAP standard dependencies against the connected S/4HANA target, and
- created an explicit human approval gate.

Continue with **[Exercise 3: Generate the RAP UI Service with the Developer](../ex03/README.md)**.
