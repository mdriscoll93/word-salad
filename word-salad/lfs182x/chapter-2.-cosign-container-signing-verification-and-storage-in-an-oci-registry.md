---
icon: '2'
---

# Chapter 2. Cosign: Container Signing, Verification, and Storage in an OCI Registry

## Chapter Overview

This chapter will focus on **Cosign**, which supports software artifact signing, verification, and storage in an OCI (Open Container Initiative) registry. While Cosign was developed with containers and container-related artifacts in mind, it can also be used for open source software packages and other file types. Cosign can therefore be used to sign blobs (binary large objects), files like READMEs, SBOMs (software bill of materials), Kubernetes Helm Charts, Tekton bundles (an OCI artifact containing Tekton CI/CD resources like tasks), and more.

By signing software, you can authenticate that you are who you say you are, which can in turn enable a trust root so that developers who leverage your software and consumers who use your software can verify that you created the software artifact that you have said you’ve created. They can also ensure that that artifact was not tampered with by a third party. As someone who may use software libraries, containers, or other artifacts as part of your development lifecycle, a signed artifact can give you greater assurance that the code or container you are incorporating is from a trusted source.

### Learning Objectives

* Explain what Cosign is.
* Install Cosign.
* Sign several software artifacts.
* Verify that software artifacts have been signed.
* Have an understanding of the trust root around Sigstore.

### Code Signing with Cosign

Software artifacts are distributed widely, can be incorporated into the software of other individuals and organizations, and are often updated throughout their life spans. End users and developers who build upon existing software are increasingly aware of the possibility of threats and vulnerabilities in packages, containers, and other artifacts. How can users and developers decide whether to use software created by others? One answer that has been increasingly gaining traction is code signing.

While code signing is not new technology, the growing prevalence of software in our everyday lives coupled with a rising number of attacks like [SolarWinds](https://www.businessinsider.com/solarwinds-hack-explained-government-agencies-cyber-security-2020-12) and [Codecov](https://www.reuters.com/technology/codecov-hackers-breached-hundreds-restricted-customer-sites-sources-2021-04-19/) has created a more pressing need for solutions that build trust, prevent forgery and tampering, and ultimately lead to a more secure software supply chain. Similar in concept to a signature on a document that was signed in the presence of a notary or other professional who can certify your identity, a signature on a software artifact attests that you are who you say you are and that the code was not altered. Instead of a recognized notary when you sign software, it is a recognized **certificate authority** (CA) that validates your identity. These checks that go through recognized bodies able to establish a developer’s identity support the root of trust that security relies on so that bad actors cannot compromise software.

Code signing involves a developer, software publisher, or entity (like an automated workload) digitally signing a software artifact to confirm their identity and ensure that the artifact was not tampered with since having been signed. Code signing has several implementations, and Cosign is one such implementation, but all code signing technology follows a similar process as Cosign.

<figure><img src="../../.gitbook/assets/image (16).png" alt=""><figcaption><p><strong>Code Signing</strong></p></figcaption></figure>

A developer (or organization) looking to sign their code with Cosign will first generate a key pair with public and private keys, and will then use the private key to create a digital signature for a given software artifact. A **key pair** is a combination of a signing key to sign data, and a verification key that is used to verify data signed with the corresponding signing key. Public keys can be known to others (and can be openly distributed), and private keys must only be known by the owner for signatures to be secure. With the key pair, the developer will sign their software artifact and store that signature in the registry (if applicable). This signature can later be verified by others through searching for an artifact, finding its signature, and then verifying it against the public key.

### Cosign in Practice

We will go through the installation and use Cosign in the Lab section. To give you an understanding of Cosign commands, we’ll go over the basics here.

When you install Cosign in the Lab section, you will be able to generate a key pair in Cosign. We’ll review the commands so you can understand how Cosign works in practice, but at this point you are not expected to follow along, and we are using imagined demo containers:

```bash
$ cosign generate-key-pair
Enter password for private key:
Enter again:
Private key written to cosign.key
Public key written to cosign.pub
```

You can sign a container and store the signature in the registry with the **cosign sign** command.

```bash
$ cosign sign --key cosign.key sigstore-course/demo
Enter password for private key:
Pushing signature to:
index.docker.io/sigstore-course/demo:sha256-87ef60f558bad79beea6425a3b28989f01dd417164150ab3baab98dcbf04def8.sig
```

Finally, you can verify a software artifact against a public key with the **cosign verify** command. This command will return **0** if at least one Cosign formatted signature for the given artifact is found that matches the public key. Any valid formats are printed to standard output in a JSON format.

<pre class="language-bash"><code class="lang-bash"><strong>$ cosign verify --key cosign.pub sigstore-course/demo
</strong>
The following checks were performed on these signatures:
  - The cosign claims were validated
  - The signatures were verified against the specified public key
{"Critical":{"Identity":{"docker-reference":""},"Image":{"Docker-manifest-digest":"sha256:87ef60f558bad79beea6425a3b28989f01dd417164150ab3baab98dcbf04def8"},"Type":"cosign container image signature"},"Optional":null}
</code></pre>

You should now have some familiarity with the process of signing and verifying code in Cosign. In the lab portion of this chapter, we will go through installing Cosign and understanding its commands in greater detail with a full demonstration.

Code signing provides developers and others who release code a way to attest to their identity, and in turn, those who are consumers (whether end users or developers who incorporate existing code) can verify those signatures to ensure that the code is originating from where it is said to have originated, and check that that particular developer (or vendor) is trusted.

### Keyless Signing

Code signing is a solution for many use cases related to attestation and verification with the goal of a more secure software supply chain. While key pairs are a technology standard that have a long history in technology (SSH keys, for instance), they create their own challenges for developers and engineering teams. The contents of a public key are very opaque; humans cannot readily discern who the owner of a given key is. Traditional public key infrastructure, or **PKI**, has done the work to create, manage, and distribute public-key encryption and digital certificates. A new form of PKI is keyless signing, which prevents the challenges of long-lived and opaque signing keys.

In keyless signing, short-lived certificates are generated and linked into the chain of trust through completing an identity challenge that confirms the identity of the signer. Because these keys persist only long enough for signing to take place, signature verification ensures that the certificate was valid at the time of signing. Policy enforcement is supported through an encoding of the identity information onto the certificate, allowing others to verify the identity of the developer who signed.

Through offering short-lived credentials, keyless signing can support the recommended practice of operating your build environment like a production environment. This prevents the opportunity for long-lived keys to be stolen and used to sign malicious artifacts. Even if these short-lived keys used in keyless signing _were_ stolen, they’d be useless!

While keyless signing can be used by individuals in the same manner as long-lived key pairs, it is also well suited for continuous integration and continuous deployment workloads. Keyless signing works by sending an OpenID Connect (OIDC) token to a certificate authority like Fulcio to be signed by a workload’s authenticated OIDC identity. This allows the developer to cryptographically demonstrate that the software artifact was built by the continuous integration pipeline of a given repository, for example.

Cosign uses ephemeral keys and certificates, gets them signed automatically by the Fulcio root certificate authority, and stores these signatures in the Rekor transparency log, which automatically provides an attestation at the time of creation.

You can manually create a keyless signature with the following command in cosign. In our example, we’ll use Docker Hub. If you would like to follow along, ensure you are logged into [Docker Hub](https://hub.docker.com/) on your local machine and that you have a Docker repository with an image available. The following example assumes a username of **docker-username** and a repository name of **demo-container**.

```bash
$ COSIGN_EXPERIMENTAL=1 cosign sign docker-username/demo-container

Generating ephemeral keys...
Retrieving signed certificate...
Your browser will now be opened to:
```

At this point, a browser window will open and you will be directed to a page that asks you to log in with Sigstore. You can authenticate with GitHub, Google, or Microsoft. Note that the email address that is tied to these credentials will be permanently visible in the Rekor transparency log. This makes it publicly visible that you are the one who signed the given artifact, and helps others trust the given artifact. That said, it is worth keeping this in mind when choosing your authentication method. Once you log in and are authenticated, you’ll receive feedback of **“Sigstore Auth Successful”**, and you may now safely close the window.

On the terminal, you’ll receive output that you were successfully verified, and you’ll get confirmation that the signature was pushed.

```bash
Successfully verified SCT...
tlog entry created with index:
Pushing signature to: index.docker.io/docker-username/demo-container
…
```

If you followed along with Docker Hub, you can check the user interface of your repository and verify that you pushed a signature.

You can then further verify that the keyless signature was successful by using cosign verify to check.

```bash
$ COSIGN_EXPERIMENTAL=1 cosign verify docker-username/demo-container

The following checks were performed on all of these signatures:
  - The cosign claims were validated
  - The claims were present in the transparency log
  - The signatures were integrated into the transparency log when the certificate was valid
  - Any certificates were verified against the Fulcio roots.
…
{"Critical":{"Identity":{"docker-reference":""},"Image":{"Docker-manifest-digest":"sha256:97fc222cee7991b5b061d4d4afdb5f3428fcb0c9054e1690313786befa1e4e36"},"Type":"cosign container image signature"},"Optional":null}
```

As part of the JSON output, you should get feedback on the issuer that you used and the email address associated with it. For example, if you used Google as the authenticator, you will have&#x20;

```json
"Issuer":"https://accounts.google.com","Subject":"your-email@gmail.com"}}] as the last part of your output.

```

### Cosign Installation

There are a few different ways to install Cosign to your local machine or remote server. The approach you choose should be based on the way you set up packages, the tooling that you use, or the way that your organization recommends.

#### Installing Cosign with Homebrew or Linuxbrew

Those who are running macOS locally may be familiar with Homebrew as a package manager. There is also a [Linuxbrew](https://docs.brew.sh/Homebrew-on-Linux) version for those running a Linux distribution. If you are using macOS and would like to leverage a package manager, you can review the [official documentation to install Homebrew](https://brew.sh/) to your machine.

To install Cosign with Homebrew, run the following command.

`$ brew install cosign`

To update Cosign in the future, you can run `brew upgrade cosign` to get the newest version.

#### Installing Cosign with Linux Package Managers

Cosign is supported by the Arch Linux, Alpine Linux, and Nix package managers. In the [releases page](https://github.com/sigstore/cosign/releases), you'll also find .deb and .rpm packages for manual download and installation.

To install Cosign on Arch Linux, use the **pacman** package manager.

`$ pacman -S cosign`

If you are using Alpine Linux or an Alpine Linux image, you can add Cosign with **apk**.

`$ apk add cosign`

For NixOS, you can install Cosign with the following command:

`$ nix-env -iA nixpkgs.cosign`

And for NixOS Linux, you can install Cosign using **nixos.cosign** with the **nix-env** package manager.

`$ nix-env -iA nixos.cosign`

For Ubuntu and Debian distributions, check the releases page and download the latest .deb package. At the time of this writing, this would be version 1.8.0. To install the **.deb** file, run:

`$ sudo dpkg -i ~/Downloads/cosign_1.8.0_amd64.deb`

For CentOS and Fedora, download the latest **.rpm** package from the releases page and install Cosign with:

```bash
$ rpm -ivh cosign-1.8.0.x86_64.rpm
```

You can check to ensure that Cosign is successfully installed using the **cosign version** command following installation. When you run the command, you should receive output that indicates the version you have installed.

#### Installing Cosign with Go

You may choose to install Cosign with Go if you already are working in the programming language Go. Additionally, installing with Go will work across different distributions. First, check that you have Go installed on your machine, and ensure that it is Go version 1.16 or later.

```bash
~ $ go version
```

As long as your output indicates that you are at Go 1.16 or above, you’ll be ready to install Cosign with Go. Your output should appear similar to the following.

```go
~ $ go version go1.17.6 darwin/arm64
```

If you run into an error or don’t receive output like the above, you’ll need to install Go in order to install Cosign with Go. Navigate to the official [Go website](https://go.dev/doc/install) in order to download the appropriate version of Go for your machine.

With Go 1.16 or above installed, you are ready to install Cosign with Go, using the following command.

<pre class="language-go"><code class="lang-go"><a data-footnote-ref href="#user-content-fn-1">$ go install ~ $ github.com/sigstore/cosign/cmd/cosign@latest</a>
</code></pre>

The resulting binary from this installation will be placed at `$GOPATH/bin/cosign.`&#x20;

#### Installing Cosign with the Cosign Binary

Installing Cosign via its binary offers you greater control over your installation, but this method also requires you to manage your installation yourself. In order to install via binary, check for the most updated version in the open source GitHub repository for Cosign under the [releases page](https://github.com/sigstore/cosign/releases).

You can use the **wget** command to install the most recent binary. In our example, the release we are installing is 1.8.0.

```bash
$ wget
"https://github.com/sigstore/cosign/releases/download/v1.8.0/cosign-linux-amd64"
```

Next, move the Cosign binary to your **bin** folder.

```bash
$ sudo mv cosign-linux-amd64 /usr/local/bin/cosign
```

Finally, update permissions so that Cosign can execute within your filesystem.

```bash
$ sudo chmod +x /usr/local/bin/cosign
```

You’ll need to ensure that you keep Cosign up to date if you install via binary. You can always later opt to use a package manager to update Cosign in the future.

#### Signing a Container with Cosign

We briefly went over the commands that you would take to sign software artifacts in Cosign earlier in the chapter. Let’s step through signing a container with Cosign. We are using a container to provide a sense of how you may use Sigstore with containerized workloads, but the steps we are taking to sign a container are very similar to the steps that we would take to sign any other software artifact that can be published in a container registry, and we will discuss signing blobs a little later.

Before beginning this section, ensure that you have Docker installed and that you are running Docker Desktop if that is relevant for your operating system. For guidance on installing and using Docker, refer to the [official Docker documentation](https://docs.docker.com/get-docker/). In order to push to the Docker container registry, you will need a Docker Hub username. If you are familiar with using a different container registry, feel free to use that.

#### Generating a Cosign Key Pair

In order to generate a Cosign key pair, you’ll need to have Cosign installed, which you can do following the previous section.

If you have not already created a Cosign key pair, navigate to your user directory so we can create one.

```bash
$ cd ~
```

You’ll use Cosign to create the key pair now.

```bash
$ cosign generate-key-pair
```

Once you run this command, you’ll receive output asking you to create a password for the private key. It is recommended to have a password for an extra layer of security, but you can also leave this field blank, especially if you are just using this key pair for testing purposes. You’ll be prompted to enter this password twice.

```bash
Enter password for private key:
Enter again:
```

Once you have entered the private key password, you’ll receive feedback that the private key and public key were written.

```bash
Private key written to cosign.key
Public key written to cosign.pub
```

Your private key, stored in the **cosign.key** file, should not be shared with anyone. Your public key, stored in the **cosign.pub** file, will be used to identify that you are the key holder who is signing your software artifacts.

Now both of these files exist in your home user directory (don’t forget where they are!), and you can inspect them if you would like by using the **cat** command, as in:

**$ cat ​​cosign.pub**

You’ll get output that indicates the beginning and end of your public key, with a large string of mixed-case alphanumeric values in between.

**-----BEGIN PUBLIC KEY-----**\
&#xNAN;**…**\
&#xNAN;**-----END PUBLIC KEY-----**

With your keys set up, you are ready to move on to creating and signing a container.

#### Creating a Container

With you keys set up, you'll now be creating a new container. Create a new directory within your user directory that is the same as your docker username and, within that, a directory called \`hello-container\`. If you will be opting to use a registry other than docker, feel free to use the relevant username for that registry.



[^1]: 
