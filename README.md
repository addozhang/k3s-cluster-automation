# k3s-cluster-automation 🚀

This repository provides streamlined scripts to automate the deployment and cleanup of lightweight Kubernetes clusters using ​**K3s**, a CNCF-certified, minimal Kubernetes distribution ideal for edge computing, IoT, and development environments. The scripts leverage [`k3sup`](https://github.com/alexellis/k3sup) for rapid cluster setup and teardown across multiple nodes.

## Features ✨

- ​**Single-command cluster setup**: Deploy a K3s cluster with 1 master + N workers using SSH.
- ​**Customizable components**: Disable non-essential services (Traefik, metrics-server, etc.) for minimal deployments.
- ​**Multi-node support**: Configure via environment variables (`HOSTS`).
- ​**Version control**: Specify K3s versions (default: `v1.28.13+k3s1`).
- ​**Docker integration**: Optional Docker runtime instead of containerd.
- ​**Automatic cleanup**: Remove all cluster components with one command.

## Prerequisites ⚙️

1. ​**SSH Key Authentication**:

```bash
ssh-keygen -t rsa -b 4096
ssh-copy-id -i ~/.ssh/id_rsa.pub your_ssh_user@your-server-ip
```

2. ​**k3sup CLI**​ (Installation Guide)

```bash
curl -sLS https://get.k3sup.dev | sh
sudo install k3sup /usr/local/bin/
```

3. ​**Environment Setup**:

```bash
export HOSTS=(192.168.1.10 192.168.1.11 192.168.1.12)  # Master first, then agents
export USER=your_ssh_user
```

## Installation & Usage 🛠️

### 1. Deploy Cluster

```bash
./setupk3s \
  --user $USER \
  --key ~/.ssh/id_rsa \
  --k3s-version v1.28.13+k3s1 \
  --mini  # For minimal cluster (disables Traefik, metrics-server, etc)
```

**Key Parameters**:

- `--docker`: Use Docker runtime instead of containerd
- `--mini`: Disable Traefik, metrics-server, local-storage, and servicelb
- `--no-traefik`: Disable Traefik ingress controller only
- `--k3s-version`: Specify K3s version (supports `stable`, `latest` channels)

Cluster config will be saved to `~/.kube/config`.

### 2. Verify Cluster

```bash
kubectl get nodes -o wide
kubectl get pods -A
```

### 3. Destroy Cluster

```bash
./cleanupk3s
```

This executes `k3s-uninstall.sh` on master and `k3s-agent-uninstall.sh` on workers

## Design Rationale 💡

### Why K3s?

- ​**50% lighter**​ than standard K8s while maintaining full compatibility
- ​**Single-binary architecture**​ simplifies deployment
- ​**Optimized for edge/IoT**​ with ARM support and low resource requirements

### Script Advantages over Manual Setup

- ​**Consistent configurations**: Enforces standardized settings across nodes
- ​**Time savings**: Reduces multi-node setup from 15+ minutes to <1 minutes
- ​**Repeatable deployments**: Ideal for CI/CD pipelines and ephemeral test environments

## Troubleshooting 🔧

- ​**SSH Connection Issues**:
    Verify key permissions: `chmod 600 ~/.ssh/id_rsa`
- ​**Component Conflicts**:
    Use `--mini` when needing minimal overhead
- ​**Version Mismatches**:
    Ensure consistent K3s versions between master/agents
