

# Calling Cerbos

Now that you know the policies are valid, it is time to make your first call to Cerbos to make an authorization check.

## Starting Cerbos
To start you need to launch the server:

```sh
# Using Container
docker run --rm --name cerbos -t -v /tutorial:/tutorial -p 3592:3592 ghcr.io/cerbos/cerbos:latest server --config=/tutorial/config/conf.yaml

# Using Binary
./cerbos server --config=/tutorial/config/conf.yaml
```

Once Cerbos has started up you should see an output confirming that there are 3 policies loaded and ready to start processing authorization checks:

```sh
2022-01-25T09:46:03.370Z	INFO	cerbos.server	maxprocs: Leaving GOMAXPROCS=4: CPU quota undefined
2022-01-25T09:46:03.382Z	INFO	cerbos.index	Found 3 executable policies
2022-01-25T09:46:03.383Z	INFO	cerbos.grpc	Starting gRPC server at :3593
2022-01-25T09:46:03.383Z	INFO	cerbos.http	Starting HTTP server at :3592
````

At this point how you make a request to the Cerbos instance is down to your preference - a simple cURL command could surfice or using a GUI such as Postman also works.


## Cerbos check call

A call to Cerbos contains 3 key bits of information:

1. The Principal - who is making the request
2. The Resources - a map of entities of a resource kind that are they requesting access too
3. The Actions - what actions are they trying to person on the entities

The request payload to the `/api/check` endpoint takes these 3 bit of information as JSON:

```json
{
  "principal": {
    "id": "user_1",     // the user ID
    "roles": ["user"],  // list of roles from user's profile
    "attr": {}          // a map of attributes about the user - not used yet
  },
  "resource": {
    "kind": "contact",  // the type of the resoureces
    "instances": {      // a map of the resource instance(s) being checked
        "contact_1": {  // key is the ID of the resource instance
            "attr": {}  // a map of attributes about the resource - not used yet
        }
    }
  },
  "actions": ["read"]   // the list of actions to be done on the resource
}
```

To make the actual call as a cURL with the default server config:

```sh
curl --location --request POST 'http://localhost:3592/api/check' \
    --header 'Content-Type: application/json' \
    --data-raw '{
      "principal": {
        "id": "user_1",
        "roles": ["user"],
        "attr": {}
      },
      "resource": {
        "kind": "contact",
        "instances": {
            "contact_1": {
                "attr": {}
            }
        }
      },
      "actions": ["read"]
    }'
```

The response object looks as follows where for each instance of the resource the authorization decision for each action is either `EFFECT_ALLOW` or `EFFECT_DENY` depending on the policies:

```json
{
  "resourceInstances": {
    "contact_1": {
      "actions": {
        "read": "EFFECT_ALLOW"
      }
    }
  }
}
```

# Conclusion

Now that you have made the first call to Cerbos you can move on to a way of checking policy logic without having to make individual calls each time by writing unit tests.