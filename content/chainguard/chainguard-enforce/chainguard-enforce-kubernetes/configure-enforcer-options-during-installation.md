---
title: "Configuring Enforcer Options"
type: "article"
lead: ""
description: "Installing Chainguard with chainctl configuring your Enforcer Options"
draft: false
tags: ["Enforce", "Product", "Procedural"]
images: []
menu:
  docs:
    parent: "chainguard-enforce-kubernetes"
weight: 70
toc: true
---

> _This document relates to Chainguard Enforce. In order to follow along, you will need access to Chainguard Enforce. You can request access through selecting **Chainguard Enforce for Kubernetes** on the [inquiry form](https://www.chainguard.dev/get-demo?utm_source=docs)._

There is currently a limited list of enforcer options that can be configured when installing Chainguard into a current Kubernetes cluster. This guide will walk you through each of these Enforcer configuration settings. 

Our [getting started guide](/chainguard/chainguard-enforce/chainguard-enforce-kubernetes/chainguard-enforce-user-onboarding/) provides more detailed information on how to set up Chainguard Enforce, and this document provides a reference on how to configure different behaviors for your cluster. 

We will use `chainctl`, the command line tool for working with Chainguard products, which you can install using our [installation guide](/chainguard/chainguard-enforce/how-to-install-chainctl/).

## Enforcer Options

The list of available Enforcer options are detailed below:

* `webhook_fail_open`: is a flag to enable/disable a fail open behavior for the Enforcer webhooks. Default is set to `false`.
* `enable_cip_cache`: is a flag to enable/disable cluster image policy (CIP) caching. Default is set to `false`.

## Install with `chainctl`

To install with `chainctl`, first authenticate into `chainctl` before running a command.

```sh
chainctl auth login
```

With your cluster already set up, you'll install the Chainguard Enforce Agent with `chainctl` and use the flag `--opt` to set any of your Enforcer specific settings. 

For this example on GKE, EKS, or AKS cloud infrastructure, we enable our cluster webhook to fail open by using the next command. 

```sh
chainctl cluster install --group=$GROUP_ID --context $CLUSTER --opt=webhook_fail_open=true
```

In this next example using a _private_ or on-prem cluster, we configure our Chainguard cluster to enable the cluster image policy (CIP) cache and a failing open behavior for the Enforce webhooks.

```sh
chainctl cluster install --group=$GROUP_ID --private --context $CLUSTER --opt=webhook_fail_open=true --opt=enable_cip_cache=true
```

Be sure to replace the `$GROUP_ID` and `$CLUSTER` variables with the appropriate [IAM Group ID](/chainguard/chainguard-enforce/chainguard-enforce-kubernetes/how-to-manage-iam-groups-in-chainguard-enforce/), and name of your Kubernetes cluster, respectively. 

If you would like more detail about installing the Chainguard Enforce Agent with `chainctl`, or on getting onboarded to Chainguard Enforce, check out our [Getting Started guide](/chainguard/chainguard-enforce/chainguard-enforce-kubernetes/chainguard-enforce-user-onboarding/).

## Next Steps

With Chainguard installed in your cluster, continue learning about Enforce by reading the [Getting Started Guide](/chainguard/chainguard-enforce/chainguard-enforce-kubernetes/chainguard-enforce-user-onboarding/), learn how to [manage policies with `chainctl`](/chainguard/chainguard-enforce/chainguard-enforce-kubernetes/chainguard-policies-cli/), or follow the tutorial on [how to detect the Log4Shell vulnerability with Enforce](/chainguard/chainguard-enforce/chainguard-enforce-kubernetes/detect-log4shell-demo/).