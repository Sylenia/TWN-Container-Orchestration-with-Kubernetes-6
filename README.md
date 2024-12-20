# Helm Chart for Microservices - Demo Project

This project demonstrates how to use a shared Helm chart to deploy multiple microservices in a Kubernetes environment. The approach emphasizes reusable configurations for Deployments and Services, streamlining the deployment process while maintaining flexibility.

## Table of Contents
1. [Project Overview](#project-overview)
2. [Folder Structure](#folder-structure)
3. [Key Components](#key-components)
4. [Setup and Installation](#setup-and-installation)
5. [Helm Commands](#helm-commands)
6. [Best Practices](#best-practices)
7. [Use Cases](#use-cases)

---

## Project Overview

The main objective of this project is to create a shared Helm chart that can be reused across various microservices. By utilizing Helm, the project achieves:
- **Consistency**: Reuse common configurations for Deployments and Services.
- **Scalability**: Simplify adding or modifying microservices.
- **Maintainability**: Centralize values and templates for easier updates.

---

## Folder Structure

```plaintext
TWN-Kubernetes-v6/
├── charts/               # Helm chart directories
│   ├── microservice/     # Chart for generic microservices
│   └── redis/            # Chart for Redis deployment
├── values/               # Values files for individual services
├── config.yaml           # Example Kubernetes configuration
├── helmfile.yaml         # Declarative Helm release management
├── install.sh            # Script to deploy services
├── uninstall.sh          # Script to remove services
```

---

## Key Components

### 1. Helm Charts

#### `microservice`
- **Purpose**: A reusable chart for generic microservices.
- **Key Templates**:
  - `templates/deployment.yaml`: Defines Deployment resources.
  - `templates/service.yaml`: Defines Service resources.
- **Dynamic Parameters**:
  - Image, replica counts, ports, and environment variables are customizable via values files.

#### `redis`
- **Purpose**: A chart tailored for Redis.
- **Features**:
  - Configures Redis deployment and service.
  - Includes liveness probes for health monitoring.

### 2. Values Files
- Located in `values/`.
- Examples include:
  - `email-service-values.yaml`
  - `redis-values.yaml`
- Customize:
  - `appName`, `appImage`, `appVersion`, replicas, ports, and environment variables.

### 3. Shell Scripts

#### `install.sh`
- Automates Helm installations for all services.
- Example:
  ```bash
  helm install -f values/email-service-values.yaml emailservice charts/microservice
  ```

#### `uninstall.sh`
- Automates removal of deployed services.
- Example:
  ```bash
  helm uninstall emailservice
  ```

---

## Setup and Installation

### Prerequisites
1. **Kubernetes Cluster**: Ensure a running Kubernetes cluster.
2. **Helm**: Install Helm ([Installation Guide](https://helm.sh/docs/intro/install/)).
3. **kubectl**: Install kubectl ([Guide](https://kubernetes.io/docs/tasks/tools/)).

### Installation Steps
1. Clone the repository:
   ```bash
   git clone <repository-url>
   cd TWN-Kubernetes-v6
   ```
2. Install services using `install.sh`:
   ```bash
   ./install.sh
   ```
3. Verify deployments:
   ```bash
   kubectl get pods
   ```

### Uninstallation
- Run `uninstall.sh`:
  ```bash
  ./uninstall.sh
  ```

---

## Helm Commands

### Basic Commands
- Install a service:
  ```bash
  helm install -f <values-file> <release-name> <chart-path>
  ```
- Upgrade a service:
  ```bash
  helm upgrade -f <values-file> <release-name> <chart-path>
  ```
- Uninstall a service:
  ```bash
  helm uninstall <release-name>
  ```

### Helmfile Commands
- Apply all releases:
  ```bash
  helmfile apply
  ```
- Destroy all releases:
  ```bash
  helmfile destroy
  ```

---

## Best Practices

1. **Centralize Values**: Keep environment-specific configurations in `values/`.
2. **Template Reuse**: Use variables (e.g., `.Values`) for dynamic configurations.
3. **Version Control**: Track Helm charts and values in a Git repository.
4. **Validation**:
   - Use `helm lint` to validate charts.
   - Test templates with `helm template`.
5. **CI/CD Integration**: Automate Helm deployments in CI/CD pipelines.

---

## Use Cases

### 1. Scaling Microservices
- Easily increase replicas using `appReplicas` in values files.
- Example:
  ```yaml
  appReplicas: 3
  ```

### 2. Environment-Specific Deployments
- Use separate values files for `staging` and `production` environments.

### 3. Centralized Redis Configuration
- Deploy Redis with a dedicated chart, ensuring consistent configuration across services.

---

This is just a demo guide for deploying and managing microservices using Helm and Kubernetes. For additional details, check out the official [Helm Documentation](https://helm.sh/docs/) and [Kubernetes Documentation](https://kubernetes.io/docs/).


# Deploy Microservices with Helmfile

This section covers deploying microservices using Helmfile, a tool that simplifies the management of multiple Helm releases by describing them declaratively in a single YAML file. By combining Kubernetes, Helm, and Helmfile, you can manage complex deployments efficiently.

---

## What is Helmfile?

Helmfile is a declarative spec for managing Helm charts. It allows you to:
- **Organize Helm Releases**: Manage multiple releases with a single file.
- **Promote Consistency**: Use shared configurations across environments.
- **Simplify Deployments**: Apply or destroy all releases with one command.

---

## Project Structure for Helmfile

```plaintext
TWN-Kubernetes-v6/
├── charts/               # Helm chart directories
├── values/               # Values files for individual services
├── helmfile.yaml         # Declarative Helmfile
├── install.sh            # Script to deploy services (with Helmfile)
├── uninstall.sh          # Script to remove services (with Helmfile)
```

---

## Helmfile Configuration

### `helmfile.yaml`
A sample `helmfile.yaml` defines multiple microservices and their configurations:

```yaml
releases:
  - name: rediscart
    chart: charts/redis
    values:
      - values/redis-values.yaml

  - name: emailservice
    chart: charts/microservice
    values:
      - values/email-service-values.yaml

  - name: cartservice
    chart: charts/microservice
    values:
      - values/cart-service-values.yaml

  - name: currencyservice
    chart: charts/microservice
    values:
      - values/currency-service-values.yaml
```

- **Releases**: Defines each service's name, chart, and values.
- **Values**: Specifies the values file for each service.
- **Chart Path**: Points to the Helm chart directory.

---

## Deploying Microservices with Helmfile

### Prerequisites
1. Ensure Kubernetes and Helm are installed.
2. Install Helmfile ([Guide](https://github.com/roboll/helmfile#installation)).

### Deployment Steps

1. Navigate to the project directory:
   ```bash
   cd TWN-Kubernetes-v6
   ```

2. Apply all Helm releases:
   ```bash
   helmfile apply
   ```
   - Deploys all services defined in `helmfile.yaml`.
   - Helmfile resolves and applies values for each release.

3. Verify the deployments:
   ```bash
   kubectl get pods
   ```

### Uninstalling Microservices
To remove all services:

```bash
helmfile destroy
```
- Uninstalls all Helm releases specified in `helmfile.yaml`.

---

## Benefits of Using Helmfile

1. **Simplified Management**:
   - Manage multiple Helm releases in a single YAML file.

2. **Consistency Across Environments**:
   - Use environment-specific overrides to deploy to `staging` or `production` seamlessly.

3. **Automation Friendly**:
   - Integrate with CI/CD pipelines for automated deployments.

4. **Centralized Configuration**:
   - Keep all release definitions in one file for better organization.

---

## Example Commands

### Lint the Helmfile
Validate the `helmfile.yaml` configuration:
```bash
helmfile lint
```

### Render Templates
Preview rendered Kubernetes manifests:
```bash
helmfile template
```

### Apply Changes
Deploy or update all services:
```bash
helmfile apply
```

### Destroy Releases
Remove all services:
```bash
helmfile destroy
```

---

This section gives you a structured approach to demo-deploying microservices using a Helmfile. It enhances scalability and maintainability, making it awesome for managing complex Kubernetes projects. 🚀🚀🚀


# Helm and Kubernetes Commands for DevOps Projects

This section aims to give you a fairly comprehensive guide to the Helm and Kubernetes commands I used for this demo project. These commands are "essential" for managing and deploying applications in Kubernetes clusters, and they serve as a valuable reference for DevOps practitioners.

---

## Kubernetes Commands

### Cluster Management
- **Check cluster information**:
  ```bash
  kubectl cluster-info
  ```
- **Get node details**:
  ```bash
  kubectl get nodes
  ```
- **Check cluster namespaces**:
  ```bash
  kubectl get namespaces
  ```

### Resource Management
- **List all resources in the default namespace**:
  ```bash
  kubectl get all
  ```
- **Get details of a specific resource (e.g., pod)**:
  ```bash
  kubectl describe pod <pod-name>
  ```
- **Apply a manifest file**:
  ```bash
  kubectl apply -f <file-name>.yaml
  ```
- **Delete a resource**:
  ```bash
  kubectl delete -f <file-name>.yaml
  ```

### Pods and Deployments
- **Check all pods in a namespace**:
  ```bash
  kubectl get pods -n <namespace>
  ```
- **View pod logs**:
  ```bash
  kubectl logs <pod-name>
  ```
- **Access a pod's shell**:
  ```bash
  kubectl exec -it <pod-name> -- /bin/bash
  ```
- **Scale a deployment**:
  ```bash
  kubectl scale deployment <deployment-name> --replicas=<number>
  ```
- **Update a deployment image**:
  ```bash
  kubectl set image deployment/<deployment-name> <container-name>=<new-image>
  ```

### Troubleshooting
- **Check pod events**:
  ```bash
  kubectl get events
  ```
- **Debug a resource**:
  ```bash
  kubectl describe <resource-type> <resource-name>
  ```
- **Dry-run for testing manifests**:
  ```bash
  kubectl apply -f <file-name>.yaml --dry-run=client
  ```

---

## Helm Commands

### Basic Commands
- **Install a chart**:
  ```bash
  helm install <release-name> <chart-name>
  ```
- **List installed releases**:
  ```bash
  helm list
  ```
- **Upgrade a release**:
  ```bash
  helm upgrade <release-name> <chart-name>
  ```
- **Uninstall a release**:
  ```bash
  helm uninstall <release-name>
  ```

### Chart Management
- **Search for charts**:
  ```bash
  helm search hub <keyword>
  helm search repo <keyword>
  ```
- **Create a new chart**:
  ```bash
  helm create <chart-name>
  ```
- **Package a chart**:
  ```bash
  helm package <chart-path>
  ```
- **Lint a chart**:
  ```bash
  helm lint <chart-path>
  ```

### Template and Debugging
- **Render templates locally**:
  ```bash
  helm template <release-name> <chart-name>
  ```
- **Validate templates**:
  ```bash
  helm lint
  ```
- **Dry-run for testing charts**:
  ```bash
  helm install <release-name> <chart-name> --dry-run
  ```

### Working with Repositories
- **Add a Helm repository**:
  ```bash
  helm repo add <repo-name> <repo-url>
  ```
- **Update Helm repositories**:
  ```bash
  helm repo update
  ```
- **List repositories**:
  ```bash
  helm repo list
  ```

---

## Advanced Commands for DevOps Projects

### Kubernetes
- **Port-forward a service**:
  ```bash
  kubectl port-forward svc/<service-name> <local-port>:<service-port>
  ```
- **Roll back a deployment**:
  ```bash
  kubectl rollout undo deployment/<deployment-name>
  ```
- **Apply multiple manifests**:
  ```bash
  kubectl apply -f <directory-name>
  ```
- **Force delete a pod**:
  ```bash
  kubectl delete pod <pod-name> --grace-period=0 --force
  ```

### Helm
- **Rollback a release**:
  ```bash
  helm rollback <release-name> <revision-number>
  ```
- **Show release history**:
  ```bash
  helm history <release-name>
  ```
- **Diff changes between revisions**:
  Requires the `helm-diff` plugin:
  ```bash
  helm diff upgrade <release-name> <chart-name>
  ```
- **Fetch chart values**:
  ```bash
  helm get values <release-name>
  ```

---

This command guide serves as a handy reference for managing Kubernetes clusters and deploying applications using Helm and Helmfile.
Happy Deploying!!!! 🚀🚀🚀