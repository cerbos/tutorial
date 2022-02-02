# Policy authoring

> The policies created below can be found in `./cerbos/policies`.

## Authentication roles

To begin with Cerbos needs to know about the basic roles which are provided by your authentication provider. In the case of Cerbforce, Auth0 provides a role of either `ADMIN` or `USER` for all profiles. This is important when starting to define access ot resources below - for now just make a note of them.

## Resources

The best place to start with defining policies is listing out all the resoueces and their actions that exist in the system. A resource is an entity type that users are authroized access too.

In the case of Cerbforce some of the resources and actions are as follows:

| Resource | Actions |
| --- | --- |
| User | Create, Read, Update, Delete |
| Company | Create, Read, Update, Delete |
| Contact | Create, Read, Update, Delete |

With this as a start you can begin creating your first Cerbos policy - a Resource Policy.


## Resource policies

Taking the user resource as an example, the most basic resource policy can be defined like below:

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
      effect: EFFECT_ALLOW
      roles:
        - admin         
```

The structure of a resource poliicy requires a name to be set on the `resource` key and then a list of rules are defined. A rule defines a list of actions on the resource, the effect of the rule (`EFFECT_ALLOW` or `EFFECT_DENY`) and then feilds to state who this applies to - in this simple case a list of `roles` which is checked for in the roles of the user making the request.

In this case a request made for a principal with a the role of `user` is granted only `create` and `read` actions whilst an `admin` role can also perform `update`, `delete` actions.

## Wildcard action

To simplify things further, admins to be able to do every action so a special `*` wildcard action can be used to keep things clean:

```yaml
{{#include ./cerbos/policies/user.yaml}}  
```

The `contact` and `company` resources have a similiar structure at this stage and can be modeled as so:

```yaml
{{#include ./cerbos/policies/contact.yaml}}
```

```yaml
{{#include ./cerbos/policies/company.yaml}}
```


## Validating policies

Now with the initial policies in place you can run Cerbos in compile mode which validates the content of the policy files to ensure they are correct.

If you are running Cerbos in a container then mount the folder containing your policies and run the `compile` command pointing to the folder of your policies.

```sh
# Using Container
docker run --rm --name cerbos -t -v /tutorial:/tutorial ghcr.io/cerbos/cerbos:latest compile /tutorial/policies

# Using Binary
./cerbos compile /tutorial/policies
```

If the policies are valid then the process exits with no errors. If there is an issue, the error message points you to where you need to look and the specific problem to fix.

## Conclusion

At this stage a simple Roles-based Access Control (RBAC) model has been deisgned and the policies have been validated - next up is making an authorization call to Cerbos.