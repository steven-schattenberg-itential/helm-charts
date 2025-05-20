# IAG5 Helm Chart

This chart is designed to enable the easy and rapid deployment of IAG5 environments and
architectures.

## IAG5 Architectures

There are a few different architectures that are available when running IAG5. This chart is capable
of building two of them:

- A simple server architecture that is invoked from a client. The client is not a part of the chart.
- A distributed architecture that includes a server and runners that is invoked from a client. The
client is not a part of the chart.

For information about installing a client please visit the IAG5 repo.

### Simple Server
An IAG5 simple server is a simple All-in-one architecture that can run automations that are invoked from a
client. That client could be a local installation of IAG5, a Torero install, or an Itential Platform
server. It runs all automations in memory and runs on a single pod. It can support TLS connections.

To create this environment the values file must provide the appropriate values for `serverMode` and
`runnerMode`. When the number of servers is greater than zero the chart will create a pod as a
server. When the number of runners is greater than zero the chart will create pods as runners. The
values in `allMode` effect all pods. These configuration sections in values.yaml file can also
contain all of the environment variables for a pod. For example, this will create a simple server:

```yaml
# Set the number of runner replicas to zero
runnerMode:
  replicaCount: 0
  env:
    ...

# Set the number of server replicas to one
serverMode:
  replicaCount: 1
  env:
    - name: GATEWAY_SERVER_DISTRIBUTED_EXECUTION
      value: "false"
    ...

# Configure all pods with these values
allMode:
  env:
    - name: GATEWAY_COMMANDER_ENABLED
      value: "false"
    - name: GATEWAY_STORE_BACKEND
      value: "memory"
```

### Distributed Server
An IAG5 distributed server is a server with a configurable number of runners. The server instructs
the runners to run IAG5 services, the runners obey. The server responds to a client, like the simple
server architecture. That client could be a local installation of IAG5, a Torero install, or an
Itential Platform server. It requires an Etcd cluster that it uses for communication. It consists of
many pods. It can support TLS connections.

The creation of the Etcd cluster is outside of the scope of this chart.

To create this environment the values file must provide the appropriate values for `serverMode` and
`runnerMode`. When the number of servers is greater than zero the chart will create a pod as a
server. When the number of runners is greater than zero the chart will create pods as runners. The
values in `allMode` effect all pods. These configuration sections in values.yaml file can also
contain all of the environment variables for a pod. For example, this will create a distributed
server:

```yaml
# Set the number of runner replicas to the desired number of runners
runnerMode:
  replicaCount: 5
  env:
    ...

# Set the number of server replicas to one
serverMode:
  replicaCount: 1
  env:
    - name: GATEWAY_SERVER_DISTRIBUTED_EXECUTION
      value: "true"
    ...

# Configure all pods with these values
allMode:
  env:
    - name: GATEWAY_COMMANDER_ENABLED
      value: "false"
    - name: GATEWAY_STORE_BACKEND
      value: "etcd"
    - name: GATEWAY_STORE_ETCD_HOSTS
      value: "etcd.default.svc.cluster.local:2379"
    - name: GATEWAY_STORE_ETCD_USE_TLS
      value: "true"
    # As appropriate if using TLS and the above is true
    # The chart will mount these as secrets
    - name: GATEWAY_STORE_ETCD_CA_CERTIFICATE_FILE
      value: /etc/ssl/etcd/ca.crt
    - name: GATEWAY_STORE_ETCD_CERTIFICATE_FILE
      value: /etc/ssl/etcd/tls-client.crt
    - name: GATEWAY_STORE_ETCD_CLIENT_CERT_AUTH
      value: "true"
    - name: GATEWAY_STORE_ETCD_PRIVATE_KEY_FILE
      value: /etc/ssl/etcd/tls-client.key
```

### Run the Chart
Clone this repo, modify the values file appropriately, and install into your Kubernetes environment
by doing the following:

```bash
helm install iag5 ./iag5
```