# Course Introduction

## Course Overview, Objectives, Timing, and Assessments

Prometheus is a monitoring system and time series database that is especially well-suited for monitoring dynamic cloud environments. With a powerful data model and query language, as well as integrated alerting and service discovery support, Prometheus allows you to gain better insight into your systems and services and define more precise and meaningful alerts.

This course guides new users through many of Prometheus's major features, best practices, and use cases. Course participants are expected to have basic experience with Linux/Unix system administration, containers and the Docker CLI, as well as some development experience in Go and/or Python. The course will cover the following aspects:

* Prometheus architecture
* Setting up and using Prometheus
* Monitoring core system components and services
* Basic and advanced querying
* Creating dashboards
* Instrumenting services and writing third-party integrations
* Alerting
* Using Prometheus with Kubernetes
* Advanced operational aspects

At the end of this course, you will be able to monitor your applications and infrastructure effectively with Prometheus.

By the end of this course, you should be able to:

* Create queries, alerts, and recording rules using Prometheus Query Language (PromQL)
* Implement basic code instrumentation
* Deploy and use exporters and the pushgateway for monitoring
* Create meaningful labels for timeseries
* Use local and external storage to persist Prometheus data
* Monitor targets through static configuration and with dynamic service discovery
* Deploy Prometheus and its components in HA environments

You should expect to spend approximately **20-25 hours** to complete this course. **You have access to this course for 12 months from the date you registered, or until your subscription expires (whichever happens first).**

The chapters in the course have been designed to build on one another. It is probably best to work through them in sequence; if you skip or only skim some chapters quickly, you may find there are topics being discussed you have not been exposed to yet. But this is all self-paced, you can always go back, thread your own path through the material, and return to exactly where you left off when you come back to start a new session. However, we still suggest you avoid long breaks in between periods of work, as learning will be faster and content retention improved.

You will encounter **ungraded practice questions** throughout the course to reinforce key concepts. At the end of the course, a **graded exam** will assess your overall comprehension of the material.

## Course Details and System Requirements

<details>

<summary>Who is this course for?</summary>

This course is meant for software engineers and systems administrators who want to learn how to monitor and get better insight into their applications and infrastructure using Prometheus.

</details>

<details>

<summary>What the course prepares you for?</summary>

This course teaches Observability concepts and best practices and will prepare you to use Prometheus for monitoring cloud native applications and infrastructure. Attendees will learn to instrument code using examples in Go and Python and how to build and use metrics exporters where instrumentation is either not desired or impossible. The course covers PromQL syntax and best practices when querying, dashboarding, creating recording rules, and when defining precise and meaningful alerts. Operational concerns of running a production Prometheus deployment are also covered, including high availability, federation, local and remote storage, and migrations and integrations with other observability tools. The material maps to the domains and competencies of the Prometheus Certified Associate (PCA) exam, preparing students for the exam.

</details>

<details>

<summary>What prerequesites should you have?</summary>

* Basic experience with Linux/Unix system administration
* Familiarity with common shell commands, such as ls, cd, curl, etc.
* Some knowledge and/or development experience in Go and Python
* Experience with running containers and building container images with Docker
* Familiarity with Kubernetes concepts

</details>

<details>

<summary>What are the lab environment/system requirements?</summary>



* A single machine with Ubuntu 24.04 or similar Linux distribution installed. All of the labs should work on any popular modern Linux distribution (like Red Hat, Arch, or Debian) using Docker, but minor details may be different
* The machine should have at least 4GB of RAM, 2 CPU cores, 50 GB of free disk space
* Docker should be installed, with a non-root user in the docker group so that users can start Docker containers without becoming root. Multiple tutorials for various Linux distributions can be found at the following link: [How to Install and Use Docker](https://www.digitalocean.com/community/tutorial-collections/how-to-install-and-use-docker)
* The user should have sudo access to be able to execute commands as root. A [tutorial on how to create a sudo user on Ubuntu](https://www.digitalocean.com/community/tutorials/how-to-create-a-new-sudo-enabled-user-on-ubuntu) is also available
* The following basic system utilities must be installed (although most of them should be pre-installed by default): **tar, unzip, wget, curl**
* Labs can be run locally, on a VM, or using a cloud provider such as Google Cloud or AWS (free tier will be sufficient to run the labs).

**Note:** When starting long-running processes and containers (like the Prometheus server) throughout this course, we assume that you keep them running for the entire course duration unless noted otherwise. In production setups, you would typically use a supervisor software like [systemd](https://www.freedesktop.org/wiki/Software/systemd/) or a cluster manager like [Kubernetes](https://kubernetes.io/) to keep server processes running in the background. In this course we will not assume a particular deployment system and run components with Docker. Docker enables running and interacting with multiple containers in the same SSH session. Should you feel the need to use multiple SSH sessions, you can use terminal multiplexer tools like [screen](https://www.gnu.org/software/screen/), [tmux](https://github.com/tmux/tmux/wiki), or [byobu](http://byobu.co/), that allow you to create and manage multiple virtual terminals over the same connection. If you are new to terminal multiplexers, we recommend byobu, as it is the most modern and easiest to use.

</details>

<details>

<summary>Lab Requirements - Port Specifications</summary>



* 8080: cAdvisor
* 9090: Main Prometheus server
* 9091: Pushgateway
* 9093: Main Alertmanager
* 9100: Node Exporter
* 9115: Blackbox Exporter
* 10000: Demo service instance 1
* 10001: Demo service instance 2
* 10002: Demo service instance 3
* 12345: Instrumentation exercise example server
* 19090: Cluster-A Prometheus server
* 19093: HA Alertmanager replica 1
* 29090: Cluster-B Prometheus server
* 29093: HA Alertmanager replica 2
* 30000: Kubernetes Prometheus server
* 39090: Global federating Prometheus server.

</details>

<details>

<summary>Formatting Colors &#x26; their meanings:</summary>

In order to make it easier to distinguish the various types of content in the course, we use the color coding and formats below:

<mark style="color:purple;">**Dark blue: text typed at the command line**</mark>

<mark style="color:green;">**Green: Output**</mark>

<mark style="color:$info;">**White : file content**</mark>

<mark style="color:orange;">**Brown : File/Directory names**</mark>

<mark style="color:blue;">**Light blue : Hyperlink**</mark>

</details>

### Course  Support + Other Resources

<details>

<summary>Class  Forum</summary>

One great way to interact with peers taking this course is via the [Class Forum](https://forum.linuxfoundation.org/categories/lfs241-class-forum).

The forum can be used to:\


* Introduce yourself to other peers taking this course.
* Discuss concepts, tools and technologies presented in this course, or related to the topics discussed in the course materials.
* Ask questions or report issues with labs or course content.
* Provide solutions/suggestions to peer learners.
* Review issues that were already raised, including possible solutions - the forum can be a great learning resource as well, and you may find that your questions have already been answered in previous posts.
* Share resources and ideas related to the topics covered in the course.

**Please note that all announcements regarding course updates are posted in the forum, so we highly recommend that you periodically check it out.**\


</details>

<details>

<summary>Customer Support - questions about enrollment, LF Account, etc.</summary>

If you have questions regarding your course enrollment, you can reach out to us via our [Support system](http://trainingsupport.linuxfoundation.org/). You will be required to login with your LF Account, which will help us to quickly locate your account and respond to your request. This will also allow you to track your support request through to resolution, and create an ongoing record of your support requests.

The Linux Foundation Education Customer Support system also offers enhanced functionality, such as:

* Knowledge Base Articles - to help you find a quick response to your commonly asked questions
* Service Request Forms - asking the right questions so that you can get the right answers.

</details>

<details>

<summary>Copyright / Usage</summary>

**Copyright 2025, The Linux Foundation. All rights reserved.**

The training materials provided or developed by The Linux Foundation in connection with the training services are protected by copyright and other intellectual property rights.

Open source code incorporated herein may have other copyright holders and is used pursuant to the applicable open source license.

Although third-party application software packages may be referenced herein, this is for demonstration purposes only and shall not constitute an endorsement of any of these software applications.

All The Linux Foundation training, including all the material provided herein, is supplied without any guarantees from The Linux Foundation. The Linux Foundation assumes no liability for damages or legal action arising from the use or misuse of contents or details contained herein.

Linux is a registered trademark of Linus Torvalds. Other trademarks within this course material are the property of their respective owners.

If you believe The Linux Foundation materials are being used, copied, or otherwise improperly distributed, please email [info@linuxfoundation.org](mailto:) or call +1-415-723-9709 (USA).

\


</details>

### About

{% columns %}
{% column %}
<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}

{% column %}
[The Linux Foundation(opens in a new tab)](https://www.linuxfoundation.org/) is the world's leading home for collaboration on open source software, hardware, standards, and data. Linux Foundation projects are critical to the world's infrastructure, including Linux, Kubernetes, Node.js, ONAP, PyTorch, RISC-V, SPDX, OpenChain, and more.
{% endcolumn %}
{% endcolumns %}

The Linux Foundation focuses on leveraging best practices and addressing the needs of contributors, users, and solution providers to create sustainable models for open collaboration. The Linux Foundation has registered trademarks and uses trademarks. For a list of trademarks of The Linux Foundation, please see its [trademark usage page(opens in a new tab)](https://www.linuxfoundation.org/legal/trademark-usage). Linux is a registered trademark of Linus Torvalds.

{% columns %}
{% column %}
[Cloud Native Computing Foundation (CNCF)(opens in a new tab)](https://www.cncf.io/) is an open source software foundation under the Linux Foundation umbrella dedicated to making cloud native computing universal and sustainable.
{% endcolumn %}

{% column %}
<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

Cloud native computing uses an open source software stack to deploy applications as microservices, packaging each part into its own container, and dynamically orchestrating those containers to optimize resource utilization. Cloud native technologies enable software developers to build great products faster.

CNCF serves as a vendor-neutral home for many of the fastest-growing projects on GitHub, including Kubernetes, Prometheus, and Envoy, fostering collaboration between the industry’s top developers, end users and vendors.

#### Linux Foundation Events

Over 85,000 open source technologists and leaders worldwide gather at Linux Foundation events annually to share ideas, learn and collaborate. Linux Foundation events are the meeting place of choice for open source maintainers, developers, architects, infrastructure managers, and sysadmins and technologists leading open source program offices, and other critical leadership functions.

These events are the best place to gain visibility within the open source community quickly and advance open source development work by forming connections with the people evaluating and creating the next generation of technology. They provide a forum to share and gain knowledge, help organizations identify software trends early to inform future technology investments, connect employers with talent, and showcase technologies and services to influential open source professionals, media, and analysts around the globe.
