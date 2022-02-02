# Adding conditions

> The policies for this section can be found [on Github](https://github.com/cerbos/tutorial/tree/main/src/06-adding-conditions/cerbos).

In the previous section a RBAC policy was created that allowed anyone with a the `user` role to update a user resource - this isn't what is intended as it would allow user's to update other user's profile. 

```yaml
---
apiVersion: api.cerbos.dev/v1
resourcePolicy:
  version: "default"
  resource: "user"
  rules:
    - actions:
        - create
        - read
        - update
      effect: EFFECT_ALLOW
      roles:
        - user
# ....other conditions
```

This blanket approach is where using pure role-based access controls falls down as there is more nuianced required to meet the requirements..

## Conditions

Cerbos is a powerful Attribute-based Access Control system which can make contextual decisions at request time whether an action can be taken.

In this senario, Cerbforce's business logic states that a user can only update their own user profile. To implement this a check needs to be made to ensure the ID of the user making the request matches the ID of the user resource being updated.

[Conditions](https://docs.cerbos.dev/cerbos/latest/policies/conditions.html) in Cerbos are written in [Common Expression Language (CEL)](https://github.com/google/cel-spec/blob/master/doc/intro.md) which is a simple way of defining boolean logic of conditions. In this enviroment there are two main bits of data provided that are of interest `request.principal` which is the information about the user making the request and `request.resource` which is the information about the resource being accessed.

The data model for each of these is as follows:

```json
// request.principal
{
  "id": "somePrinicpalId", // the prinicpal ID
  "roles": ["user"], // the list of roles from the auth provider
  "attr": {
    // a map of attributes about the prinicpal
  }
}

// request.resource
{
  "id": "someResourceId", // the resource ID
  "attr": {
    // a map of attributes about the resourece
  }
}
```

Using this information a check to see if the prinicpal ID is the same as the ID of the user resouece being accessed can be defined as

`request.resource.id == request.principal.id`

Adding this to the policy request a new rule to be created that is just for the `update` and `delete` actions which is for the `user` role and has a single condition.

```yaml
---
apiVersion: api.cerbos.dev/v1
resourcePolicy:
  version: "default"
  resource: "user"
  rules:
    - actions:
        - create
        - read
      effect: EFFECT_ALLOW
      roles:
        - user

    - actions:
        - update
        - delete
      effect: EFFECT_ALLOW
      roles:
        - user
      condition:
        match:
          - expr: request.resource.id == request.principal.id

# ....other conditions
```

Complex logic can be defined in conditions (or sets of conditions) which you can read more about in [the docs](https://docs.cerbos.dev/cerbos/latest/policies/conditions.html).

# Extending tests

Now that you have a conditional policy, you can add these as test cases in the user tests. You can now define multiple `user` resources and principals and create test cases for ensuring the `update` action is allowed when the ID of the principal matches the ID of the resource, aswell as checking that it isn't allowed if the condition is not met.

```yaml
{{#include ./cerbos/tests/user_test.yaml}}
```