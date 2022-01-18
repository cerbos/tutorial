# Running Locally

As the developers of Cerbforce began their investigation of the system the first step was getting a Cerbos instance up and running locally.

## Config File

The first step is to create a server configuration file. The most simple configuration to get up and running using a local folder for storage of policies requires only the port and location to be set.

```yaml
---
server:
  httpListenAddr: ":3592"
storage:
  driver: "disk"
  disk:
    directory: /policies
```

Save this file in a directory eg `/tutorial/config/conf.yaml` and also create an empty policy folder eg `/tutorial/policies`

You can find the full configuration schema in the [Cerbos docs](https://docs.cerbos.dev/cerbos/latest/configuration/index.html).

With the configuration defined there are two ways to run an instance of Cerbos locally:

## Container

If you have Docker you can simply use the published images. You will need to mount the folder created in the above into the container for it to be able to read the policies:

```sh
docker run --rm --name cerbos -d -v /tutorial:/tutorial -p 3592:3592 ghcr.io/cerbos/cerbos:latest server --config=/tutorial/config/conf.yaml
```

## Binary

Alternatively, if you don't have Docker running you can grab the relevant release binary from [here](https://docs.cerbos.dev/cerbos/latest/installation/binary.html), extract it, and then run:

```sh
./cerbos server --config=/tutorial/config/conf.yaml
```


Once started you can open `http://localhost:3592` to see the API documentation.