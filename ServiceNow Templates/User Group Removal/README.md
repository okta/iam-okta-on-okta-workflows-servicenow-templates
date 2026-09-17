# User Group Removal

Okta Workflows + ServiceNow template that automates the secure, tracked removal of specified users from targeted Okta groups using a CSV upload — initiated from a ServiceNow Service Catalog form.

## What this does

The **User Group Removal** catalog item lets a requester:

1. Specify the **application name** the group removal relates to.
2. Upload a **CSV file** listing the users to remove.
3. Submit — the request triggers the Flow Designer flow **`Okta Workflows - User group removal`**, which parses the CSV and removes each listed user from the target group(s) in Okta.

## Components included in this update set

| Component | Name | Purpose |
| --- | --- | --- |
| Catalog Item | `User Group Removal` | The Service Catalog form end users fill out |
| Flow Designer Flow | `Okta Workflows - User group removal` | Backend automation that parses the CSV and removes users from Okta group(s) |
| Catalog Variables (3) | See table below | Form fields on the catalog item |
| System Properties (2) | `user-group-removal.clientToken`, `user-group-removal.workFlowId` | Authenticate and route requests to the published Okta Workflows flow (see below) |

## Catalog variables

| Variable | Label | Type | Required |
| --- | --- | --- | --- |
| `application_name` | Application name | Single Line Text | Yes |
| `list_of_users` | Upload a list of users to be removed (.csv) | Attachment | Yes |
| `requestor_for` | Requested For | Requested For (variable set) | Yes |

## Prerequisites
- Access to an Okta tenant.
- Workflow Template: Download the [User Group Removal](https://github.com/okta/workflows-templates/tree/master/workflows/).
- Okta Workflows connector configured and authenticated to the target Okta org, with permissions to remove users from groups.
- ServiceNow instance with **Flow Designer** and **IntegrationHub** available.
- CSV file format expected by the flow (typically a single column of usernames/emails) — confirm the exact expected column header/format against the flow's CSV parsing step before go-live.
- The following System Properties filled out with values from your Okta Workflows instance:
  - `x_1647345_okta_w_0.iam.okta-workflow.user-group-removal.clientToken`
  - `x_1647345_okta_w_0.iam.okta-workflow.user-group-removal.workFlowId`

## Setup steps

1. **Import the update set**
   - Go to **System Update Sets → Retrieved Update Sets → Import Update Set from XML**.
   - Upload `sys_remote_update_set_12632fa897974710326630fce053af9f.xml`.
   - Preview and **Commit** the update set.

2. **Verify the Flow Designer flow**
   - Navigate to **Flow Designer** and confirm `Okta Workflows - User group removal` imported successfully and is **Active**.
   - Confirm the Okta connector alias used inside the flow points to your Okta tenant.
   - Confirm the CSV parsing step matches the format you plan to distribute to requesters (delimiter, expected column(s)).

3. **Set the required System Properties**
   - Go to **System Properties** (`sys_properties.list`) and set:
     - `x_1647345_okta_w_0.iam.okta-workflow.user-group-removal.clientToken` — the client token used to authenticate against the Okta Workflows flow.
     - `x_1647345_okta_w_0.iam.okta-workflow.user-group-removal.workFlowId` — the Okta workflow ID.
4. **Publish the catalog item**
   - Go to **Service Catalog → Catalog Definitions → Maintain Items**.
   - Confirm **User Group Removal** is `Active` and assigned to the correct catalog/category.

## Testing

1. Prepare a test CSV with 1–2 known test user emails/usernames.
2. Open the Service Portal and search for **User Group Removal**.
3. Fill in the application name, attach the test CSV, and submit.
4. Check **Flow Designer → Execution History** for `Okta Workflows - User group removal` to confirm a successful run and that it processed the expected number of rows.
5. Verify in the Okta Admin Console that the test users were removed from the target group(s).
6. Confirm the ServiceNow request/ticket reflects a completed status with an audit trail of the removal.

## Known limitations

- This template removes users from groups based solely on the uploaded CSV — there is no built-in preview/dry-run step before removal executes.
- Malformed CSVs (wrong column, extra whitespace, non-existent users) should be validated against your flow's error handling before relying on this for production-scale removals.
