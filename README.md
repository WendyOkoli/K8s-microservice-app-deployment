# Kubernetes Microservice App Deployment

## Overview

A microservices-based architecture application is deployed on Kubernetes and there’s a need to create a clear IaaC (Infrastructure as Code) deployment to be able to deploy the services in a fast manner. 

All deliverables need to be deployed using an Infrastructure as Code approach.

●  In your solution please emphasize readability and maintainability (make yor application deployment clear)

●  We expect a clear way to recreate your setup and will evaluate the project decisions based on:

Deploy pipeline

Metrics (Alertmanager)

Monitoring (Grafana)

Logging (Prometheus)

●  Use Prometheus as a monitoring tool

●  Use Ansible or Terraform as the configuration management tool.

●  You can use an IaaS provider of your choice.

●  The application should run on Kubernetes


● Setup for provisioning for the sockshop app - https://github.com/microservices-demo/microservices-demo/tree/master

## Prerequisites

● Terraform 

● kubectl 

● Helm 

● AWS CLI (configured with appropriate access)

● Git

![Prerequisites installed](images/Prerequisites.png)

## Workflow Overview
● Deploy EKS Cluster with Terraform

● Deploy Microservice Application

● Deploy Let's Encrypt for SSL/TLS Certificates

● Deploy Prometheus for Monitoring

● Deploy NGINX Ingress Controller

● Write and Implement CI/CD Pipelines

### Step 1 - Deploy EKS Cluster
```
cd infrastructure
git clone https://github.com/hashicorp/learn-terraform-provision-eks-cluster

terraform init
```
![Clone Terraform EKS Cluster and initialized Terraform](images/CloneTerraformEKSCluster.png)

* Once terraform is initialized, run
```
terraform plan
terraform apply -auto-approve
```
![terraform plan](images/terraformplan.png)

![terraform apply](images/terraformapply.png)

### Step 2 - Deploy the Microservice App 
`cd deployments`

* Configure kubectl

`aws eks --region <region-name> update-kubeconfig --name <cluster-name>`

![Configure Kubectl](images/KubectlConfigtoEKS.png)

* Apply the sock-shop.yaml file
`kubectl apply -f sock-shop.yaml`
![Sockshop applied](images/sockshop.yaml%20deployed.png)

* Verify and apply ingress file
 ```
 kubectl get pods -n ingress-nginx
 ```
 ![Verify ingress](images/verify%20ingress-nginx.png)

 ```
 kubectly apply -f sock-shop-ingress.yaml
 kubectl get ingress -n sock-shop-ingress.yaml
 ```
![Apply the ingress file](images/verify%20ingress.png)

* Using a DNS, create a CNAME using your domain name, save it. Then copy the domain name and paste on your browser
![Frontend served](images/frontend%20served.png)

* Install cert manager
 
    First add the Jetstack Helm repo

`helm repo add jetstack https://charts.jetstack.io --force-update`

`helm repo update`

Then install the cert manager
```
helm install cert-manager jetstack/cert-manager \
  --namespace cert-manager \
  --create-namespace \
  --version v1.15.2 \
  --set crds.enabled=true
  ```
  ![Cert applied](images/install%20cert-manager.png)
Verify the cert manager
`kubectl get certificate -n sock-shop`

Refresh your browser to confirm the certifcate has been applied
![Cert worked](images/cert%20worked.png)

### Step 3 - Prometheus and Grafana

* Add the Prometheus community Helm repository
```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```

* Install prometheus for monitoring
```
helm install prometheus prometheus-community/kube-prometheus-stack --namespace sock-shop --create-namespace
```
![Prometheus installed](images/prometheus%20installed.png)

