---
layout:
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# Chapter 3 - Install Cilium

## Chapter Overview and Learning Objectives

In this chapter, we’ll dive right in and install Cilium into a Kubernetes cluster! Then, we’ll review Cilium’s architecture, learning how the components work together to enhance network connectivity, observability, and security in Kubernetes.

By the end of this chapter, you’ll be able to:

* List the supported installation methods for Cilium.
* Install Cilium into a Kubernetes cluster using the Cilium CLI tool.
* Use the Cilium CLI tool to check Cilium status.
* Use the Cilium CLI tool to run connectivity tests to verify Cilium operation.
* Discuss the Cilium components running in a Kubernetes cluster.
* Understand the Cilium concepts of Endpoints and Identity.

### Cilium Versions

Cilium is under active development, and the labs in this course were developed using Cilium 1.16.x releases. If you are using this course but find the Cilium and Cilium CLI versions installed differ from those listed in the provided command output, don’t be alarmed! The labs in this course cover the most mature Cilium features. The instructions here should carry forward without issue for the foreseeable future, even after the 1.16 release is no longer current. As long as you install the default Cilium release provided by the latest available Cilium CLI tool version, as done in this lab, the subsequent labs in this course should work as expected.
