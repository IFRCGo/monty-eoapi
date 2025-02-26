# Monty eoAPI Super App Helm Chart

This Helm chart is used for deploying and maintaining the **Montandon eoAPI** application and its supporting services in Kubernetes environments using ArgoCD.

## Overview

The Montandon eoAPI is an Earth Observation API that provides access to geospatial data. This Helm chart deploys the eoAPI application along with its required infrastructure, including PostgreSQL clusters with PostGIS and pgSTAC for storing and querying geospatial data.

## Repository Structure

```console
├── README.md                       # This file
├── Chart.yaml                      # Main Chart descriptor
├── values.yaml                     # Default values for the chart
├── app-bootstraps/                 # ArgoCD application bootstrap manifests
│   └── montandon-eoapi-staging.yaml # Bootstrap manifest for staging environment
├── components/                     # Component-specific values templates
│   ├── eoapi-values.yaml           # Template values for eoAPI
│   └── eoapi-support-values.yaml   # Template values for eoAPI support services
├── environments/                   # Environment-specific values
│   ├── production/                 # Production environment configuration
│   │   └── values.yaml             # Production-specific values
│   ├── shared/                     # Shared configuration across environments
│   └── staging/                    # Staging environment configuration
│       └── values.yaml             # Staging-specific values
└── templates/                      # Helm chart templates
    ├── _helpers.tpl                # Helper functions for templates
    ├── docker-config-secret.yaml   # Template for Docker registry credentials
    ├── eoapi-app.yaml              # Template for eoAPI application
    ├── eoapi-support-app.yaml      # Template for eoAPI support services
    ├── project.yaml                # Template for ArgoCD project
    ├── secrets.yaml                # Template for application secrets
    └── serviceaccount.yaml         # Template for service account
```

## Prerequisites

- Kubernetes cluster
- ArgoCD installed and configured
- Helm 3.x
- Access to the GitHub repository

## Installation

### Bootstrap the Application

To deploy the application to your Kubernetes cluster using ArgoCD, you can use one of the following methods:

#### Method 1: Using the ArgoCD CLI

For production environment:

```console
argocd app create monty-eoapi \
    --dest-namespace argocd \
    --dest-server https://kubernetes.default.svc \
    --repo https://github.com/IFRCGo/monty-eoapi.git \
    --revision main \
    --path . \
    --values environments/production/values.yaml \
    --sync-policy automated
```

For staging environment:

```console
argocd app create monty-eoapi-staging \
    --dest-namespace argocd \
    --dest-server https://kubernetes.default.svc \
    --repo https://github.com/IFRCGo/monty-eoapi.git \
    --revision main \
    --path . \
    --values environments/staging/values.yaml \
    --sync-policy automated
```

#### Method 2: Using the Bootstrap Manifests

Apply one of the bootstrap manifests from the `app-bootstraps` directory:

```console
kubectl apply -f app-bootstraps/montandon-eoapi-staging.yaml
```

## Configuration

### Environment-specific Configuration

The chart supports different environments through the values files in the `environments` directory:

- `environments/production/values.yaml`: Production environment configuration
- `environments/staging/values.yaml`: Staging environment configuration

### Component Configuration

The main components of the application can be configured through the values files:

#### eoAPI Component

The eoAPI component is configured in `components/eoapi-values.yaml` and can be customized through the values in `values.yaml` or environment-specific values files:

```yaml
components:
  eoapi:
    enabled: true
    chart:
      repository: https://devseed.com/eoapi-k8s/
      chart: eoapi
      version: 0.5.0
    replicaCount: 3
    ingress:
      enabled: true
      hostname: monty-eoapi
      tls:
        enabled: true
        secretName: monty-eoapi-tls
    postgrescluster:
      bouncer-replicas: 1
      data-node:
        replicas: 1
        resources:
          requests:
            storage: 100Gi
            cpu: 1024m
            memory: 3048Mi
```

#### eoAPI Support Component

The eoAPI support component is configured in `components/eoapi-support-values.yaml` and can be enabled/disabled through the values:

```yaml
components:
  eoapi-support:
    enabled: true  # Set to false to disable
```

## Usage

Once deployed, the eoAPI will be accessible through the configured ingress hostname. For example:

- Production: https://montandon-eoapi.ifrc.org
- Staging: https://montandon-eoapi-stage.ifrc.org

## Contributing

Contributions to this Helm chart are welcome. Please follow these steps:

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

## License

This project is licensed under the terms specified in the repository.

## Contact

For questions or support, please contact the IFRC Go team.
