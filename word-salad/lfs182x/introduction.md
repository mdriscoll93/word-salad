---
description: an overview of software supply chain security
icon: '1'
---

# Introduction

**Compromises** in the software supply chain have been on the rise over the past decade. Attackers have, for instance, distributed compilers with backdoors added (example: XcodeGhost Compromise), broken into software build systems to inject malicious code  (example: SolarWinds Attack), and hijacked automatic update systems to distribute malware. The count of publicly reported software supply chain compromises, depending on the methodology, numbers in the hundreds or thousands. See the figure below for a graph describing the growth of software supply chain attacks.

<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption><p><strong>Count of Software Supply Chain Attacks by Year Reported</strong> <br>Data Source: Dan Geer, Bentz Tozer, and John Speed Meyers,<br>“Counting Broken Links: A Quant’s View of Software Supply Chain Security,” USENIX ;login:, December 2020.</p></figcaption></figure>

Countering these compromises through prevention, mitigation, and remediation has therefore taken on increasing urgency. Founded in 2020, the [Open Source Security Foundation](https://openssf.org/) (OpenSSF) has begun to devise improved defenses against software supply chain attacks. As a cross-industry collaboration, the OpenSSF partners with private companies, government agencies, and individuals to support their mission of proactively handling security. The [**Sigstore project**](https://www.sigstore.dev/) is one of these improved defenses, providing a method for guaranteeing the end-to-end integrity of software artifacts.

This chapter defines software supply chain security and provides examples of attacks on the software supply chain. You’ll become acquainted with several concepts and terms associated with software supply chain security. Finally, we’ll dive into the motivation and history of the Sigstore project and an overview of the technical architecture.

## Learning Objectives

* Define software supply chain security.
* Have an understanding of key software supply chain security concepts and terms.
* Discuss the motivation and history of the Sigstore project.
* Explain the overall architecture of the Sigstore project.

### What is Software Supply Chain Security?

It can be all too easy to label all software issues as supply chain security issues. One might think: All security issues have to be introduced somewhere in the software’s supply chain or how else would these vulnerabilities end up in the finished software? But software supply chain security has a narrower, technical meaning: security issues introduced by the third-party components and technologies used to write, build, and distribute software. A generic example can help illustrate the difference. Imagine ACME company unintentionally creates a SQL injection vulnerability in a piece of software that ACME company distributes. This is not a software supply chain security issue. Code from ACME’s own developers is responsible for this security issue. But should ACME company use an open source software component that has been maliciously tampered with to send sensitive secrets to an attacker when the code was built, then ACME would be the victim of a software supply chain attack. In that case, the supply chain of ACME’s developers is the origin of the security issue.

Software supply chain compromises can involve both malicious and unintentional vulnerabilities. The insertion of malicious code anywhere along the supply chain poses a severe risk to downstream users, but unintentional vulnerabilities in the software supply chain can also lead to risks should some party choose to exploit these vulnerabilities. For instance, the [log4j open source software vulnerability](https://www.washingtonpost.com/technology/2021/12/20/log4j-hack-vulnerability-java/) in late 2021 exemplifies the danger of vulnerabilities in the supply chain, including the open source software supply chain. In this case, log4j, a popular open source Java logging library, had a severe and relatively easily exploitable security bug. Many of the companies and individuals using software built with log4j found themselves vulnerable because of this bug.

Malicious attacks, or what often amounts to code tampering, deserve special recognition though. In these attacks, an attacker controls the functionality inserted into the software supply chain and can often target attacks on specific victims. These attacks often prey on the lack of integrity in the software supply chain, taking advantage of the trust that software developers place in the components and tools they use to build software. Notable attack vectors include compromises of source code systems, such as GitHub or GitLab; build systems, like Jenkins or Tekton; and publishing infrastructure attacks on either corporate software publishing servers, update servers, or on community package infrastructure. Another important attack vector is when an attacker steals the credentials of an individual open source software developer and adds malicious code to a source code management system or published package.

Sigstore aims to help restore this missing integrity, ensuring that software developers and downstream consumers can verify and trust the software on which they depend.

### Key Software Supply Chain Security Concepts and Terms

There are a number of concepts and terms that software professionals interested in software supply chain security use frequently. Not only are these terms generally useful, but these concepts are also relevant to Sigstore and the Sigstore project’s mission.

**SLSA Framework:**

The Supply chain Levels for Software Artifacts (SLSA, pronounced “salsa”) framework is an incremental series of measures that protect the integrity of a software project’s software supply chain. There are four SLSA levels (1-4) with higher levels representing more security. The incremental approach allows organizations to adopt SLSA in a piecemeal fashion. The security measures associated with SLSA span the source code, build system, provenance, and any associated computer systems. The [SLSA framework](https://slsa.dev/) is itself an open source project.

**Software Integrity:**

Software artifacts that have integrity have not been modified in an unauthorized manner. For instance, an artifact that has been replaced by an attacker or an artifact that has had bit flips due to hard drive corruption would _not_ have integrity.

**Code Signing:**

Code signing refers to the creation of a cryptographic digital signature that ties an identity (often a company or a person) to an artifact. This signature proves to the consumer that the software has not been tampered with and that the specified party approves the artifact. Signing an artifact typically requires generating a keypair of public and private keys. The signer uses the private key to digitally sign the artifact and the consumer uses the public key to verify that the private key was used to sign the artifact.

**Attestations:**

An attestation is signed metadata about one or more software artifacts. Metadata can refer, for instance, to how an artifact was produced, including the build command and associated dependencies. In fact, there are many different types of possible metadata for a software artifact. Crucially, an attestation must also include a signature by the party that created the attestation. The SLSA project contains more information on the [definition of a software attestation](https://github.com/slsa-framework/slsa/blob/main/controls/attestations.md).

**SBOMs:**

SBOM (pronounced “_S-bomb_”) refers to a software bill of materials, or a list of ingredients that make up software components. SBOMs are widely viewed as one helpful building block for software security and software supply chain security risk management. You can find more information on SBOMs via a [Linux Foundation SBOM repor](https://www.linuxfoundation.org/tools/the-state-of-software-bill-of-materials-sbom-and-cybersecurity-readiness/)[t](https://www.linuxfoundation.org/tools/the-state-of-software-bill-of-materials-sbom-and-cybersecurity-readiness/). There is also a [Linux Foundation training course on “Generating a Software Bill of Materials](https://training.linuxfoundation.org/training/generating-a-software-bill-of-materials-sbom-lfc192/).

**Provenance:**

In the context of software security, provenance refers to information about who produced one or more software artifacts, and what steps and materials were used to produce those artifacts. This information helps software consumers make informed decisions about what software to consume and trust. You can find a specific [technical definition of provenance](https://slsa.dev/provenance/v0.1) via the SLSA website.

\*Throughout this course, we’ll use these terms frequently, so you will become more familiar with their usage and applications.

### The Motivation and History of the Sigstore Project

Neither software supply chain security, software integrity, nor code signing are new topics. For instance, a 1984 Turing award lecture by Kenneth Thompson, entitled “[Reflections on Trusting Trust](https://www.cs.cmu.edu/~rdriley/487/papers/Thompson_1984_ReflectionsonTrustingTrust.pdf),” arguably defined the modern debate over software supply chain security.

But despite a decades-long interest in software integrity, the practice of signing and verifying software artifacts remains relatively rare. In open source package managers that support package signatures, relatively few maintainers use existing methods to sign released artifacts. One [2016 study of the Python Package Index ecosystem](https://www.usenix.org/system/files/conference/nsdi16/nsdi16-paper-kuppusamy.pdf) found that a mere four percent of projects had signatures and that less than one-tenth of one percent of users downloaded these signatures for verification. The story is similar in many other software packaging ecosystems.

Moreover, traditional methods for signing software packages suffer from at least two defects. First, the software consumer must know what public key to use to verify the artifact. Traditional methods make finding this information cumbersome. Second, a single signature conveys relatively little information: that some party created that artifact. But it would be preferable to be able to convey more information, or metadata, about the software so that consumers can make more informed decisions about what software to use.

Sigstore aims to change this modern state of affairs. Several organizations including Google, Red Hat, Purdue University, and others began working together under the aegis of the Open Source Security Foundation (OpenSSF) in late 2020 and early 2021 to build the Sigstore project. Sigstore aims to make code signing and verification simple, widespread, and part of the invisible digital infrastructure that most computer users have become accustomed to when they, for instance, surf the web and benefit from widespread web traffic encryption.

To effect this change, Sigstore implements an architecture with multiple components that together enable a streamlined signing and verification process for software developers and consumers. Moreover, Sigstore uses additional technologies beyond a signing tool to bind identities such as emails to public keys and a transparency log to store software artifact metadata. These technologies will be explained in the next section.

### The Sigstore Architecture: Cosign, Fulcio, and Rekor

Sigstore’s GitHub repository contains a number of projects, although three are arguably central to the overall project and enable the technical vision described previously.

* [**Cosign**](https://github.com/sigstore/cosign) creates a key pair with public and private keys and then uses the private key to create a digital signature of software artifacts, that is, any item produced during the software development lifecycle, such as containers or open source software packages. This is the first step in creating a system that supports end-to-end integrity of a software artifact: the software developer must attach a signature to the created artifact. And, unlike previous approaches, Cosign (in combination with Fulcio, described next) reduces the burden on software developers by allowing them to use their identity associated with popular internet platforms (like GitHub) and therefore avoid storing private keys, which is both a hassle and a security risk.
* [**Fulcio**](https://docs.sigstore.dev/fulcio/overview/) is a certificate authority that binds public keys to email addresses (such as a Google account) using OpenID Connect. Fulcio serves as a trusted third party, helping parties that need to attest and verify identities. By connecting an identity to a verified email or other unique identifier, developers can attest that they truly did create their signed artifacts and later software consumers can verify that the software artifacts they use really did come from the expected software developers.
* [**Rekor**](https://docs.sigstore.dev/rekor/overview/) stores records of artifact metadata, providing transparency for signatures and therefore helping the open source software community monitor and detect any tampering of the software supply chain. On a technical level, it is an append-only (sometimes called “immutable”) data log that stores signed metadata about a software artifact, allowing software consumers to verify that a software artifact is what it claims to be.

The image below provides a diagram describing the system architecture of Sigstore.

<figure><img src="../../.gitbook/assets/image (15).png" alt=""><figcaption><p><strong>System Architecture of Sigstore</strong><br>Source: <a href="https://www.sigstore.dev/img/system_architecture_summary-01.svg">https://www.sigstore.dev/img/system_architecture_summary-01.svg </a></p></figcaption></figure>

Together, these components provide a system that makes widespread signing and verification of software artifacts possible. Software developers can more easily sign what they create, and software consumers can ensure that their software possesses integrity and was not compromised by tampering.

#### Sources:&#x20;

1. [For Good Measure - Counting Broken Links: a Quant's View of Software Supply Chain Security](https://www.usenix.org/system/files/login/articles/login_winter20_17_geer.pdf)
2. [Novel Malware XcodeGhost Modifies Xcode, Infects Apple iOS Apps and Hits App Store](https://unit42.paloaltonetworks.com/novel-malware-xcodeghost-modifies-xcode-infects-apple-ios-apps-and-hits-app-store/)
3. [Cybersecurity: Federal Response to SolarWinds and Microsoft Exchange Incidents\
   ](https://www.gao.gov/products/gao-22-104746)
