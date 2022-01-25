# Policy Authoring

> The policies created below can be found in `./cerbos/policies`.

## Authentication Roles

To begin with Cerbos needs to know about the basic roles which are provided by your authentication provider. In the case of Cerbforce, Auth0 provides a role of either `ADMIN` or `USER` for all profiles. This will be important when starting to define access ot resources below - for now just make a note of them.

## Resources

The best place to start with defining policies is listing out all the resoueces and their actions that exist in the system. A resource is an entity type that users will be authroized access too.

In the case of Cerbforce some of the resources and actions are as follows:

| Resource | Actions |
| --- | --- |
| User | Create, Read, Update, Delete |
| Company | Create, Read, Update, Delete |
| Contact | Create, Read, Update, Delete |

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
      effect: EFFECT_ALLOW
      roles:
        - admin         
```

The structure of a resource poliicy requires a name to be set on the `resource` key and then a list of rules are defined. A rule defines a list of actions on the resource, the effect of the rule (`EFFECT_ALLOW` or `EFFECT_DENY`) and then feilds to state who this applies to - in this simple case a list of `roles` which is checked for in the roles of the user making the request.

In this case a request made for a principal with a the role of `user` is granted only `create` and `read` actions whilst an `admin` role can also perform `update`, `delete` actions.

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

Our `contact` and `company` resources have a similiar structure at this stage and can be modeled as so:

```yaml
---
apiVersion: api.cerbos.dev/v1
resourcePolicy:
  version: "default"
  resource: "contact"
  rules:
    - actions:
        - create
        - read
        - update
        - delete
      effect: EFFECT_ALLOW
      roles:
        - user

    - actions:
        - "*"
      effect: EFFECT_ALLOW
      roles:
        - admin
```

```yaml
---
apiVersion: api.cerbos.dev/v1
resourcePolicy:
  version: "default"
  resource: "company"
  rules:
    - actions:
        - create
        - read
        - update
        - delete
      effect: EFFECT_ALLOW
      roles:
        - user

    - actions:
        - "*"
      effect: EFFECT_ALLOW
      roles:
        - admin
```


## Validating Policies

Now with our initial policies in place we can run Cerbos in compile mode which validates the content of the policy files to ensure they are correct.

If you are running Cerbos in a container then mount the folder containing your policies and run the `compile` command pointing to the folder of your policies.

```sh
# Using Container
docker run --rm --name cerbos -t -v /tutorial:/tutorial ghcr.io/cerbos/cerbos:latest compile /tutorial/policies

# Using Binary
./cerbos compile /tutorial/policies
```

If the policies are valid then the process will exit with no errors. If there is an issue, the error message will point you to where you need to check and the specific problem to fix.

## Conclusion

At this stage a simple Roles-based Access Control (RBAC) model has been deisgned and the policies have been validated - next up is making our first authorization check to Cerbos.