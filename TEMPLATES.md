# Okta Workflows + ServiceNow Templates — Index

This index covers the additional Okta Workflows + ServiceNow catalog templates included in this repository, alongside the existing **SSO Application Creation** template described in [README.md](README.md).

Each template is a self-contained ServiceNow **Update Set** (remote update set XML) bundling a Service Catalog item, its Flow Designer flow, and any supporting Script Includes / catalog policies. They can be imported independently of one another.

## Available templates

| Template | Folder | Description |
| --- | --- | --- |
| [SSO Application Creation](README.md) | `ServiceNow Application/` | Self-service request and creation of new Okta SSO applications (SAML/OIDC), with approval routing and email notifications. |
| [Manage a Resource Owner](ServiceNow%20Templates/Manage%20Resource%20Owner/README.md) | `ServiceNow Templates/Manage Resource Owner/` | Assign/update resource owners (applications, entitlements, entitlement bundles) in Okta Identity Governance (OIG) from a catalog form. |
| [Service Account Creation](ServiceNow%20Templates/Service%20Account%20Creation/README.md) | `ServiceNow Templates/Service Account Creation/` | Request and provision new Okta service accounts, including owner/co-owner assignment, compliance tagging, and password handling metadata. |
| [User Group Removal](ServiceNow%20Templates/User%20Group%20Removal/README.md) | `ServiceNow Templates/User Group Removal/` | Bulk-remove users from Okta groups via CSV upload, with execution tracked through Flow Designer. |

## Common prerequisites (all templates)

- An Okta tenant with **Okta Workflows** enabled and the relevant Okta connector authenticated.
- A ServiceNow instance with **Flow Designer** and **IntegrationHub** available.
- The scoped application `x_1647345_okta_w_0` (Okta Workflows) installed, since the Script Includes in these templates live in that scope.
- The [SSO Application Creation](README.md) template (`ServiceNow Application/`) deployed first — the other templates in this index build on the same scoped application and connector setup it establishes. 

## Authentication

These templates authenticate to their Okta Workflows flows using a **Client Token** (stored in the `clientToken` System Property for each template) passed alongside the target `workFlowId`. This is the default authentication method used across all templates in this repo.

All three templates (`Manage Resource Owner`, `Service Account Creation`, `User Group Removal`) call their REST steps through the **same IntegrationHub Connection Alias**. Because they share this single alias, you only need to configure authentication **once** — you don't need to set a client token per app/template.

If you prefer to authenticate against your Okta Workflows/Okta org using **Auth0** (OAuth2) instead of a static client token:

- Configure an OAuth2 Auth Profile (**System OAuth → Application Registry**) against your Auth0 tenant.
- Update the shared **Connection Alias** (`x_1647345_okta_w_0.Okta_Workflows`) (used by all three Flow Designer flows) to reference that OAuth2 profile instead of the client token header. Since it's one shared alias, this single change applies to all three templates at once.
- Retire the `clientToken` System Properties for each template once the switch is validated, since they will no longer be used for authentication.
## General import pattern

1. **System Update Sets → Retrieved Update Sets → Import Update Set from XML**, upload the folder's `sys_remote_update_set_*.xml` file.
2. **Preview** the update set and resolve any reported collisions.
3. **Commit** the update set.
4. Verify the imported **Flow Designer flow** is Active and its Okta connector alias points to your tenant.
5. Verify any imported **Script Include** is Active and Client Callable (where applicable).
6. Publish/activate the **Catalog Item** in Service Catalog → Catalog Definitions → Maintain Items.

See each template's own `README.md` for the exact components, catalog variables, setup steps, and test plan specific to that automation.
