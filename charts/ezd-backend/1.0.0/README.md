<!--- app-name: ezd-backend -->
# LP backend for EZD RP 

Services necessary to run EZD RP app 

## TL;DR

```console
helm upgrade --install --create-namespace ezd-backend -n ezdrp linuxpolska/ezd-backend
```

## Introduction

This chart bootstraps a set of operatos and CRDs on a [Kubernetes](https://kubernetes.io) cluster using the [Helm](https://helm.sh) package manager.

Linux Polska charts can be served by [Rancher Apps & Marketplace](https://ranchermanager.docs.rancher.com/pages-for-subheaders/helm-charts-in-rancher) for deployment and management of Helm Charts in clusters.


## Prerequisites

- Kubernetes 1.19+
- Helm 3.2.0+
- PV provisioner support in the underlying infrastructure

## Installing the Chart

To install the chart with the release name `my-release`:

```console
helm repo add linuxpolska  https://qwiatu-linuxpolska.github.io/ezd/
helm repo update 

helm upgrade --install --create-namespace ezd-backend -n ezdrp linuxpolska/ezd-backend
```

The command deploys postgresql, rabbitmq, redis on the Kubernetes cluster in the default configuration. The [Parameters](#parameters) section lists the parameters that can be configured during installation.

> **Tip**: List all releases using `helm list`

## Uninstalling the Chart

To uninstall/delete the `ezd-backend` deployment:

```console
helm -n default uninstall ezd-backed
```


> **Note**: Deleting the helm chart will delete all data as well. Please be cautious before doing it.

> **Note**: Remove helm chart before remove CRDs for LP Backend.

## Parameters

### Global parameters

