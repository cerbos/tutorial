# Cerbos

## Authorization-as-a-Service

One of the key tenants that have allowed Cerbforce to scale is it's the adoption of a microservices architecture where each component can be scaled to meet the exact demands of the system. 

Cerbos has been built as a standalone service which gives it several characteristics that are desirable for such an architecture

- Authorization checks can be made from any system or part of the app stack. No more complicated logic replicating rules - now it is a single call out to Cerbos which returns a simple ALLOW or DENY response for the request.
- All policy decisions are centralized in the Cerbos instances so there is a single location where audit logs can be gathered from.
- The Cerbos instances can be scaled alongside the rest of your services for example as a Kubernetes sidecar

## Policy as Code

As Cerbforce has grown the complexity of authorization rules has required complicated logic to be translated into each language used and hardcoded into each service in the app stack. Any updates require engineering time to go and change the logic, run tests, and then cut a release of every part of the system which is affected. 

Cerbos' approach is to define all policy as human-readable policy definitions held centrally and that is read by all the Cerbos instances. This way any updates or changes to authorization rules can be made once and then all services that call Cerbos for permissions checks get the updated result. No code changes or releases are needed.

## Bring your own identity

Cerbforce has standardized on using Auth0 for authentication across their suite of applications. This contains the user profile information such as what role the user has, which department they belong too and what office they are based in.

Cerbos can consume an identity from any authentication provider be it homegrown or a managed service and can even natively support JWTs including verification.

This profile information from Auth0 is used to construct the user information (called the principal in Cerbos speak due to supporting not user identities also) which is passed in with an authorization call to make policy decisions with.

## Performance

Given the scale of Cerbforce there is concern about how performant Cerbos is be given authorization checks are being made in the blocking path of every request. Several key features of Cerbos have quelled these concerns:

- The Cerbos API is exposed over a highly performant [gRPC](https://docs.cerbos.dev/cerbos/latest/api/index.html) interface to keep overheads low (with an HTTP gateway on top). 
- A recommended approach is a [sidecar deployment](https://docs.cerbos.dev/cerbos/latest/deployment/k8s-sidecar.html) so that each service instance has its Cerbos instance to keep latency as low as possible - calls can even be made over [UNIX sockets](https://github.com/cerbos/demo-rest/blob/main/docker-compose.yaml).
- Cerbos is advocating a modern cloud-native approach to dealing with common infrastructure services such as authorization. This is a proven method - [Microsoft Dapr](https://docs.microsoft.com/en-us/dotnet/architecture/dapr-for-net-developers/dapr-at-20000-feet) is a good example at scale.

