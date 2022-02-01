# Principal Policies

The final type of policy that Cerbos supports is a principal policy which is a special type which allows user specific overrides to be defined.

In the case of Cerbforce there is a Data Protection Officer (DPO) that handles any data deletion requests. By default they would not have any delete access to contacts unless they were the owner of the record or have the `admin` role. To overcome this a principal policy has been created which targets their userId and overrides this for the delete action on a contact resource:

```yaml
{{#include ./cerbos/policies/dpo.yaml}}
```

With this policy in place, when an authorization check is made with the principal ID of `dpo1` the delete action on a `contact` resource will be overriden to be allowed.