[Home - Agentic ABAP Workshop](../README.md)

# Bonus: Consume Your RAP Service from a CAP App

## Introduction

In this bonus you build a **SAP CAP (Node.js) app** that consumes the OData V4 service you created in Exercise 3, `ZAIUI_SREQ_MGMT_O4###`. The CAP app shows the service requests in a Fiori elements preview and writes changes back to SAP:

- list and object page with the live data from SAP,
- **Create**, **Edit** and **Delete**,
- **Assign to Me** and **Resolve** (with the required comment),
- status colours from `StatusCriticality`.

You let GitHub Copilot do the work, with the **CAP MCP server** (`cds-mcp`) for CAP documentation and model questions. Give the agent one prompt at a time and check the result before the next one.

The instructor has a working reference implementation for participant `000`. Ask for it only if you are stuck.

---

**Table of Contents**
- [Requirements](#requirements)
- [B.1 - Create the CAP Project](#exercise-b1-create-the-cap-project)
- [B.2 - Import Your RAP Service](#exercise-b2-import-your-rap-service)
- [B.3 - Connect to SAP Without Committing Secrets](#exercise-b3-connect-to-sap-without-committing-secrets)
- [B.4 - Read the Service Requests](#exercise-b4-read-the-service-requests)
- [B.5 - Pass Through the Actions](#exercise-b5-pass-through-the-actions)
- [B.6 - Create, Edit and Delete](#exercise-b6-create-edit-and-delete)
- [B.7 - Fiori Elements Preview](#exercise-b7-fiori-elements-preview)
- [What the Instructor Run Taught Us](#what-the-instructor-run-taught-us)
- [Success Criteria](#success-criteria)

---

## Requirements

| Item | Value |
|---|---|
| Completed exercises | 0 to 4 (your RAP service is published; Exercise 4.5 recommended) |
| Node.js | 22 or later (`--use-system-ca` needs 22.15 / 23.8 or later) |
| CAP tools | `@sap/cds-dk` 9 installed globally (`npm i -g @sap/cds-dk`) |
| CAP MCP server | `cds-mcp` available in GitHub Copilot |
| Service | `ZAIUI_SREQ_MGMT_O4###`, published, at `/sap/opu/odata4/sap/zaiui_sreq_mgmt_o4###/srvd/sap/zaiui_sreq_mgmt_o4###/0001` |
| SAP access | your SAP user and password, client from the instructor |

Business requirements for the CAP app:

1. Show only **saved** service requests from SAP, never SAP drafts.
2. Show Request ID, Title, Description, Priority, Status (with colour), Requested By, Assigned To, Due Date and Resolution Comment.
3. Users can create, edit and delete service requests. Request ID, Status, Assigned To and Resolution Comment are read-only; SAP sets them.
4. **Assign to Me** and **Resolve** call the SAP actions. Resolve asks for a required comment.
5. Errors from SAP (for example "A resolved service request cannot be assigned.") reach the user unchanged.
6. No host, user or password is committed to git.

---

## Exercise B.1: Create the CAP Project
[^Top of page](#)

<details>
  <summary>🔵 Click to expand!</summary>

```text
Create a new Node.js CAP project named cap-sreq-consumer in the folder bonus/ of this workspace with cds init.
Do not add sample data or sample services. Install the dependencies.
```

Check: `bonus/cap-sreq-consumer/package.json` exists and `npm install` succeeded.

</details>

---

## Exercise B.2: Import Your RAP Service
[^Top of page](#)

<details>
  <summary>🔵 Click to expand!</summary>

1. Download the `$metadata` of your service. Log on to the SAP system in the browser and open:

   ```text
   https://<HOST>:<PORT>/sap/opu/odata4/sap/zaiui_sreq_mgmt_o4###/srvd/sap/zaiui_sreq_mgmt_o4###/0001/$metadata?sap-client=<CLIENT>
   ```

   Save the page as `bonus/cap-sreq-consumer/srv/external/ZAIUI_SREQ_MGMT_O4###.edmx`.

2. Ask the agent:

```text
In bonus/cap-sreq-consumer, import srv/external/ZAIUI_SREQ_MGMT_O4###.edmx as a CAP remote service with cds import.
Show me the entity SREQ, its key fields and its bound actions from the imported model.
Use the CAP MCP server for CAP documentation and to inspect the model.
```

Check: the agent reports the key `UUID` + `IsActiveEntity` and the actions `AssignToMe`, `Resolve( ResolutionComment )`, `Edit`, `Activate`, `Discard`, `Prepare`, `Resume`.

</details>

---

## Exercise B.3: Connect to SAP Without Committing Secrets
[^Top of page](#)

<details>
  <summary>🔵 Click to expand!</summary>

```text
Configure the remote service ZAIUI_SREQ_MGMT_O4### in bonus/cap-sreq-consumer as kind odata with CSRF token handling.
Put the URL, basic authentication and the sap-client query parameter into .cdsrc-private.json, not into package.json.
Make sure .cdsrc-private.json is git-ignored, and create a .cdsrc-private.example.json with placeholders <HOST>, <PORT>, <USER>, <PASSWORD>.
Add the SAP Cloud SDK packages CAP needs for remote OData calls.
Do not ask me for the password; I will fill it in myself.
```

Then fill in `.cdsrc-private.json` yourself and check `git status`: the file must **not** appear.

</details>

---

## Exercise B.4: Read the Service Requests
[^Top of page](#)

<details>
  <summary>🔵 Click to expand!</summary>

```text
Create a CAP service SreqService at path /sreq with an entity ServiceRequests that consumes the remote entity SREQ.
Use an own entity with the business fields only (UUID, RequestID, Title, Description, Priority, Status, RequestedBy,
AssignedTo, DueDate, ResolutionComment, StatusCriticality), not a projection, so the remote draft and UI annotations do not propagate.
Implement READ in a custom handler that forwards the query to SAP and returns only active instances (IsActiveEntity = true).
Support $select, $filter, $orderby, $top/$skip, $count and read by key.
Start the app with cds watch and show me the list as JSON.
```

Start the app so Node trusts the company certificate of the SAP system:

```bash
NODE_OPTIONS=--use-system-ca npx cds watch
```

Check: `http://localhost:4004/sreq/ServiceRequests` returns your service requests from SAP.

</details>

---

## Exercise B.5: Pass Through the Actions
[^Top of page](#)

<details>
  <summary>🔵 Click to expand!</summary>

```text
Add two bound actions to ServiceRequests: assignToMe() and resolveRequest(comment : String(255), mandatory).
Implement them by calling the SAP bound actions AssignToMe and Resolve(ResolutionComment) on the active instance,
then return the updated request. Pass SAP error messages to the user unchanged.
Test both actions with curl and show me the responses.
```

Check: Resolve with an empty comment is rejected; Assign to Me on a resolved request returns the SAP error text.

</details>

---

## Exercise B.6: Create, Edit and Delete
[^Top of page](#)

<details>
  <summary>🔵 Click to expand!</summary>

```text
Make ServiceRequests editable from a Fiori elements V4 UI: users can create, edit and delete.
RequestID, Status, AssignedTo, ResolutionComment and StatusCriticality are read-only; Title is mandatory.
The SAP service is draft-enabled, so every write in SAP must go through its draft: create a draft, change it, then activate it.
SAP assigns the UUID of a new request, so the CAP app must use the UUID that SAP returns.
Delete must delete the active instance in SAP. Cancelling a new request must discard the SAP draft.
Test create, edit, delete and an empty title against SAP with curl, and delete your test records afterwards.
```

Check: a request created in the CAP app appears in your SAP Fiori app with the same Request ID.

</details>

---

## Exercise B.7: Fiori Elements Preview
[^Top of page](#)

<details>
  <summary>🔵 Click to expand!</summary>

```text
Add UI annotations for SreqService.ServiceRequests: header info, selection fields Status, Priority, AssignedTo,
a list with RequestID, Title, Priority, Status (criticality StatusCriticality with icon), AssignedTo, DueDate,
row highlighting by StatusCriticality, an object page with all fields, and the buttons Assign to Me and Resolve.
Open the Fiori elements preview and show me which buttons are visible on the list and on the object page.
```

Open `http://localhost:4004/$fiori-preview/SreqService/ServiceRequests#preview-app`.

Check: the list shows **Create**, **Delete**, **Assign to Me** and **Resolve**; the object page shows **Edit**, **Delete**, **Assign to Me** and **Resolve**.

</details>

---

## What the Instructor Run Taught Us

Use these hints only if the agent gets stuck.

| Problem | Cause | Fix |
|---|---|---|
| `unable to get local issuer certificate` | The SAP certificate is issued by the company CA, which Node does not know. | Start with `NODE_OPTIONS=--use-system-ca` so Node uses the Windows certificate store. Do not disable TLS checks. |
| `Request failed with status code 401` | Wrong user or password in `.cdsrc-private.json`. | Correct the file; `cds watch` reloads it. |
| Warning: action `resolve()` conflicts with method in base class | `resolve` is a method of the CAP service class. | Name the action `resolveRequest`; the button can still say "Resolve". |
| `Property 'IsActiveEntity' / 'DraftAdministrativeData_DraftUUID' not found` | CAP draft columns were forwarded to SAP. | Forward only columns that exist on the remote `SREQ`. |
| `Error while parsing an XML stream` (400) on create | `null` was sent for a non-nullable SAP field. | Send empty strings for empty text fields, `null` only for dates. |
| No Create / Edit buttons | Fiori elements V4 shows Create and Edit only for draft-enabled entities. | Add `@odata.draft.enabled` to `ServiceRequests`. |
| `Invalid value: UUID` after Save | The CAP draft key differs from the UUID SAP assigns. | When a new draft is created, first create the SAP draft and use its UUID as the CAP draft key; on Save, fill and activate that SAP draft. |
| `428 Precondition Required` | SAP requires an ETag for changes. | Send `If-Match: *` on writes and actions. |
| Bound action not found | The action path needs the OData namespace. | Use `com.sap.gateway.srvd.zaiui_sreq_mgmt_o4###.v0001.<Action>` (from the `$metadata` schema). |
| Mock server does not start (`SQLITE_CONSTRAINT_NOTNULL`) | Mock data misses non-nullable fields. | Fill every `not null` field in `test/data/ZAIUI_SREQ_MGMT_O4###-SREQ.csv`, including the `__EntityControl_*` and `__OperationControl_*` columns. |

Known limitation: pressing **Create** opens a draft in SAP right away. If the browser is closed instead of pressing **Cancel**, that SAP draft stays until the RAP draft timeout removes it.

---

## Success Criteria

- [ ] The list shows your SAP service requests, without SAP drafts.
- [ ] Create, Edit and Delete in the CAP app change the data in SAP.
- [ ] Assign to Me and Resolve work, and SAP error messages are shown.
- [ ] Resolve asks for a required comment.
- [ ] `git status` shows no `.cdsrc-private.json`.
