---
type: docs
title: "Pulsar"
linkTitle: "Pulsar"
description: "Detailed documentation on the Pulsar pubsub component"
aliases:
  - "/operations/components/setup-pubsub/supported-pubsub/setup-pulsar/"
---

## Component format
To setup Pulsar pubsub create a component of type `pubsub.pulsar`. See [this guide]({{< ref "howto-publish-subscribe.md#step-1-setup-the-pubsub-component" >}}) on how to create and apply a pubsub configuration.

```yaml
apiVersion: dapr.io/v1alpha1
kind: Component
metadata:
  name: pulsar-pubsub
  namespace: default
spec:
  type: pubsub.pulsar
  version: v1
  metadata:
  - name: host
    value: "localhost:6650"
  - name: enableTLS
    value: "false"
  - name: tenant
    value: "public"
  - name: namespace
    value: "default"
  - name: persistent
    value: "true"
  - name: backOffPolicy
    value: "constant"
  - name: backOffMaxRetries
    value: "-1"

```
## Spec metadata fields

| Field              | Required | Details | Example |
|--------------------|:--------:|---------|---------|
| host               | Y  | Address of the Pulsar broker. Default is `"localhost:6650"` | `"localhost:6650"`
| enableTLS          | N  | Enable TLS.  Default: `"false"` | `"true"`, `"false"`
| tenant          | N  | The topic tenant within the instance. Tenants are essential to multi-tenancy in Pulsar, and spread across clusters.  Default: `"public"` | `"public"`
| namespace          | N  | The administrative unit of the topic, which acts as a grouping mechanism for related topics.  Default: `"default"` | `"default"`
| persistent          | N  | Pulsar supports two kind of topics: [persistent](https://pulsar.apache.org/docs/en/concepts-architecture-overview#persistent-storage) and [non-persistent](https://pulsar.apache.org/docs/en/concepts-messaging/#non-persistent-topics). With persistent topics, all messages are durably persisted on disks (if the broker is not standalone, messages are durably persisted on multiple disks), whereas data for non-persistent topics is not persisted to storage disks. Note: the default retry behavior is to retry until it succeeds, so when you use a non-persistent theme, you can reduce or prohibit retries by defining `backOffMaxRetries` to `0`. Default: `"true"` | `"true"`, `"false"`
| backOffPolicy              | N        | Retry policy, `"constant"` is a backoff policy that always returns the same backoff delay. `"exponential"` is a backoff policy that increases the backoff period for each retry attempt using a randomization function that grows exponentially. Defaults to `"constant"`. | `constant`、`exponential` |
| backOffDuration            | N        | The fixed interval only takes effect when the policy is constant. There are two valid formats, one is the fraction with a unit suffix format, and the other is the pure digital format that will be processed as milliseconds. Valid time units are "ns", "us" (or "µs"), "ms", "s", "m", "h". Defaults to `"5s"`. | `"5s"`、`"5000"`                  |
| backOffInitialInterval     | N        | The backoff initial interval on retry. Only takes effect when the policy is exponential. There are two valid formats, one is the fraction with a unit suffix format, and the other is the pure digital format that will be processed as milliseconds. Valid time units are "ns", "us" (or "µs"), "ms", "s", "m", "h". Defaults to `"500"`                         | `"50"`                       |
| backOffMaxInterval         | N        | The backoff initial interval on retry. Only takes effect when the policy is exponential. There are two valid formats, one is the fraction with a unit suffix format, and the other is the pure digital format that will be processed as milliseconds. Valid time units are "ns", "us" (or "µs"), "ms", "s", "m", "h". Defaults to `"60s"`     | `"60000"`                     |
| backOffMaxRetries          | N        | The maximum number of retries to process the message before returning an error. Defaults to `"0"` which means the component will not retry processing the message. `"-1"` will retry indefinitely until the message is processed or the application is shutdown. Any positive number is treated as the maximum retry count. | `"3"` |
| backOffRandomizationFactor | N        | Randomization factor, between 1 and 0, including 0 but not 1. Randomized interval = RetryInterval * (1 ± backOffRandomizationFactor). Defaults to `"0.5"`.                 | `"0.5"`                       |
| backOffMultiplier          | N        | Backoff multiplier for the policy. Increments the interval by multiplying it with the multiplier. Defaults to `"1.5"`         | `"1.5"`      |
| backOffMaxElapsedTime      | N        | After MaxElapsedTime the ExponentialBackOff returns Stop. There are two valid formats, one is the fraction with a unit suffix format, and the other is the pure digital format that will be processed as milliseconds. Valid time units are "ns", "us" (or "µs"), "ms", "s", "m", "h". Defaults to `"15m"` | `"15m"` |

## Create a Pulsar instance

{{< tabs "Self-Hosted" "Kubernetes">}}

{{% codetab %}}
```
docker run -it \
  -p 6650:6650 \
  -p 8080:8080 \
  --mount source=pulsardata,target=/pulsar/data \
  --mount source=pulsarconf,target=/pulsar/conf \
  apachepulsar/pulsar:2.5.1 \
  bin/pulsar standalone

```
{{% /codetab %}}

{{% codetab %}}
Refer to the following [Helm chart](https://pulsar.apache.org/docs/en/kubernetes-helm/) Documentation.
{{% /codetab %}}

{{< /tabs >}}

## Related links
- [Basic schema for a Dapr component]({{< ref component-schema >}})
- Read [this guide]({{< ref "howto-publish-subscribe.md#step-2-publish-a-topic" >}}) for instructions on configuring pub/sub components
- [Pub/Sub building block]({{< ref pubsub >}})