# Policy Authoring

> The policies created below can be found in `./cerbos/policies`.

## Authentication Roles

To begin with Cerbos needs to know about the basic roles which are provided by your authentication provider. In the case of Cerbforce, Auth0 provides a role of either `ADMIN` or `USER` for all profiles. This will be important when starting to define access ot resources below - for now just make a note of them.

## Resources

The best place to start with defining policies is listing out all the resoueces and their actions that exist in the system. A resource is an entity type that users will be authroized access too.

In the case of Cerbforce some of the resources and actions are as follows:

| Resource | Actions |
| --- | --- |
| User | Create, Read, Update, Delete, Reset Password |
| Company | Create, Read, Update, Archive |
| Contact | Create, Read, Update, Archive, Publish, Assign to Company |

With this as a start we can begin creating our first Cerbos policy - a Resource Policy.


## Resource Policies

Taking the user resource outlined above, the most basic resource policy can be defined like below:

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
        - create
        - read
        - update
        - delete
        - update_password
      effect: EFFECT_ALLOW
      roles:
        - admin         
```

The structure of a resource poliicy requires a name to be set on the `resource` key and then a list of rules are defined. A rule defines a list of actions on the resource, the effect of the rule (`EFFECT_ALLOW` or `EFFECT_DENY`) and then feilds to state who this applies to - in this simple case a list of `roles` which is checked for in the roles of the user making the request.

In this case a request made for a principal with a the role of `user` is granted only `create` and `read` actions whilst an `admin` role can also perform `update`, `delete` and `update_password` actions.

## Wildcard Action

To simplify things further, we want admins to be able to do every action so a special `*` wildcard action can be used to keep things clean:

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
        - '*'
      effect: EFFECT_ALLOW
      roles:
        - admin         
```

## Conclusion

At this stage a simple Roles-based Access Control (RBAC) model has been implmeneted, but buisness requirements are more complex than that and so some additional conditions are required which will be added in the next section.