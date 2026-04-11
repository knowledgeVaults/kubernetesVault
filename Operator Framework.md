
The **[[Operator Framework]]** is a set of tools and patterns for building, packaging, and running Kubernetes Operators (Controllers that automate complex stateful application management)

* Operators encode operational knowledge (install, upgrade, backup, failover) as code instead of runbooks
* Built on the Kubernetes controller pattern: watch for Custom Resources, reconcile toward desired state
* The Operator Framework includes the **Operator SDK**, **OLM (Operator Lifecycle Manager)**, and **OperatorHub.io**

## What Is an Operator?

An Operator is a Kubernetes controller that manages a custom resource representing a complex application

```
CRD (desired state spec) + Controller (reconciliation logic) = Operator
```

Example: A PostgreSQL Operator watches `PostgresCluster` custom resources and handles provisioning, replication, backups, and failover automatically.

## Operator SDK

The Operator SDK provides scaffolding and libraries for building operators

```Bash
# Go-based operator
operator-sdk init --domain example.com --repo github.com/my-org/my-operator
operator-sdk create api --group database --version v1 --kind PostgresCluster

# Helm chart-based operator (wrap a Helm chart as an operator)
operator-sdk init --plugins helm

# Ansible-based operator
operator-sdk init --plugins ansible
```

## Operator Lifecycle Manager (OLM)

OLM manages the lifecycle of operators on a cluster (installation, updates, and dependency resolution)

```Bash
# Install OLM
operator-sdk olm install

# List available operators
kubectl get packagemanifests -n olm

# Install an operator
kubectl apply -f operator-subscription.yaml
```

## OperatorHub.io

OperatorHub.io is the community hub for discovering and distributing Kubernetes operators

* Hosts operators across categories: databases, monitoring, networking, storage, security
* Operators are vetted and categorised by maturity level (Basic, Seamless Upgrades, Full Lifecycle, etc.)
* Integrated directly with OLM for easy installation via `Subscription` objects

## Maturity Levels

| Level | Capabilities |
|---|---|
| Basic Install | Automated app provisioning |
| Seamless Upgrades | Patch and minor version upgrades |
| Full Lifecycle | App lifecycle, storage lifecycle, backup/recovery |
| Deep Insights | Metrics, alerts, log processing |
| Auto Pilot | Auto-scaling, auto-config, auto-tuning |
