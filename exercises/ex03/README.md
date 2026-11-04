[Home - Agentic ABAP Workshop](../../README.md)

# Exercise 3: Generate the RAP UI Service with the Developer

## Introduction

In this exercise, you will hand the **approved design** to **`abap-kebap-developer`**. The developer will discover the available RAP generator/tooling in your installed ADT version, create the baseline RAP artifacts in your participant package, activate them, publish the OData V4 service, and preview the first application.

> [!WARNING]
> Coding-agent output is non-deterministic. **Review every proposed object name and every write tool call before approval.** The participant package is a hard boundary.

### Exercises

- [3.1 - Discover the Available RAP Generator](#exercise-31-discover-the-available-rap-generator)
- [3.2 - Generate the Baseline RAP UI Service](#exercise-32-generate-the-baseline-rap-ui-service)
- [3.3 - Review and Activate the Generated Objects](#exercise-33-review-and-activate-the-generated-objects)
- [3.4 - Publish the Service Binding](#exercise-34-publish-the-service-binding)
- [3.5 - Preview the Service Request App](#exercise-35-preview-the-service-request-app)
- [Summary & Next Exercise](#summary--next-exercise)

---

## Exercise 3.1: Discover the Available RAP Generator
[^Top of page](#)

> Let the developer discover the generator capabilities exposed by the installed ADT MCP Server.

<details>
  <summary>🔵 Click to expand!</summary>

1. Select **`abap-kebap-developer`**.
2. Enter:

```text
Read workshop/participant.json and workshop/design-approved.md.
Do not write yet.

Use ADT MCP to list the RAP generators available in this session and identify which ones are supported by the connected S/4HANA system.
Identify the generator that best matches an OData V4 UI service from scratch and inspect its schema before proposing the implementation step.
```

3. Review the generator and schema reported by the developer.

> [!TIP]
> This follows the same safe pattern as an API client: **discover capability → inspect schema → execute**.

</details>

---

## Exercise 3.2: Generate the Baseline RAP UI Service
[^Top of page](#)

> Generate the approved baseline. Do not add the custom business actions yet.

<details>
  <summary>🔵 Click to expand!</summary>

Run:

```text
Implement the approved design baseline now.

Use the participant package and approved object names from the workspace files.
Use the generator "OData UI Service from Scratch" (x-ui-service), transactional with draft,
with prefix AI, suffix ###, entity SREQ, project name SREQ_MGMT, package ZAI###, transport from workshop/participant.json.
Create the managed Service Request RAP stack and OData V4 UI service baseline.

Baseline fields:
- RequestUUID technical key
- RequestID
- Title
- Description
- Priority
- Status
- RequestedBy
- AssignedTo
- DueDate
- managed audit fields appropriate for the connected S/4HANA / RAP target

Do not implement AssignToMe or Resolve yet.
After generation, report every created object and any deviation from the approved design.
```

Approve only tool calls whose destination/package/object names match your participant boundary.

> 💡 **Booster**
>
> If the developer drifts from the approved scope, use [sources/ex03_generator-prompt.txt](sources/ex03_generator-prompt.txt).

</details>

---

## Exercise 3.3: Review and Activate the Generated Objects
[^Top of page](#)

> Inspect the artifacts before activation, then let the developer close syntax/dependency problems.

<details>
  <summary>🔵 Click to expand!</summary>

1. Refresh the ABAP repository tree for package **`ZAI_DEMO > ZAI###`**.
2. Verify that the generated stack includes the expected categories:
   - database persistence,
   - root/interface CDS entity,
   - projection CDS entity,
   - behavior definition/implementation,
   - service definition,
   - service binding,
   - metadata extension where applicable.

   For participant `000` the instructor rehearsal produced exactly these objects (replace `000` with your number):

   | Type | Name |
   |---|---|
   | Tables | `ZAISREQ000`, `ZAISREQ_D000` (draft) |
   | Root view / BDEF / DCL | `ZAIR_SREQ000` |
   | Projection view / BDEF / DDLX / DCL | `ZAIC_SREQ000` |
   | Behavior pools | `ZAIBP_R_SREQ000`, `ZAIBP_C_SREQ000` |
   | Service definition + binding (OData V4) | `ZAIUI_SREQ_MGMT_O4000` |
   | SAP object type / node type | `ZAISREQ_MGMT000` |

   Every name must start with exactly **one** `Z`. A `ZZ…` name means the prefix was passed with a `Z`; stop and ask the instructor.
3. Check the generated **field lengths** before you go on. In the instructor dry run the generator ignored the requested lengths and created every text field as `char(10)` or `char(40)`, which truncates `IN_PROGRESS` and user names. Ask the developer:

```text
Read the generated persistent table and draft table.
Set title to char(80), description to char(255), status to char(12), and requested_by / assigned_to to data element SYUNAME
in BOTH tables (the draft table uses the field names without underscores).
Then check all newly created objects for syntax and dependency errors, fix only issues inside my participant package, and activate the complete baseline.
Do not add new business requirements in this step.
```

4. Review the Problems panel if activation fails.

> [!NOTE]
> The exact list of generated artifacts can vary with generator/version. The approved business contract matters more than reproducing one fixed generator output byte-for-byte.

</details>

---

## Exercise 3.4: Publish the Service Binding
[^Top of page](#)

> Publish the OData V4 UI service and verify that the binding is usable.

<details>
  <summary>🔵 Click to expand!</summary>

1. Open the service binding the generator created (its name is in the generator report).
2. Ask the developer to publish it:

```text
Publish the service binding the generator created in my participant package and confirm it is published.
```

   ADT MCP exposes this as the object action `odatav4publish` on the service binding; the editor's **Publish** button does the same.
3. Confirm the binding status is published.
4. Select the Service Request entity.

</details>

---

## Exercise 3.5: Preview the Service Request App
[^Top of page](#)

> Preview the generated Fiori elements application and create one baseline record.

<details>
  <summary>🔵 Click to expand!</summary>

1. Open the service binding preview for the Service Request entity.
2. Create a request with values similar to:

| Field | Sample Value |
|---|---|
| Request ID | `SR-###-01` |
| Title | `Printer access request` |
| Description | `Workshop baseline record` |
| Priority | `MEDIUM` |
| Status | `OPEN` |

3. Save the record.
4. Verify that the record appears in the list report and can be opened in the object page.

> ✅ **Success**: You have a working transactional RAP UI service before custom behavior is added.

</details>

---

## Summary & Next Exercise
[^Top of page](#)

In this exercise, you have:

- discovered the generator/tool schema before execution,
- generated the baseline RAP UI service with the developer,
- reviewed and activated the generated objects,
- published the OData V4 service, and
- previewed a working Service Request application.

Continue with **[Exercise 4: Add Business Behavior](../ex04/README.md)**.
