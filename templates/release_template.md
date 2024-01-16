# Release Note

**Version 1.2.1 released!**


This is initial and first stable version of LP-EZD package developed by Linux Polska which is allowing to install application developed by NASK on Kubernetes using Helm.

## Components version:
1. Backend-CRD
* redis_operator 0.15.0-golang-1.17-r1
* cluster_operator 2.6.0-golang-1.20-r1
* cloudnative-pg 1.22.0-debian-11-r1
2. Backend-DB
* mongodb 5.0.23-debian-11-r1
* redis 7.0.12-alpine-3.15-r1
* rabbitmq 3.12.12-management-ubuntu-22.04-r1
* postgresql 15.5-postgres-15.5-bullseye-r1

## Installation

> **Please ensure your Kubernetes cluster is at least v1.19 and helm 3.2.0+ before installing edzrp linuxpolska solution.**

For the installation instructions follow [installation](https://github.com/linuxpolska/ezd-rp/blob/main/INSTALLATION.md)


## Upgrade

There is no avaible upgrades yet.


## Bugs fixed

n/a

## Improvments

n/a

## Contributors
@Qwiatu-LinuxPolska
