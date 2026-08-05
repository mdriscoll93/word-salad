---
icon: file-lines
coverY: 0
layout:
  width: default
  cover:
    visible: true
    size: hero
    mask: none
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
  metadata:
    visible: true
  tags:
    visible: true
  actions:
    visible: true
---

# Prometheus  Origins

Prometheus was started at [SoundCloud](https://soundcloud.com/) in 2012 by Matt Proud and Julius Volz. Both had just left Google with the aim of making SoundCloud a more reliable website.

SoundCloud was in a unique position at the time, having already built its own container orchestration system before open-source solutions like Docker and Kubernetes emerged.

The creators drew heavy inspiration from Google's internal monitoring tool, Borgmon. They were motivated by the distinct challenges of monitoring a dynamic cloud environment, where existing tools fell short in efficiency, querying capabilities, and data models.

As a time-series database and monitoring system, Prometheus is characterized by its dimensional data model—where metrics are uniquely identified by a name and key-value pairs called labels—and its specialized query language, PromQL. To ensure operational simplicity and reliability, Prometheus avoids distributed clustering, opting instead for efficient local storage. Implemented as a static Go binary, it is highly optimized, capable of handling millions of concurrent time series and ingesting over one million samples per second while compressing each sample to just 1–2 bytes.

Today, the core server sits at the center of a broader ecosystem that includes:

* **Alertmanager**: Handles alert routing.
* **Pushgateway**: Supports ephemeral jobs.
* **Exporters**: A collection of tools that translate metrics from third-party applications into the Prometheus format.

Reflecting its structural maturity, Prometheus became the second project to join the Cloud Native Computing Foundation (CNCF) after Kubernetes, eventually graduating as a mature open-source standard.

## Infrastructure Context

At the time, SoundCloud ran much of its infrastructure on traditionally managed machines, yet its in-house cluster for running microservices already consisted of:

* Tens of machines.
* Hundreds of microservices.
* Thousands of microservice instances.

The cluster orchestrator dynamically scheduled service containers onto hosts with available capacity. Every deployment of a new service version led to a complete rescheduling of all of that service's containers onto different hosts. This dynamic nature created significant challenges in monitoring and assessing the health of individual service instances.

## SoundCloud Monitoring Before Prometheus

SoundCloud previously used a wide variety of monitoring systems and time series databases to gain insight into its systems, including Nagios, Ganglia, Munin, StatsD, Graphite, NewRelic, and Cacti. However, these tools struggled to provide detailed insight into dynamically scheduled service instances.

Shortcomings of these existing solutions included:

* **Insufficient data models**: Little or no support for dimensional drill-down.
* **Missing or limited query capabilities**: Inadequate ways of querying collected data.
* **Unsatisfying user interfaces**.
* **Lack of efficiency**: Inefficient data storage and processing.
* **No support for dynamic service discovery**.
* **Lack of integration**: Metrics collection and alerting were not unified.

SoundCloud suffered frequent outages during this period, and diagnosis was often difficult. For example, when a microservice experienced a spike in user-visible latency, existing systems couldn't distinguish whether the spike was caused by a single misbehaving instance or a systematic issue across the entire service.

## Creation of Prometheus

These challenges led Matt and Julius to create Prometheus, which eventually became the primary monitoring solution at SoundCloud. Prometheus was inspired by Google's Borgmon, the system used to monitor Google's cluster orchestrator, Borg. Because SoundCloud’s cluster orchestrator was conceptually similar to Borg, the monitoring concepts transferred well.

Although Prometheus was open-sourced on GitHub in 2012, SoundCloud officially announced it on January 26, 2015. Since then, it has become a [CNCF](https://www.cncf.io/) graduated project and the go-to monitoring solution for organizations worldwide.
