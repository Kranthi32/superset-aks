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

```
## 🔧 Step 2: View charts in repo

```bash
helm search repo superset
```
# Output
NAME                    CHART VERSION   APP VERSION     DESCRIPTION
superset/superset       0.1.1           1.0             Apache Superset is a modern, enterprise-ready b..

## 🔧 Step 3: Prepare Your my-values.yaml
   refer my-values.yaml file in repo

## step4: Install Superste with helm

```bash

helm upgrade --install superset superset/superset -n superset --create-namespace --values my-values.yaml --timeout 20m

```
## step5: create Confidential information kubernates env 

```bash

kubectl create secret generic superset-db-creds --from-literal=DB_USER='<your-db-user>' --from-literal=DB_PASS='<your-db-password>' --from-literal=DB_HOST='<your-db-host>' --namespace superset


 kubectl create secret generic superset-secret --from-literal=secret-key='<your-secret-key>' --namespace superset
```

#Example :


kubectl create secret generic superset-db-creds --from-literal=DB_PASS='PassW%40rEP%24qlSs' --namespace superset


## step6: Update already created secrets

```bash


kubectl delete secret superset-db-creds -n superset
kubectl create secret generic superset-db-creds -n superset --from-literal=DB_PASS="MyPassword"
```
## step7: Verify Secrets

```bash

kubectl get secret superset-db-creds -n superset -o yaml
```
## step 8: verify Superset Configuration

```bash

helm template superset superset/superset -n superset --values my-values.yaml > rendered.yaml
```
## step9: Run Superset

```bash

helm upgrade --install superset superset/superset -n superset --create-namespace --values my-values.yaml --timeout 20m
```




