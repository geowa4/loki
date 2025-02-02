# Fluent-bit Loki chart

This chart install the fluent-bit application to ship logs to Loki. It defines daemonset on a [Kubernetes](http://kubernetes.io) cluster using the [Helm](https://helm.sh) package manager.

## Installing the Chart

> If you don't have `Helm` installed locally, or `Tiller` installed in your Kubernetes cluster, read the [Using Helm](https://docs.helm.sh/using_helm/) documentation to get started.
To install the chart with the release name `my-release` using our helm repository:

```bash
helm repo add loki https://grafana.github.io/loki/charts
helm upgrade --install my-release loki/fluent-bit \
    --set loki.serviceName=loki.svc.cluster.local
```

The command deploys fluent-bit on the Kubernetes cluster with the default configuration. The [configuration](#configuration) section lists the parameters that can be configured during installation.

To configure the chart to send to [Grafana Cloud](https://grafana.com/products/cloud) use:

```bash
helm upgrade --install my-release loki/fluent-bit \
    --set loki.serviceName=logs-us-west1.grafana.net,loki.servicePort=80,loki.serviceScheme=https \
    --set loki.user=2830,loki.password=1234
```

> **Tip**: List all releases using `helm list`

To install a custom tag use the following command:

```bash
helm upgrade --install my-release loki/fluent-bit \
    --set image.tag=<custom tag>
```

The full list of available tags on [docker hub](https://cloud.docker.com/u/grafana/repository/docker/grafana/fluent-bit-plugin-loki).

Alternatively you can install the full [Loki stack](../loki-stack) (Loki + fluent-bit) using:

```bash
helm upgrade --install my-release loki/loki-stack \
    --set fluent-bit.enabled=true,promtail.enabled=false
```

This will automatically configured the `loki.serviceName` configuration field to the newly created Loki instance.

## RBAC

By default, `rbac.create` is set to true. This enable RBAC support in fluent-bit and must be true if RBAC is enabled in your cluster.

The chart will take care of creating the required service accounts and roles for fluent-bit.

If you have RBAC disabled, or to put it another way, ABAC enabled, you should set this value to `false`.

## Uninstalling the Chart

To uninstall/delete the `my-release` deployment:

```bash
helm delete my-release
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

## Configuration

The following tables lists the configurable parameters of the fluent-bit chart and their default values.

For more details, read the [fluent-bit documentation](../../../cmd/fluent-bit/README.md)

| Parameter                | Description                                                                                        | Default                          |
|--------------------------|----------------------------------------------------------------------------------------------------|----------------------------------|
| `loki.serviceName`       | The address of the Loki service.                                                                   | `"${RELEASE}-loki"`              |
| `loki.servicePort`       | The port of the Loki service.                                                                      | `3100`                           |
| `loki.serviceScheme`     | The scheme of the Loki service.                                                                    | `http`                           |
| `loki.user`              | The http basic auth username to access the Loki service.                                           |                                  |
| `loki.password`          | The http basic auth password to access the Loki service.                                           |                                  |
| `config.port`            | the fluent-bit port to listen. (This is mainly used to serve metrics)                              | `2020`                           |
| `config.loglevel`        | the fluent-bit log level (debug,info,warn,error).                                                  | `warn`                           |
| `config.lineFormat`      | The line format to use to send a record (json/key_value)                                           | `json`                           |
| `config.removeKeys`      | The list of key to remove from each record                                                         | `[removeKeys,stream]`            |
| `config.labels`          | A set of labels to send for every log                                                              | `'{job="fluent-bit"}'`           |
| `config.labelMap`        | Mapping of labels from a record. See [fluent-bit documentation](../../../cmd/fluent-bit/README.md) |                                  |
| `affinity`               | [affinity][affinity] settings for pod assignment                                                   | `{}`                             |
| `annotations`            | Annotations to add to Kubernetes resources.                                                        | `{}`                             |
| `deploymentStrategy`     | The deployment strategy to use with the daemonset                                                  | `RollingUpdate`                  |
| `image.repository`       | The fluent-bit docker image repository                                                             | `grafana/fluent-bit-plugin-loki` |
| `image.tag`              | The fluent-bit docker image tag                                                                    | `0.1`                            |
| `image.pullPolicy`       | The fluent-bit docker image pull policy                                                            | `IfNotPresent`                   |
| `nodeSelector`           | fluent-bit [node labels][nodeSelector] for pod assignment                                          | `{}`                             |
| `podLabels`              | additional fluent-bit pod labels                                                                   | `{}`                             |
| `podAnnotations`         | additional fluent-bit pod annotations                                                              | `Prometheus discovery`           |
| `rbac.create`            | Activate support for RBAC                                                                          | `true`                           |
| `resources`              | Resource requests/limit                                                                            |                                  |
| `tolerations`            | [Toleration][toleration] labels for pod assignment                                                 | `no schedule on master nodes`    |
| `volumes`                | [Volume]([volumes]) to mount                                                                       | `host containers log`            |
| `volumeMounts`           | Volume mount mapping                                                                               |                                  |
| `serviceMonitor.enabled` | Create a [Prometheus Operator](operator) serviceMonitor resource for fluent-bit                    | `false`                          |


[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[nodeSelector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#nodeselector
[affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity
[volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[operator]: https://github.com/coreos/prometheus-operator
