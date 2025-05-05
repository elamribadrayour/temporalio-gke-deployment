# Temporal Deployment on GKE

This directory contains the configuration for deploying [Temporal](https://temporal.io/) to a Google Kubernetes Engine (GKE) cluster using the official [Temporal Helm chart](https://github.com/temporalio/helm-charts).

## Overview

- **Chart Source:** [temporalio/helm-charts](https://github.com/temporalio/helm-charts)
- **Deployment Target:** GKE (Google Kubernetes Engine)
- **Monitoring:** Grafana is **not** deployed by default in this configuration.
- **Configuration Files:**
  - `values.temporal.yaml`: Main Temporal configuration
  - `values.postgresql.yaml`: PostgreSQL database configuration
  - `values.github.yaml`: Example configuration file showing all available options in the Temporal Helm chart

## Prerequisites

- Access to a GKE cluster (`kubectl` configured)
- [Helm 3+](https://helm.sh/docs/intro/install/) installed
- [Google Cloud SDK](https://cloud.google.com/sdk/docs/install) installed

## Installation

1. Set up your GKE cluster context:

```sh
# List available GKE clusters
gcloud container clusters list

# Get credentials for your cluster
gcloud container clusters get-credentials CLUSTER_NAME --zone ZONE --project PROJECT_ID

# Verify the context is set correctly
kubectl config current-context
```

2. Add the required Helm repositories:

```sh
# Add Temporal Helm repository
helm repo add temporal https://go.temporal.io/helm-charts
helm repo update

# Add PostgreSQL Helm repository
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
```

3. Install PostgreSQL for Temporal:

```sh
helm install temporal-postgresql bitnami/postgresql \
  -f values.postgresql.yaml \
  -n temporal \
  --create-namespace
```

4. Install Temporal:

```sh
helm upgrade --install temporal temporal/temporal \
  --namespace temporal \
  --create-namespace \
  -f values.temporal.yaml
```

## Configuration

The deployment uses the following configuration files:

- `values.temporal.yaml`: Main configuration file containing:
  - Persistence configuration
  - SQL database settings
  - Namespace configuration
- `values.postgresql.yaml`: PostgreSQL database settings
- `values.github.yaml`: Example configuration file showing all available options in the Temporal Helm chart. Use this as a reference for available configuration options.

## Accessing the Temporal Web UI

To access the Temporal Web UI locally:

```sh
kubectl port-forward svc/temporal-web 8080:8080 -n temporal
```

Then open [http://localhost:8080](http://localhost:8080) in your browser.

## Verification

After deployment, verify the service is running:

```sh
# Check pods
kubectl get pods -n temporal

# Check services
kubectl get svc -n temporal

# Check logs
kubectl logs -n temporal -l app.kubernetes.io/name=temporal
```

## Uninstall

To remove the deployment:

```sh
# Remove Temporal
helm uninstall temporal -n temporal

# Remove PostgreSQL
helm uninstall temporal-postgresql -n temporal
```

---

*For more configuration options, see the [official Temporal Helm chart documentation](https://github.com/temporalio/helm-charts/blob/main/README.md).* 