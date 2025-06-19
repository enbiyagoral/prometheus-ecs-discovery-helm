# Prometheus ECS Discovery Helm Chart

[![Artifact Hub](https://img.shields.io/endpoint?url=https://artifacthub.io/badge/repository/prometheus-ecs-discovery)](https://artifacthub.io/packages/helm/prometheus-ecs-discovery/prometheus-ecs-discovery)

A Helm chart for deploying Prometheus ECS Service Discovery to automatically discover and monitor AWS ECS services in Kubernetes.

## Features

- **🔍 ECS Service Discovery**: Automatically discover services running in AWS ECS clusters
- **📊 Prometheus Integration**: Seamless integration with Prometheus for monitoring
- **💾 EFS Storage**: Persistent storage using AWS EFS
- **🔐 IAM Integration**: Secure access using AWS IAM roles
- **⚙️ Configurable**: Full configuration support via values.yaml
- **🚀 Production Ready**: Optimized for production deployments

## Prerequisites

- Kubernetes 1.19+
- Helm 3.0+
- AWS EKS cluster
- EFS CSI Driver
- AWS IAM role with ECS permissions

## Quick Start

### 1. Add the Helm Repository

```bash
helm repo add prometheus-ecs-discovery https://enbiyagoral.github.io/prometheus-ecs-discovery
helm repo update
```

### 2. Install the Chart

```bash
helm install ecs-discovery prometheus-ecs-discovery/prometheus-ecs-discovery \
  --namespace monitoring \
  --create-namespace
```

### 3. Configure ECS Cluster

```bash
helm upgrade ecs-discovery prometheus-ecs-discovery/prometheus-ecs-discovery \
  --namespace monitoring \
  --set ecsDiscovery.cluster="your-ecs-cluster-name"
```

## Configuration

### Values.yaml Parameters

| Parameter | Description | Default |
|-----------|-------------|---------|
| `replicaCount` | Number of replicas | `1` |
| `image.repository` | Container image repository | `enbiyagoral/prometheus-ecs-discovery` |
| `image.tag` | Container image tag | `latest` |
| `ecsDiscovery.cluster` | ECS cluster name | `prometheus-discovery` |
| `ecsDiscovery.scrapeInterval` | Discovery interval | `1m` |
| `storage.enabled` | Enable persistent storage | `true` |
| `storage.size` | Storage size | `1Gi` |
| `storage.storageClass` | Storage class name | `efs-sc` |

### Advanced Configuration

```yaml
# ECS Discovery Configuration
ecsDiscovery:
  cluster: "my-ecs-cluster"
  scrapeInterval: "1m"
  writeTo: "/etc/prometheus/ecs_file_sd.yml"
  serverNameLabel: "PROMETHEUS_EXPORTER_SERVER_NAME"
  jobNameLabel: "PROMETHEUS_EXPORTER_JOB_NAME"
  pathLabel: "PROMETHEUS_EXPORTER_PATH"
  portLabel: "PROMETHEUS_EXPORTER_PORT"

# Storage Configuration
storage:
  enabled: true
  size: 1Gi
  storageClass: efs-sc
  accessModes:
    - ReadWriteMany

# Resource Configuration
resources:
  limits:
    cpu: 100m
    memory: 128Mi
  requests:
    cpu: 50m
    memory: 64Mi
```

## IAM Requirements

The application requires the following IAM permissions:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ecs:ListClusters",
        "ecs:ListServices",
        "ecs:DescribeServices",
        "ecs:ListTasks",
        "ecs:DescribeTasks"
      ],
      "Resource": "*"
    }
  ]
}
```

## Usage

### Check Status

```bash
# Check pod status
kubectl get pods -n monitoring -l app.kubernetes.io/name=ecs-discovery

# View logs
kubectl logs -n monitoring -l app.kubernetes.io/name=ecs-discovery

# Check service discovery file
kubectl exec -n monitoring deployment/ecs-discovery -- cat /etc/prometheus/ecs_file_sd.yml
```

### Upgrade

```bash
helm upgrade ecs-discovery prometheus-ecs-discovery/prometheus-ecs-discovery \
  --namespace monitoring \
  --set ecsDiscovery.cluster="new-cluster-name"
```

### Uninstall

```bash
helm uninstall ecs-discovery -n monitoring
```

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Support

- 📖 [Documentation](https://github.com/enbiyagoral/prometheus-ecs-discovery)
- 🐛 [Issues](https://github.com/enbiyagoral/prometheus-ecs-discovery/issues)
- 💬 [Discussions](https://github.com/enbiyagoral/prometheus-ecs-discovery/discussions) 