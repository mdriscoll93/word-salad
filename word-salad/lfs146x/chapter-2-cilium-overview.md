# Chapter 2 - Cilium Overview

## Cilium

## Chapter Overview and Learning Objectives

To get started, let’s review what Cilium is, discuss the container networking challenges and the underlying eBPF platform that inspired Cilium’s design.

By the end of this chapter you should be able to:

* Describe at a high level what Cilium does and why it’s built using eBPF.
* Appreciate how the dynamic nature of Kubernetes creates challenges for networking at production scale.
* List the most commonly used Cilium capabilities.

## What is Cilium?

[Cilium](https://cilium.io/) is an open source, cloud native solution for providing, securing, and observing network connectivity between workloads. In this course, we'll focus on learning how to use Cilium in Kubernetes environments where the aforementioned workloads are Kubernetes pods. However, it's important to point out that the benefits of Cilium aren't limited to Kubernetes environments.

In a Kubernetes environment, Cilium acts as a networking plugin that provides connectivity between pods. It provides security by enforcing network policies and through transparent encryption, and the Hubble component of Cilium provides deep visibility into network traffic flows.

Thanks to [eBPF](https://ebpf.io/what-is-ebpf/), Cilium’s networking, security, and observability logic can be programmed directly into the kernel, making Cilium and Hubble’s capabilities entirely transparent to application workloads. These will be containerized workloads in a Kubernetes cluster, though Cilium can also connect traditional workloads such as virtual machines and standard Linux processes.

## Solving the Challenges of Container Networking at Scale

In the highly dynamic and complex world of microservices, thinking about networking primarily in terms of IP addresses and ports can lead to frustration. Using traditional networking tool implementations can be highly inefficient, providing only coarse-grained visibility and filtering and limiting your ability to troubleshoot and secure your container networks. These are [challenges Cilium was created to solve](https://cilium.io/blog/2018/04/24/cilium-security-for-age-of-microservices/).

{% embed url="https://cilium.io/blog/2018/04/24/cilium-security-for-age-of-microservices/" %}
A story of beginnings
{% endembed %}

{% embed url="https://docs.cilium.io/en/stable/reference-guides/bpf/index.html" %}
Developer Reference Guide
{% endembed %}

From inception, Cilium was designed for large-scale, highly dynamic containerized environments. It natively understands container and Kubernetes identities and parses API protocols like HTTP, gRPC, and Kafka, providing visibility and security that is simpler and more powerful than a traditional firewall.

### Built on top of eBPF

Using [eBPF](https://ebpf.io/) as an underlying engine, Cilium creates a networking stack optimized for running microservices on platforms like Kubernetes. eBPF enables Cilium’s powerful security visibility and control logic to be dynamically inserted within the Linux kernel. eBPF makes the Linux kernel programmable so that applications like Cilium can hook into Linux kernel subsystems, bringing user space application context to kernel operations.

Because eBPF runs inside the Linux kernel, Cilium security policies can be applied and updated without changing the application code or container configuration. eBPF programs hook into the Linux network datapath and can be used to drop packets based on network policy rules as the packet enters the network socket.

<figure><img src="../../.gitbook/assets/image (17).png" alt=""><figcaption><p><strong>eBPF Linux Kernel Hooks (by Isovalent, Inc.)</strong></p></figcaption></figure>

eBPF enables visibility into and control over systems and applications at a level of granularity and efficiency that was not possible before. It does so in a completely transparent way, without requiring the application to change. Cilium harnesses the power of eBPF by layering an efficient identity concept, bringing Kubernetes contextual information, like metadata labels, to eBPF-powered networking logic.

So now that we know what Cilium is, what problems it aims to solve, and how eBPF empowers it, let’s discuss what Cilium can do.

## Cilium Capabilities: Overview

Cilium has a wide range of features for connecting traffic between workloads and observing and securing that traffic. Let’s review some of the most commonly used capabilities.

## Networking

Cilium provides network connectivity, allowing communication between pods and other components (within or outside a Kubernetes cluster). It implements a simple flat Layer 3 network that can span multiple clusters and connect all application containers.

By default, Cilium supports an **overlay** networking model, where a virtual network spans all hosts. Traffic in an overlay network is encapsulated for transport between different hosts. This mode is chosen as the default because it requires minimal infrastructure and integration and only IP connectivity between hosts.

Cilium also offers the option of a **native routing** networking model, using the regular routing table on each host to route traffic to pod (or external) IP addresses. This mode is for advanced users and requires awareness of the underlying networking infrastructure. It works well with native IPv6 networks, cloud network routers, or pre-existing routing daemons.

## Identity-aware Network Policy Enforcement

Network policies define which workloads are permitted to communicate with one another, securing your deployment by preventing unexpected traffic. Cilium can enforce both native Kubernetes NetworkPolicies, and an enhanced CiliumNetworkPolicy resource type.

Traditional firewalls secure workloads by filtering on IP addresses and destination ports. In a Kubernetes environment, this requires manipulating the firewalls (or iptables rules) on all node hosts whenever a pod is started anywhere in the cluster to rebuild firewall rules corresponding to desired network policy enforcement. This doesn’t scale well.

To avoid this situation, Cilium assigns an identity to groups of application containers based on relevant metadata, such as Kubernetes labels. The identity is then associated with all network packets emitted by the application containers, allowing eBPF programs to efficiently validate the identity at the receiving node – without using any Linux firewall rules. For example, when a deployment is scaled up, and a new pod is created somewhere in the cluster, the new pod shares the same identity as the existing pods. The eBPF program rules corresponding to network policy enforcement do not have to be updated again, as they already know about the pod’s identity!

While traditional firewalls operate at Layers 3 and 4, Cilium can also secure modern Layer 7 application protocols such as REST/HTTP, gRPC, and Kafka (in addition to enforcing at Layers 3 and 4). It provides the ability to enforce network policy corresponding to application protocol request conditions such as:

* Allow all HTTP requests with method `GET` and path `/public/.*`. Deny all other requests.
* Require the HTTP header `X-Token: [0-9]+` to be present in all REST calls.

We will get hands-on crafting a Layer 7 policy using `CiliumNetworkPolicies` in Chapter 4.

### Transparent Encryption

In-flight data encryption between services is now a requirement in many regulation frameworks such as PCI or HIPAA. Cilium supports simple-to-configure transparent encryption, using IPSec or WireGuard, that when enabled, secures traffic between nodes without requiring reconfiguring any workload. We’ll discuss this in more detail in Chapter 7.

### Multi-cluster Networking

Cilium’s Cluster Mesh capabilities make it easy for workloads to communicate with services hosted in different Kubernetes clusters. You can make services highly available by running them in clusters in different regions, using Cilium Cluster Mesh to connect them. You’ll learn more about this in Chapter 9.

## Load Balancing

Cilium implements distributed load balancing for traffic between application containers and external services. As you’ll see later in this course, Cilium can [fully replace](https://cilium.io/blog/2020/06/22/cilium-18/#kubeproxy-removal) components such as kube-proxy and be used as a [standalone load balancer](https://cilium.io/blog/2022/04/12/cilium-standalone-L4LB-XDP/). Load balancing is implemented in eBPF using efficient hash tables, allowing for almost unlimited scale. You’ll work with this feature more in Chapter 8 when we learn how to use Cilium to replace kube-proxy.

## Enhanced Network Observability

While we’ve learned to love tools like **tcpdump** and **ping**, which will always find a special place in our hearts, they are just not up to the task of troubleshooting networking issues in dynamic Kubernetes cluster environments. Cilium strives to provide observability tooling that lets you quickly identify and fix cluster networking problems.

Towards that end, Cilium includes a dedicated network observability component called Hubble. Hubble makes use of Cilium’s identity concept to make it easy to filter traffic in an actionable way and provides:

* Visibility into network traffic at Layer 3/4 (IP address and port) and Layer 7 (API Protocol).
* Event monitoring with metadata: When a packet is dropped, the tool reports not only the source and destination IP but also the full label information of both the sender and receiver, among other information.
* Configurable Prometheus metrics exports.
* A graphical UI to visualize the network traffic flowing through your clusters.

## Enhanced Network Observability

While we’ve learned to love tools like **tcpdump** and **ping**, which will always find a special place in our hearts, they are just not up to the task of troubleshooting networking issues in dynamic Kubernetes cluster environments. Cilium strives to provide observability tooling that lets you quickly identify and fix cluster networking problems.

Towards that end, Cilium includes a dedicated network observability component called Hubble. Hubble makes use of Cilium’s identity concept to make it easy to filter traffic in an actionable way and provides:

* Visibility into network traffic at Layer 3/4 (IP address and port) and Layer 7 (API Protocol).
* Event monitoring with metadata: When a packet is dropped, the tool reports not only the source and destination IP but also the full label information of both the sender and receiver, among other information.
* Configurable Prometheus metrics exports.
* A graphical UI to visualize the network traffic flowing through your clusters.

<figure><img src="../../.gitbook/assets/image (18).png" alt=""><figcaption><p>Hubble UI</p></figcaption></figure>

## Prometheus Metrics

Cilium and Hubble export metrics about network performance and latency via Prometheus so that you can integrate Cilium metrics into your existing dashboards.

<figure><img src="../../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

## Service Mesh

You’ve already seen that Cilium supports load-balancing between services, application layer visibility, and a variety of security-related features, all of which are features of a Kubernetes service mesh. Cilium also supports both Kubernetes Ingress and Gateway API to provide a full suite of [service mesh](https://isovalent.com/blog/post/cilium-service-mesh/) features without requiring the overhead of sidecar containers injected into every pod.

## Knowledge Check - Quiz 1 Chapter 2

1. Which of the following does Cilium provide?
   1. Network Policy Enforcement
   2. Visibility into traffic at L3/L4 and L7
   3. Networking between Kubernetes Pods
2. What is the name of the Cilium component that provides visibility into network traffic?
   1. Hubble
3. Cilium can generate Prometheus metrics showing you information about network performance and latency.
   1. True
4. Which of these are characteristics of eBPF?
   1. It allows dynamic changes to the kernel
   2. It enables high-performance networking with security and observability built-in
   3. It can be used to drop network packets that are forbidden by the network policy
