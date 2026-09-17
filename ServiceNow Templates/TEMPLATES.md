# Okta Workflows + ServiceNow Templates — Index

This index covers the additional Okta Workflows + ServiceNow catalog templates included in this repository, alongside the existing **SSO Application Creation** template described in [README.md](README.md).

Each template is a self-contained ServiceNow **Update Set** (remote update set XML) bundling a Service Catalog item, its Flow Designer flow, and any supporting Script Includes / catalog policies. They can be imported independently of one another.

## Available templates

| Template | Folder | Description |
| --- | --- | --- |
| [SSO Application Creation](README.md) | `ServiceNow Application/` | Self-service request and creation of new Okta SSO applications (SAML/OIDC), with approval routing and email notifications. |
| [Manage a Resource Owner](Manage%20Resource%20Owner/README.md) | `Manage Resource Owner/` | Assign/update resource owners (applications, entitlements, entitlement bundles) in Okta Identity Governance (OIG) from a catalog form. |
| [Service Account Creation](Service%20Account%20Creation/README.md) | `Service Account Creation/` | Request and provision new Okta service accounts, including owner/co-owner assignment, compliance tagging, and password handling metadata. |
| [User Group Removal](User%20Group%20Removal/README.md) | `User Group Removal/` | Bulk-remove users from Okta groups via CSV upload, with execution tracked through Flow Designer. |

## Common prerequisites (all templates)

- An Okta tenant with **Okta Workflows** enabled and the relevant Okta connector authenticated.
- A ServiceNow instance with **Flow Designer** and **IntegrationHub** available.
- The scoped application `x_1647345_okta_w_0` (Okta Workflows) installed, since the Script Includes in these templates live in that scope.

## General import pattern

1. **System Update Sets → Retrieved Update Sets → Import Update Set from XML**, upload the folder's `sys_remote_update_set_*.xml` file.
2. **Preview** the update set and resolve any reported collisions.
3. **Commit** the update set.
4. Verify the imported **Flow Designer flow** is Active and its Okta connector alias points to your tenant.
5. Verify any imported **Script Include** is Active and Client Callable (where applicable).
6. Publish/activate the **Catalog Item** in Service Catalog → Catalog Definitions → Maintain Items.

See each template's own `README.md` for the exact components, catalog variables, setup steps, and test plan specific to that automation.
