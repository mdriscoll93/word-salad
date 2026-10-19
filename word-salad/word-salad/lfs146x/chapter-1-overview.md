# Chapter 1 - Overview

## Course Overview, Objectives, Timing, and Assessment

&#x20;

When you deploy Kubernetes, you need to install a networking plug-in implementing the Container Networking Interface (CNI) to provide connectivity between workloads. Cilium is a popular and widely-deployed CNI solution that is now the default across many Kubernetes distributions and cloud provider offerings.

In this course, you will learn the basics of Cilium and how it can be used to connect, observe, and secure Kubernetes clusters. We will start by reviewing the container networking challenges motivating the creation of Cilium, we’ll move on to discussing the architecture of Cilium and how it uses eBPF – a revolutionary Linux kernel technology – to address those challenges.

We will provide a step-by-step guide for installing and setting up Cilium as your CNI. Once installed, we will show how to configure network policies to secure your network and how we can use Hubble to observe network flows. Finally, we will get hands-on experience using some of Cilium’s most popular features, such as support for L7 protocol-aware network policies, transparent encryption, and cluster mesh networking.

By the end of this course, you will understand how Cilium and Hubble work and how they can be used to connect, observe, and secure your cloud native environments.

By the end of this course, you should be able to:

* Describe at a high level what Cilium does and why it’s built using eBPF
* Install Cilium and Hubble into a Kubernetes cluster and verify their operation status
* Write Layer 3/4 and Layer 7 network policies to secure network traffic
* Use Hubble to observe network flows in a Kubernetes cluster
* Use the Hubble UI to visualize network flows as service maps
* Enhance your cluster’s network observability by enabling Cilium Prometheus metrics and integrating those metrics into Grafana dashboards
* Configure Cilium to use transparent encryption for network traffic
* Replace kube-proxy with Cilium
* Connect multiple Kubernetes clusters with Cilium Cluster Mesh
* Create a Cluster Mesh global service with local cluster affinity to implement high availability fail-over across clusters

You should expect to spend approximately **26 hours** to complete this course. You have access to this course for 12 months from the date you registered, or until your subscription expires (whichever happens first).

The chapters in the course have been designed to build on one another. It is probably best to work through them in sequence; if you skip or only skim some chapters quickly, you may find there are topics being discussed you have not been exposed to yet. But this is all self-paced, you can always go back, thread your own path through the material, and return to exactly where you left off when you come back to start a new session. However, we still suggest you avoid long breaks in between periods of work, as learning will be faster and content retention improved.

At the end of each chapter, you will find a series of knowledge check questions. These questions were designed with one main goal in mind: to help you better comprehend the course content and reinforce what you have learned. The end-of-chapter knowledge check questions are not graded. However, at the end of the course, you will need to complete a final exam and achieve a score of 70% or higher to successfully complete the course.

&#x20;

## Course Details and System Requirements

* This course is designed for application developers, systems operators, and security professionals with an interest in learning how to use Cilium to better connect, observe, and secure Kubernetes. Learners should be familiar with basic Kubernetes concepts, Kubernetes operations and the kubectl tool.
* Learners should have some familiarity with Kubernetes operations and have basic experience using the kubectl tool. The course assumes that students are comfortable with basic Kubernetes concepts such as pods, nodes, services, and clusters.
* It is sufficient to have used minikube or kind to deploy a demo microservice application in a development cluster environment.
* To make the most of this course, we highly recommend the free _Introduction to Kubernetes (LFS158)_ course, which covers these prerequisites.
* Upon completion, you will be able to install and use Cilium to better connect, observe and secure your Kubernetes applications. Cluster administrators will be able to confidently install Cilium on a single cluster or in a cluster mesh configuration and to replace kube-proxy for more efficient, scalable networking. Cluster users will be able to use Hubble to observe network activity and then craft L3-L7 network policy to better secure their applications.
* The hands-on exercises require a Kubernetes cluster pre-provisioned without a CNI plugin.
* The cluster hosts must be using a Linux kernel with socket load balancing support (kernel versions v4.19.57, v5.1.16, v5.2.0 or more recent)
* The learners’ primary system should have the helm, kubectl and curl commands available.
* All exercises have been tested using local development clusters based on Kind (v0.25.0) and Minikube (v1.31), as well as Azure’s AKS service. You can find instructions for setting up a Kubernetes cluster that meets the requirements in the Cilium documentation.

In order to make it easier to distinguish the various types of content in the course, we use the color coding and formats below:

**Dark blue: Text typed at the command line**

**Green: Output**

**Black: File content**

**Brown: File/Directory names**

> Class Forum
>
> One great way to interact with peers taking this course is via the Class Forum. The forum can be used in the following ways:
>
> * To introduce yourself to other peers taking this course.
> * To discuss concepts, tools and technologies presented in this course, or related to the topics discussed in the course materials.
> * To ask questions or report issues with hands-on exercises/quizzes or course content.
> * To share resources and ideas related to Cilium.
> * To learn about course updates.
>
> The Class Forum will be reviewed periodically by the Linux Foundation staff, but it is primarily a community resource, not an 'ask the instructor' service.

If you have questions regarding your course enrollment, you can reach out to us via our Support system. You will be required to login with your LF Account, which will help us to quickly locate your account and respond to your request. This will also allow you to track your support request through to resolution, and create an ongoing record of your support requests.

The Linux Foundation Education Customer Support system also offers enhanced functionality, such as:

* Knowledge Base Articles - to help you find a quick response to your commonly asked questions
* Service Request Forms - asking the right questions so that you can get the right answers.
