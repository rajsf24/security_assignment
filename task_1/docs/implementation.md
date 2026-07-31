# Task 1 – Secure Kubernetes Deployment Implementation

## Overview

This document describes the security enhancements implemented for the Ledger API Kubernetes deployment. The objective was to secure the application using Kubernetes native security features and policy enforcement following production-oriented DevSecOps practices.

---

# Environment

| Component | Value |
|-----------|-------|
| Operating System | Amazon Linux |
| Container Runtime | Docker |
| Kubernetes | Kind |
| Package Manager | Helm |
| Secret Management | Bitnami Sealed Secrets |
| Policy Engine | Kyverno |
| Ingress Controller | NGINX Ingress |

---

# Repository Structure

```
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

# 1. RBAC

## Objective

Apply the Principle of Least Privilege by preventing workloads from using the default ServiceAccount.

## Implementation

- Created a dedicated ServiceAccount
- Created a Role
- Created a RoleBinding
- Attached the ServiceAccount to the Ledger API Deployment

## Verification

```bash
kubectl get sa -n payments
kubectl get role -n payments
kubectl get rolebinding -n payments
```

---

# 2. ConfigMap

## Objective

Externalize application configuration from the container image.

## Implementation

Created a ConfigMap containing:

- APP_ENV
- LOG_LEVEL
- PAYMENT_PROVIDER

The Deployment consumes the ConfigMap using `envFrom`.

## Verification

```bash
kubectl get configmap -n payments
```

---

# 3. Secret Management

## Objective

Protect sensitive application credentials.

## Implementation

- Created Kubernetes Secret
- Installed Bitnami Sealed Secrets Controller
- Generated SealedSecret
- Configured the application to consume secrets through environment variables

Sensitive values include:

- STRIPE_API_KEY
- DB_PASSWORD

---

# 4. Pod Security Hardening

## Objective

Reduce the attack surface of application containers.

## Security Controls

- runAsNonRoot
- runAsUser
- runAsGroup
- allowPrivilegeEscalation: false
- readOnlyRootFilesystem
- Drop ALL Linux Capabilities
- RuntimeDefault Seccomp Profile

## Security Benefit

These controls prevent privilege escalation and enforce least privilege inside containers.

---

# 5. Resource Management

## Objective

Prevent resource starvation and improve scheduling.

## Configuration

Requests

- CPU: 100m
- Memory: 128Mi

Limits

- CPU: 500m
- Memory: 256Mi

---

# 6. Health Probes

## Liveness Probe

Automatically restarts unhealthy containers.

Endpoint

```
/health
```

## Readiness Probe

Ensures Pods only receive traffic after becoming healthy.

---

# 7. Ingress

## Objective

Expose the application using Kubernetes Ingress.

## Implementation

- Installed NGINX Ingress Controller
- Created an Ingress Resource
- Configured routing to the Ledger API Service

## Verification

```bash
kubectl get ingress -n payments
```

---

# 8. Kyverno Policy Enforcement

## Objective

Enforce security policies at admission time.

## Policies Implemented

### Disallow latest image tag

Prevents deployment of mutable container images.

### Require runAsNonRoot

Rejects Pods that do not configure:

```
runAsNonRoot: true
```

## Verification

```bash
kubectl get clusterpolicy
```

---

# 9. Kustomize

## Objective

Provide a single deployment entry point.

Deployment command

```bash
kubectl apply -k task_1/
```

This automatically deploys:

- Namespace
- RBAC
- ConfigMap
- Sealed Secrets
- Hardened Deployment
- Service
- Ingress
- Kyverno Policies

---

# Validation Commands

```bash
kubectl get pods -n payments

kubectl get ingress -n payments

kubectl get clusterpolicy

kubectl get role,rolebinding,serviceaccount -n payments

kubectl get configmap -n payments
```

---

# Security Controls Summary

| Control | Status |
|----------|--------|
| Namespace Isolation | ✅ |
| Dedicated ServiceAccount | ✅ |
| RBAC | ✅ |
| ConfigMap | ✅ |
| Secret Management | ✅ |
| Sealed Secrets | ✅ |
| Security Context | ✅ |
| Resource Requests | ✅ |
| Resource Limits | ✅ |
| Liveness Probe | ✅ |
| Readiness Probe | ✅ |
| Ingress | ✅ |
| Kyverno | ✅ |
| Kustomize | ✅ |
