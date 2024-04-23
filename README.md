# Kubernetes-based Streaming Server

This repository contains Kubernetes manifests needed to deploy a streaming server configured with Nginx and the RTMP module for live streaming capabilities. This setup is designed to leverage the power of Kubernetes for scalability and reliability.

## Features

- **Nginx RTMP Streaming**: Configured to handle RTMP based streaming.
- **Adaptive Bitrate Streaming**: Supports HLS for adaptive bitrate streaming.
- **Kubernetes Deployment**: Leverages Kubernetes for managing scalable and resilient streaming services.

## Prerequisites

Before you begin, ensure you have the following prerequisites installed:
- **Docker**: For building the container images. [Install Docker](https://www.docker.com/products/docker-desktop).
- **Kubectl**: For interacting with your Kubernetes cluster. [Install kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/).
- **Minikube** or **a Kubernetes cluster**: For deploying your configurations. [Install Minikube](https://minikube.sigs.k8s.io/docs/start/).

## Getting Started

These instructions will get your streaming server running on your Kubernetes cluster.

### 1. Clone the Repository

Clone this repository to get the required Kubernetes configuration files:

```bash
git clone git@github.com:contourkde/repo-testing-devops.git
cd repo-testing-devops
```
