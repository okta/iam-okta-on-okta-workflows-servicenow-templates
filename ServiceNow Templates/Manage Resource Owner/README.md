# Manage a Resource Owner

Okta Workflows + ServiceNow template that lets an admin or requester manage resource ownership (application/entitlement/bundle owners) inside Okta Identity Governance (OIG) directly from a ServiceNow Service Catalog form — no need to go into the Okta Admin Console.

## What this does

The **Manage a Resource Owner** catalog item lets a requester:

1. Pick a **resource type** to manage (Application, Entitlement, or Entitlement Bundle).
2. Search and select the specific resource (app, entitlement, or bundle) by name.
3. Assign a **new resource owner**.
4. Submit — the request triggers the Flow Designer flow **`Okta Workflows - Manage a Resource Owner`**, which calls the Okta OIG API to look up and update the resource owner.

## Components included in this update set

| Component | Name | Purpose |
| --- | --- | --- |
| Catalog Item | `Manage a Resource Owner` | The Service Catalog form end users fill out |
| Flow Designer Flow | `Okta Workflows - Manage a Resource Owner` | Backend automation that calls Okta OIG APIs |
| Script Include | `OIGOktaAPIQueryAjax` (`x_1647345_okta_w_0.OIGOktaAPIQueryAjax`) | Client-callable AJAX processor that queries Okta applications, bundles, entitlements, and resource owners via Flow Designer subflows |
| Widget | `Okta Workflows - Select Application` | Service Portal widget used for the application typeahead/search field |
| Catalog UI Policies (7) | — | Show/hide catalog variables dynamically based on the selected resource type |
| Catalog Variables (13) | See table below | Form fields on the catalog item |
| System Properties| `Set Resource Values (Entitlement)`, `Fill Entitlements values` | Store the complex object schema used by the Flow Designer data pill mapping |

## Catalog variables

| Variable | Label | Type | Required |
| --- | --- | --- | --- |
| `resource_type` | Which resource type would you like to manage? | Choice | Yes |
| `select_application` | Select Application | Reference/Lookup | No |
| `application_id` | Application ID | Single Line Text | No |
| `application_name` | Application Name | Single Line Text | No |
| `orn_id` | ORN ID | Single Line Text | No |
| `resource_id` | Resource ID | Single Line Text | No |
| `resource_name` | Resource Name | Single Line Text | No |
| `action_to_perform` | Select the action you would like to perform | Choice | No |
| `bundles` | Select the bundle you wish to manage | Lookup Select Box | No |
| `entitlements` | Select the entitlement you wish to manage | Lookup Select Box | No |
| `select_entitlement_value` | Select entitlement value | Lookup Select Box | No |
| `new_resource_owner` | Assign a new resource owner | Reference | No |
| `requested_for` | Requested For | Requested For (variable set) | No |

## Prerequisites
- Workflow Template: Download the [Manage Resource Owner](https://github.com/okta/workflows-templates/tree/master/workflows/).
- Access to an Okta tenant with Okta Workflows and **Okta Identity Governance (OIG)** enabled.
- Okta Workflows connector configured and authenticated to the same Okta org.
- ServiceNow instance with **Flow Designer** and **IntegrationHub** available.
- The scoped application `x_1647345_okta_w_0` (Okta Workflows) installed — this update set depends on Script Include `OIGOktaAPIQueryAjax` living in that scope.
- Underlying Okta Workflows flows/subflows already published and reachable by name within scope `x_1647345_okta_w_0`:
  - `okta_workflows__get_application_filter_by_name`
  - `okta_workflows__get_bundles_by_app`
  - `okta_workflows__get_entitlements_by_app`
  - `okta_workflows__retrieve_resource_owners`
  - `okta_workflows__look_up_entitlement_values_by_entitlement`
- The following System Properties filled out with values from your Okta Workflows instance:
  - `iam.okta-workflows.manage-a-resource-owner.clientToken`
  - `iam.okta-workflow.manage-a-resource-owner.workFlowId`
## Setup steps

1. **Import the update set**
   - Go to **System Update Sets → Retrieved Update Sets → Import Update Set from XML**.
   - Upload `sys_remote_update_set_84da8ab247df0f1083a1981f316d4365.xml`.
   - Preview and **Commit** the update set.

2. **Verify the Flow Designer flow**
   - Navigate to **Flow Designer** and confirm `Okta Workflows - Manage a Resource Owner` imported successfully and is **Active**.
   - Confirm the Okta connector alias used inside the flow points to your Okta tenant.

3. **Verify the Script Include**
   - Confirm `OIGOktaAPIQueryAjax` is active under scope `x_1647345_okta_w_0` and that `Client callable` is checked.

4. **Set the required System Properties**                                                                                                                                                           
      - Go to **System Properties** (`sys_properties.list`) and set:                                                                                                                                   
      - `iam.okta-workflows.manage-a-resource-owner.clientToken` — the client token used to authenticate against the Okta Workflows flow.                                                            
      - `iam.okta-workflow.manage-a-resource-owner.workFlowId` — the Okta workflow ID.  

5. **Publish the catalog item**
   - Go to **Service Catalog → Catalog Definitions → Maintain Items**.
   - Confirm **Manage a Resource Owner** is `Active` and assigned to the correct catalog/category.

## Testing

1. Open the Service Portal and search for **Manage a Resource Owner**.
2. Select **Resource Type = Application**, search and select an application.
3. Confirm entitlements/bundles load dynamically based on the selected application.
4. Assign a new resource owner and submit.
5. Check **Flow Designer → Execution History** for `Okta Workflows - Manage a Resource Owner` to confirm a successful run.
6. Verify the resource owner change reflects in the Okta Admin Console (OIG → Applications → Resource Owners).

## Known limitations

- Resource type support is limited to what the underlying Okta Workflows subflows expose (Applications, Entitlements, Entitlement Bundles).
- The typeahead search fields depend on the corresponding Okta Workflows flow being reachable and returning data within the synchronous execution timeout.
