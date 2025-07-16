# EKS EBS Optimizer

> 🔎 This is part of the [FineOps](../README.md) open toolkit for DevOps engineers optimizing cloud infrastructure.

Tuning **Kubernetes cost optimization** for EKS by selecting the right EBS volume types.

## EKS Cost Tuning Steps
1. Audited existing PersistentVolume usage
2. Switched heavy workloads to gp3
3. Enabled volume resizing via Terraform modules

## Impact
- 25% lower storage spend
- No impact on application I/O performance
