# Introduction to Cerbos

## Authorization as a Service

One of the key tenants that have allowed Cerbforce to scale is it's the adoption of a microservices architecture where each component can be scaled to meet the exact demands of the system. 

Cerbos has been built as a standalone service which gives it several characteristics that are desirable for such an architecture

- Authorization checks can be made from any system or part of the application stack. No more complicated logic replicating rules - now it is a single call out to Cerbos which returns a simple ALLOW or DENY response for the request.
- All policy decisions are centralized in the Cerbos instances so there is a single location where audit logs can be gathered from.
- The Cerbos instances can be scaled alongside the rest of your services for example as a Kubernetes sidecar

## Policy as Code

As Cerbforce has grown the complexity of authorization rules has required complicated logic to be translated into each language used and hardcoded into each service in the application stack. Any updates require engineering time to go and change the logic, run tests, and then cut a release of every part of the system which is affected. 

Cerbos' approach is to define all policy as human-readable policy definitions held centrally and that is read by all the Cerbos instances. This way any updates or changes to authorization rules can be made once and then all services that call Cerbos for permissions checks get the updated result. No code changes or releases are needed.

## Bring your own Identity

Cerbforce has standardized on using Auth0 for authentication across their suite of applications. Cerbos can consume an identity from any authentication provider be it homegrown or a managed service. 

## Performance