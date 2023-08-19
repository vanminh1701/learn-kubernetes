# Learn Kubernetes resource and apply demo with cert-manager chart

## Structure folder
`resources`
- Basic k8s objects from ACloudGuru course and they are applied to local minikube.

`terraform-script` 
- A Demo create AWS EKS by public terraform eks registry

- Using `Helm` chart to apply
    
    ingress-nginx and cert-manager to deploy a simple website container
    
    Ingress create AWS Network Load Balancer, and manually pointed to custom domain in Cloudflare DNS 
    cert-manager help to create TLS certification and apply to custom domain name


# Integrate Nginx Ingress 
## Install ingress-nginx with Helm chart
Add ingress-nginx chart to local machine

`helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx`

Install `quickstart` chart to EKS cluster

`helm install quickstart ingress-nginx/ingress-nginx --set controller.service.annotations."service\.beta\.kubernetes\.io/aws-load-balancer-type"="nlb"`

## Install cert-manager
`kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.12.0/cert-manager.yaml`

## Install manifests
`kubectl apply -f manifests/`

`kuard-deployment.yaml`: Setup a Deployment, Service and INgress to run container `kuard`

`cert-issuer.yaml`: Issue a free TLS certification with Let's Encrypt service

## Learn Terraform - Provision an EKS Cluster

This repo is a companion repo to the [Provision an EKS Cluster tutorial](https://developer.hashicorp.com/terraform/tutorials/kubernetes/eks), containing
Terraform configuration files to provision an EKS cluster on AWS.

Setup a EKS cluster with 2 nodes inside a VPC
Config kubectl in local machine
Run the following command to retrieve the access credentials for your cluster and configure kubectl.
```
aws eks --region $(terraform output -raw region) update-kubeconfig \
    --name $(terraform output -raw cluster_name)

```

Verify the Cluster
`kubectl cluster-info`