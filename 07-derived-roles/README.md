# Derived Roles

The business requirements for Cerbforce state that only an owner of Contacts and Companies are allowed to delete them from the system. With Cerbos, the aim is to keep polcies as simple as possible and not repeat logic across different resources, so in this situation a [Derived Role](https://docs.cerbos.dev/cerbos/latest/policies/derived_roles.html) can help up.


 Derived roles are a way of augmenting the broad roles with are attached to the user in the directory of authentication system with contextual data to provide more fine-grained control at runtime. On every request all the relevant derived role policies are evaluated and those matching roles are 'attached' to the user as Cerbos computes access.

 # Onwer Derived Role

 In the Cerbforce data model, the `contact` and `company` both have an attribute called `ownerId` which is the ID of the user that created the record. Rather than adding a condition to both of these resource policies, we are going to create a derived role which gives the principal and additoinal `owner` role within the context of the request. The policy for this is as follows:

 ```yaml
 ---
apiVersion: "api.cerbos.dev/v1"
description: |-
  Common dynamic roles used within the Cerbforce app
derivedRoles:
  name: cerbforce_derived_roles
  definitions:
    - name: owner
      parentRoles: ["user"]
      condition:
        match:
          expr: request.resource.attr.ownerId == request.principal.id
```

The structure is similar to a resource policy but rather than defining actions with conditions, it defines roles which are an extension of the listed `parentRoles` and can have any number of conditions as with resources.

With this derived role policy setup a resource can import them and then make use of them in rules eg:


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
      effect: EFFECT_ALLOW
      roles:
        - user

    - actions:
        - update
        - delete
      effect: EFFECT_ALLOW
      derivedRoles:
        - owner

    - actions:
        - "*"
      effect: EFFECT_ALLOW
      roles:
        - admin
```