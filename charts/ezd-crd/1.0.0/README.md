<!--- app-name: ezd-crd -->
# CRDs for EZD backend Helm Chart

Helm chart necessary to install EZD backend chart


## TL;DR

```console
helm repo add lp-ezd https://linuxpolska.github.io/ezd-rp
helm upgrade --install --create-namespace ezd-crd -n default lp-ezd/ezd-crd
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
helm upgrade --install --create-namespace ezd-crd -n default lp-ezd/ezd-crd
```

The command deploys operators on the Kubernetes cluster in the default configuration. The [Parameters](#parameters) section lists the parameters that can be configured during installation.

> **Tip**: List all releases using `helm list`

## Uninstalling the Chart

To uninstall/delete the `my-release` deployment:

```console
helm uninstall ezd-crd
```

The command removes all the Kubernetes components but no CRDs

To delete the CRDs  associated with `my-release`:

```console

kubectl get crd -o name | grep -E "(postgresql.cnpg.io|rabbitmqclusters.rabbitmq.com)" | xargs kubectl delete 

```

> **Note**: Deleting the CRDs will delete all data as well. Please be cautious before doing it.

## Parameters

### Global parameters

