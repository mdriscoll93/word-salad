# Observability

## Learning Objectives

By the end of this chapter, you should be able to:

* Explain why observability is important
* Discuss the benefits and downsides of different observability signals
* Identify example implementations for monitoring observability signals
* Explain how Prometheus fits into the observability landscape

### IT Systems Landscape

From small companies to large corporations, most organizations depend on IT systems, consisting of software, infrastructure, and services for their daily business. This is true whether a company is in the IT sector itself or not.

As a consequence of IT systems becoming a vital part of many businesses, it is becoming increasingly important to ensure that these systems work reliably and as expected.

## IT Systems Health

Whenever an IT system is vital for the fulfillment of business operations (or some other endeavor), you will want to ensure that the system's behavior is:

* Reliable
* Fast
* Efficient
* Correct

**However, the reality is that systems can be brittle,complex, and hard to understand and maintain**. Modern cloud computing, microservice application architecture, and distributed systems come with additional benefits and challenges. Modern applications are often designed to be more resilient, better performant, and more secure but require more visibility into the health of logical (software) and physical (infrastructure) layers. Partial failures are the norm, rather than the exception, in large, distributed systems. Even if the system as a whole is still performing its job, it might be in a partially degraded state where it works inefficiently or is about to fail completely.

## Examples of Systems Problems

* put
* some
* here

### Traditional Monitoring: Check-Based Fault Detection

Check-based fault detection is a type of monitoring in which the monitoring system executes periodic check scripts on hosts to ensure that the hosts and services running on them are currently up, running, and healthy (for example: is the Apache web server process running?). When a check fails, the monitoring system sends a notification to an operator who then takes action. The most well-known example of a check-based monitoring system is [Nagios](https://www.nagios.org/), which presents its view of the current health of a host and its services like this:

<figure><img src="../../.gitbook/assets/image (20).png" alt=""><figcaption><p><strong>Nagios Host Check View - Screenshot</strong></p></figcaption></figure>

In these check-based monitoring tools, there is usually little emphasis on historical data or basing fault detection on a unified, aggregated, or otherwise flexibly-derived view of an entire system or service over time and across machines. Instead, they mostly focus on reporting only the current and local health of components. Check-based tools also fall into the category of **black-box monitoring software**, since they probe a service from the outside rather than gathering detailed instrumentation from inside a service. Thus, the level of insight they can provide is limited.

Check-based tools like Nagios were originally built to monitor relatively static computing environments, in which machines had a designated purpose (database server, web server, proxy server, etc.) and where the overall topology changed infrequently. Because of their limited abilities and often static configuration, they break down in modern dynamic computing environments, especially when cluster orchestrators like Kubernetes come into play. Besides not coping well with the dynamic nature of such environments, check-based systems also don't provide deep enough insight into the health of the components they monitor to formulate the most precise and actionable alerts.

### What is Observability

Lately, the term "observability" has sprung up in the IT world. It generally refers to the measurable outputs a system provides to help understand and respond to its behavior. Systems need to be designed and built with mechanisms that make them observable, which often requires additional automation or instrumentation of the infrastructure or code.

Observability data is collected and used to predict and detect faults that could impede business operations, providing continuous insight into the health of systems and their environments. When faults are detected, this data can also trigger alerts for individuals or automated remediation systems.. Observability software, such as Prometheus, helps you do both. In addition to detecting faults, these tools help analyze overall system performance, which can lead to operational improvements.
