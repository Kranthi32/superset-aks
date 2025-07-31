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

# first time this step will take Minimum 15 minutes Time

## step10: port forward

```bash
 kubectl port-forward svc/superset -n superset 8088:8088
```


## step11: Verify Pods

```bash

kubectl get pods -n superset

```


## ⚙️ Get Pods, Services, and Cluster IP

```bash
# Get services with ports and Cluster IPs
kubectl get svc -n superset

# Get running Superset pods
kubectl get pods -n superset

```


## 📦 Check Superset Version


```bash

kubectl exec -it -n superset <your-superset-pod-name> -- superset version
```

## 🔐 Reset Admin Password
```bash
kubectl exec -it -n superset <your-superset-pod-name> -- superset fab reset-password --username admin
```
---
You'll be prompted to enter a new password.
## ♻️ Uninstall & Clean Reinstall Superset

```bash
helm uninstall superset -n superset
kubectl delete pvc --all -n superset
kubectl delete pods --all -n superset

```


## 📦 Custom Superset Docker Image (MSSQL, PostgreSQL, Redis)
---
refer custom image Docker file 
# redirect to custom image Docker file yaml path execute below commands
```bash
docker build -t <your-username>/superset-full:4.1.2 .
docker push <your-username>/superset-full:4.1.2
```


##  Updaet  Superset

```bash

helm upgrade --install superset superset/superset -n superset --create-namespace --values my-values.yaml --timeout 20m
```

## Nginx Ingress Load Balencer Configuration

----

## 🌐 NGINX Ingress Controller + Load Balancer Setup

### 📦 Step 1: Connect to AKS Cluster

```bash
az aks get-credentials --resource-group <your-resource-group> --name <your-aks-cluster>
```
## 🌍 Step 2: Install Ingress NGINX via Helm


```bash

helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
kubectl create namespace ingress-nginx

helm install ingress-nginx ingress-nginx/ingress-nginx --namespace ingress-nginx

```

## 🔎 Step 3: Get Ingress LoadBalancer IP

```bash
kubectl get svc -n ingress-nginx

```
# Look for the EXTERNAL-IP under ingress-nginx-controller. This is used to access Superset externally.

## 🧭 Step 4: Verify Superset Ingress

```bash
kubectl get ingress -n superset
```
# Output should show a valid host like:

superset.20.235.237.100.nip.io
Note: Ensure your my-values.yaml uses this hostname in the ingress section.


## 🐚 Step 5: Troubleshooting / Debugging
Check Ingress NGINX logs:
```bash
kubectl logs -n ingress-nginx -l app.kubernetes.io/component=controller
```
---
Enter the Superset pod:
```bash
kubectl exec -it <superset-pod-name> -n superset -- /bin/bash

## ✅ Final Check
# Superset should now be accessible at:

```bash
http://<external-ip> or http://superset.<external-ip>.nip.io
```







