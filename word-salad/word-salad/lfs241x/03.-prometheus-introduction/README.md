---
icon: fire
---

# 03. Prometheus Introduction



<details>

<summary>Objectives</summary>

1. Differentiate between  what Prometheus is trying to solve and what it is not.
2. Discuss how/why Prometheus was created.
3. Explain basic Prometheus system architecture.
4. Describe major selling points of Prometheus \~ ie. data model, query language, visualization integrations..

</details>

## <mark style="color:$success;">What Prometheus</mark> <mark style="color:$success;"></mark>_<mark style="color:$success;">is</mark>_

[Prometheus](https://prometheus.io/docs/introduction/overview/) is an **integrated monitoring system and time series database** that aims to solve a broad spectrum of _metrics-based system monitoring needs_:

* **Exposing metrics** from processes, machines, and other components (instrumentation)
* **Collecting and storing that metrics data**
* **Querying, alerting, and dashboarding** based on the collected data.

{% hint style="info" %}
Prometheus is generic enough to monitor all levels of typical IT stacks, from the network and hardware layer all the way up to application-level metrics. Still, it is optimized for operational systems monitoring and is not always suitable for tracking business-level metrics.

<mark style="color:$success;">Prometheus was especially designed to work well with dynamic cloud environments and container orchestrators like Kubernetes.</mark>
{% endhint %}

## <mark style="color:$danger;">What Prometheus</mark> <mark style="color:$danger;"></mark>_<mark style="color:$danger;">is</mark>_ _<mark style="color:$danger;">not</mark>_

Since Prometheus focuses on time series-based operational systems monitoring, it explicitly does not want to solve certain other goals.

### Goals Prometheus is not Solving:

<details>

<summary>Support for event-based monitoring or tracing</summary>

* Prometheus **only** collects metrics represented as data points

</details>

<details>

<summary>Automatic anomaly detection using machine learning or advanced statistics</summary>

* In Prometheus you can configure complex alerting rules, but they have to be explicitly defined.

</details>

<details>

<summary>Automatic Horizontal Scaling</summary>

* While you can scale Prometheus to the largest possible setups, scaling requires more than the push of a button and just adding more nodes to a cluster.

</details>

<details>

<summary>Data Durability</summary>

* Single Prometheus server nodes only store data on their local disk, and data is not automatically replicated to other nodes. Prometheus provides interfaces to provide data durability externally though.

</details>

<details>

<summary>User Management and Authentication</summary>

* Prometheus is a single-user system, and any required authentication and authorization features need to be built around it.

</details>

## Origins of Prometheus

{% content-ref url="prometheus-origins.md" %}
[prometheus-origins.md](prometheus-origins.md)
{% endcontent-ref %}
