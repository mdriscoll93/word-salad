---
description: ':]'
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

## What Prometheus _is_

[Prometheus](https://prometheus.io/docs/introduction/overview/) is an **integrated monitoring system and time series database** that aims to solve a broad spectrum of _metrics-based system monitoring needs_:

* **Exposing metrics** from processes, machines, and other components (instrumentation)
* **Collecting and storing that metrics data**
* **Querying, alerting, and dashboarding** based on the collected data.

{% hint style="info" %}
Prometheus is generic enough to monitor all levels of typical IT stacks, from the network and hardware layer all the way up to application-level metrics. Still, it is optimized for operational systems monitoring and is not always suitable for tracking business-level metrics.

<mark style="color:$success;">Prometheus was especially designed to work well with dynamic cloud environments and container orchestrators like Kubernetes.</mark>
{% endhint %}

## What Prometheus is not

Since Prometheus focuses on time series-based operational systems monitoring, it explicitly does not want to solve certain other goals.

<details>

<summary></summary>



</details>

<br>
