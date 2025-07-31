# Apache Superset on Azure Kubernetes Service (AKS) using Helm

This project provides a working Helm-based deployment of **Apache Superset** on **Azure Kubernetes Service (AKS)**. It is created to help others set up Superset without facing the same issues I did.

---

## 🚀 Overview

This guide covers:
- Adding the Superset Helm chart
- Custom configuration with `my-values.yaml`
- Deploying Superset on AKS
- Common troubleshooting tips

---

## 📦 Prerequisites

- An Azure account with AKS configured
- `kubectl` and `helm` installed
- Access to PostgreSQL and Redis (either managed or in-cluster)

---

## 🔧 Step 1: Add Superset Helm Repository

```bash
helm repo add superset https://apache.github.io/superset
helm repo update
