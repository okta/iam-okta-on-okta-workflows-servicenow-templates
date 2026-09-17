# Service Account Creation

Okta Workflows + ServiceNow template that automates the request and creation of new Okta service accounts, including manager/co-owner approvals, compliance tagging, and password storage tracking — all initiated from a ServiceNow Service Catalog form.

## What this does

The **New Service Account Request** catalog item lets a requester:

1. Specify the connected application, team, and a short/abbreviated name for the account.
2. Describe the account's purpose and choose an **Account Type** (e.g., Interactive vs. non-interactive).
3. Flag **Compliance** scope (e.g., HIPAA) and whether the **password will be hardcoded**.
4. Identify a **Service Account Owner** and **Co-owner**, and who will have access to the password.
5. Submit — the request triggers the Flow Designer flow **`Okta Workflows - Create Service Account`**, which calls Okta Workflows to provision the new service account.

## Components included in this update set

| Component | Name | Purpose |
| --- | --- | --- |
| Catalog Item | `New Service Account Request` | The Service Catalog form end users fill out |
| Flow Designer Flow | `Okta Workflows - Create Service Account` | Backend automation that creates the service account in Okta |
| Script Include | `ServiceAccountCreationUtilitiesAjax` (`x_1647345_okta_w_0.ServiceAccountCreationUtilitiesAjax`) | Client-callable AJAX processor used by the catalog form (e.g. application lookups, validations) |
| Catalog Client Script | `catalog_script_client_...` | Client-side form logic (field visibility/validation) |
| Catalog UI Policies (5) | — | Show/hide catalog variables based on Account Type / password handling answers |
| Catalog Variables (19) | See table below | Form fields on the catalog item |
| ACL + ACL Role (1 each) | — | Restrict access/visibility on relevant fields or tables |
| Scope Privileges (5) | — | Cross-scope access grants required for this app to call the needed APIs/tables |
| System Properties (2) | `new-service-account.clientToken`, `new-service-account.workFlowId` | Authenticate and route requests to the published Okta Workflows flow (see below) |

## Catalog variables

| Variable | Label | Type | Required |
| --- | --- | --- | --- |
| `connected_application` | Application Name | Single Line Text | Yes |
| `abbreviated_application_name` | Abbreviated Application Name | Single Line Text | Yes |
| `purpose_of_the_account` | Abbreviated purpose for account name | Single Line Text | Yes |
| `describe_purpose_of_the_account` | Describe how this service account will be used | Multi Line Text | Yes |
| `account_type` | Account Type | Choice | Yes |
| `compliance` | Compliance | Lookup Select Box | Yes |
| `password_storage` | Password Storage | Lookup Select Box | Yes |
| `password_be_hardcoded` | Will the password be hardcoded? | Yes/No | Yes |
| `service_account_owner` | Service Account Owner | Reference | Yes |
| `service_account_co_owner` | Service Account Co-owner | Reference | Yes |
| `manager` | Manager | Reference | No |
| `who_will_have_access_to_the_password` | Who will have access to the password? | List Collector | No |
| `business_justification` | Business Justification | Multi Line Text | No |
| `team` | Team Owning the Service Account | Lookup Select Box | No |
| `team_reference` | Team Name | Single Line Text | No |
| `application_name` | Application Name | Single Line Text | No |
| `service_account_name` | Service Account Name | Single Line Text | No |
| `requestor_email` | Requestor Email | Single Line Text | No |
| `requestor_for` | Requested by | Requested For (variable set) | No |

## Prerequisites

- Workflow Template: Download the [Service Account Creation](https://github.com/okta/workflows-templates/tree/master/workflows/).
- ServiceNow instance with **Flow Designer** and **IntegrationHub** available.
- The scoped application `x_1647345_okta_w_0` (Okta Workflows) installed — this update set's Script Include and ACLs depend on that scope.
- Any downstream approval groups (e.g., manager or security approvals) already configured if your process requires them before fulfillment.
- The following System Properties filled out with values from your Okta Workflows instance:
  - `x_1647345_okta_w_0.new-service-account.clientToken`
  - `x_1647345_okta_w_0.new-service-account.workFlowId`

## Setup steps

1. **Import the update set**
   - Go to **System Update Sets → Retrieved Update Sets → Import Update Set from XML**.
   - Upload `sys_remote_update_set_71be972097974710326630fce053afd5.xml`.
   - Preview and **Commit** the update set.

2. **Review scope privileges**
   - Confirm the 5 imported **Scope Privileges** grant the necessary table/API access for `x_1647345_okta_w_0` — adjust if your instance's cross-scope access policy blocks any of them.

3. **Verify the Flow Designer flow**
   - Navigate to **Flow Designer** and confirm `Okta Workflows - Create Service Account` imported successfully and is **Active**.
   - Confirm the Okta connector alias used inside the flow points to your Okta tenant.

4. **Verify the Script Include**
   - Confirm `ServiceAccountCreationUtilitiesAjax` is active and `Client callable` is checked.

5. **Set the required System Properties**
   - Go to **System Properties** (`sys_properties.list`) and set:
     - `x_1647345_okta_w_0.new-service-account.clientToken` — the client token used to authenticate against the Okta Workflows flow.
     - `x_1647345_okta_w_0.new-service-account.workFlowId` — the Okta workflow ID.
6. **Publish the catalog item**
   - Go to **Service Catalog → Catalog Definitions → Maintain Items**.
   - Confirm **New Service Account Request** is `Active` and assigned to the correct catalog/category.

## Testing

1. Open the Service Portal and search for **New Service Account Request**.
2. Fill in required fields (application, abbreviated name, purpose, account type, compliance, password storage, owner, co-owner) and submit.
3. Confirm the request routes through any configured approvals.
4. Check **Flow Designer → Execution History** for `Okta Workflows - Create Service Account` to confirm a successful run.
5. Verify the new service account was created in Okta with the expected naming convention and attributes.

## Known limitations

- This template does not manage service account **deactivation/offboarding** — pair it with a separate process for lifecycle management.
- Password handling fields (`password_be_hardcoded`, `who_will_have_access_to_the_password`) capture intent/metadata only; they do not enforce secret storage — ensure your organization's secrets management policy is followed downstream.
