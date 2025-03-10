---
page_title: "keycloak_group_memberships Resource"
---

# keycloak\_group\_memberships Resource

Allows for managing a Keycloak group's members.

Note that this resource attempts to be an **authoritative** source over group members. When this resource takes control
over a group's members, users that are manually added to the group will be removed, and users that are manually removed
from the group will be added upon the next run of `terraform apply`.

Also note that you should not use `keycloak_group_memberships` with a group has been assigned as a default group via
`keycloak_default_groups`.

This resource **should not** be used to control membership of a group that has its members federated from an external
source via group mapping.

To non-exclusivly manage the group's of a user, see the [`keycloak_user_groups` resource][1]

## Example Usage

```hcl
resource "keycloak_realm" "realm" {
  realm   = "my-realm"
  enabled = true
}

resource "keycloak_group" "group" {
  realm_id = keycloak_realm.realm.id
  name     = "my-group"
}

resource "keycloak_user" "user" {
  realm_id = keycloak_realm.realm.id
  username = "my-user"
}

resource "keycloak_group_memberships" "group_members" {
  realm_id = keycloak_realm.realm.id
  group_id = keycloak_group.group.id

  members  = [
    keycloak_user.user.username
  ]
}
```

## Argument Reference

- `realm_id` - (Required) The realm this group exists in.
- `group_id` - (Required) The ID of the group this resource should manage memberships for.
- `members` - (Required) A list of usernames that belong to this group.
- `pagination` - (Optional) An integer value to limit the number of members retrieved in one API call when refreshing the list, lower the value if the Keycloak backend has limited resources, -1 to retrieve all in one go. Defaults to -1.

## Import

This resource does not support import. Instead of importing, feel free to create this resource
as if it did not already exist on the server.

[1]: /docs/resources/user_groups.html
