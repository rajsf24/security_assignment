# Secure Kubernetes Deployment - Ledger API

## Project Overview

This repository contains my implementation of the Ledger API technical assignment with a focus on Kubernetes security, workload hardening, and policy enforcement.

The application is deployed on a local Kubernetes cluster created using Kind and secured following production-oriented Kubernetes security best practices.

---

# Architecture

Client
↓
NGINX Ingress Controller
↓
Ingress
↓
Service (ClusterIP)
↓
Ledger API Pods

Supporting Components

- Namespace Isolation
- RBAC
- Dedicated Service Account
- ConfigMap
- Sealed Secrets
- Security Context
- Resource Limits
- Health Probes
- Kyverno Policies

---

# Security Controls Implemented

## RBAC

- Dedicated ServiceAccount
- Least Privilege Role
- RoleBinding

## Secret Management

- Kubernetes Secret
- Bitnami Sealed Secrets

## Pod Security

- runAsNonRoot
- allowPrivilegeEscalation: false
- readOnlyRootFilesystem
- Drop ALL Linux Capabilities
- RuntimeDefault Seccomp Profile

## Resource Management

CPU Requests and Limits

Memory Requests and Limits

## Health Monitoring

Liveness Probe

Readiness Probe

## Networking

NGINX Ingress Controller

Ingress Resource

## Policy Enforcement

Kyverno

- Disallow latest image tags
- Require runAsNonRoot

---

# Repository Structure

```text
task_1/
├── base/
├── configmap/
├── docs/
├── ingress/
├── kustomization.yaml
├── kyverno/
├── rbac/
├── screenshots/
├── secrets/
└── security-context/
```

---

# Deployment

Create Kind Cluster

```bash
kind create cluster --config kind/cluster-config.yaml
```

Deploy Application

```bash
kubectl apply -k task_1/
```

Verify

```bash
kubectl get pods -n payments
kubectl get ingress -n payments
kubectl get clusterpolicy
```

---

# Skills Demonstrated

- Kubernetes
- Docker
- Kind
- RBAC
- Kubernetes Networking
- Ingress
- ConfigMaps
- Secrets
- Sealed Secrets
- Security Context
- Health Probes
- Resource Management
- Kyverno
- Kustomize

---

# Future Improvements

- GitHub Actions CI/CD
- Trivy Image Scanning
- Cosign Image Signing
- ArgoCD GitOps
- Prometheus Monitoring
- Grafana Dashboards