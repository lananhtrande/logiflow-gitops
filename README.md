# LogiFlow GitOps + EKS + KEDA + IRSA Lab README

## Overview

This lab demonstrates production-style cloud-native deployment using:

* **Amazon EKS** for Kubernetes orchestration
* **ArgoCD** for GitOps continuous delivery
* **KEDA** for event-driven autoscaling
* **AWS SQS** for queue-based workload scaling
* **AWS SNS** for notifications
* **IAM Roles for Service Accounts (IRSA)** for secure AWS access
* **Helm** for Kubernetes package management
* **GitHub** as source-of-truth for declarative infrastructure

---

# Core Skills Learned

## Kubernetes

* Namespace lifecycle management
* Force-removing stuck namespaces via finalizer cleanup
* Deployments, ConfigMaps, ServiceAccounts
* CRDs (Custom Resource Definitions)
* Troubleshooting Pending/Terminating pods
* Scaling workloads with KEDA
* Reading pod logs, events, and `kubectl describe`

## GitOps / ArgoCD

* Declarative application deployment from GitHub
* ArgoCD installation and CRD troubleshooting
* Syncing, health checks, self-healing
* Managing application drift
* Fixing permission and TLS connection issues

## AWS Cloud Infrastructure

* SQS queue creation and permissions
* SNS topic subscription
* IAM role creation and trust policies
* IAM policy attachment/detachment
* OIDC provider integration with EKS
* IRSA for Kubernetes workloads
* Debugging AWS credential resolution failures

## Helm

* Installing KEDA correctly
* Managing failed Helm releases
* Understanding ownership metadata conflicts
* Rebuilding clean deployments from scratch

---

# Major Technical Challenges Solved

## 1. Broken Namespace Cleanup

### Problem:

Namespaces (`keda`, `logiflow`) stuck in `Terminating`

### Solution:

* Export namespace JSON
* Remove `spec.finalizers`
* Use:

```bash
kubectl replace --raw "/api/v1/namespaces/<namespace>/finalize" -f file.json
```

### HR takeaway:

Demonstrates advanced Kubernetes cluster recovery beyond normal deployment.

---

## 2. ArgoCD CRD Annotation Size Errors

### Problem:

`metadata.annotations: Too long`

### Solution:

* Install CRDs separately
* Use `kubectl create` instead of repeated `apply`
* Remove stale CRDs before reinstalling

### HR takeaway:

Shows understanding of enterprise deployment hygiene.

---

## 3. KEDA SQS Trigger Failures

### Problem:

`GetQueueAttributes`, `AccessDenied`, `IMDS timeout`

### Root Causes:

* Incorrect IAM permissions
* Missing IRSA
* Wrong trust relationship
* Service accounts recreated without annotations

### Solution:

* Rebuild KEDA cleanly
* Install with Helm role annotations built-in
* Correct trust policy:

```json
system:serviceaccount:keda:*
system:serviceaccount:logiflow:*
```

* Verify OIDC provider
* Confirm pod AWS env injection

### HR takeaway:

Highlights real DevOps troubleshooting of cloud security and autoscaling.

---

## 4. IAM + IRSA Mastery

### Key Lesson:

Kubernetes service account annotations alone are insufficient unless:

* OIDC provider exists
* Trust policy is correct
* Pods are created with annotated SA
* Helm deployment includes annotations at install time

### Practical Outcome:

Secure, least-privilege AWS integration without static credentials.

### HR takeaway:

Strong cloud security and infrastructure engineering competency.

---

# Final Architecture

```txt
GitHub Repo (GitOps)
       ↓
    ArgoCD
       ↓
 Amazon EKS Cluster
       ↓
 Worker Deployment
       ↓
     KEDA
       ↓
 AWS SQS Queue Metrics
       ↓
Horizontal Scaling
       ↓
 SNS Notifications
```

---

# Professional Competencies Demonstrated

## Technical

* Kubernetes administration
* GitOps deployment pipelines
* Cloud IAM security
* Event-driven autoscaling
* Infrastructure as Code
* Helm lifecycle management
* AWS integration
* Production troubleshooting

## Operational

* Root cause analysis
* Disaster recovery
* System rebuild from scratch
* Deployment debugging
* Security-first architecture
* CI/CD resilience

---

# Resume / HR Value

This project demonstrates:

* Ability to design and troubleshoot distributed systems
* Strong DevOps / SRE fundamentals
* Practical AWS cloud engineering
* Secure Kubernetes operations
* GitOps best practices
* Advanced debugging under real infrastructure constraints

## Suggested Resume Bullet:

> Built and troubleshot an end-to-end cloud-native logistics workflow on Amazon EKS using ArgoCD, KEDA, SQS, SNS, Helm, and IRSA, implementing secure GitOps deployment and event-driven autoscaling with production-grade IAM integration.

---

# Biggest Lessons

## Never:

* Repeatedly patch broken installs
* Ignore CRD ownership conflicts
* Assume annotations alone fix IRSA
* Overlook trust relationships

## Always:

* Clean broken environments fully
* Rebuild from known-good state
* Validate IAM + OIDC + ServiceAccount chain
* Deploy declaratively
* Verify runtime credential injection

---

# Conclusion

This lab moved beyond basic deployment into real-world platform engineering:

* Cloud infrastructure
* Kubernetes administration
* Security architecture
* GitOps automation
* Incident recovery

It reflects skills relevant to:

* DevOps Engineer
* Site Reliability Engineer
* Cloud Engineer
* Platform Engineer
* Infrastructure Engineer

---

# End Result

A fully operational, scalable, GitOps-managed logistics platform with secure AWS integration and enterprise-grade troubleshooting experience.
