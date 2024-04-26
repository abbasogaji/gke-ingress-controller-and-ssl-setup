# Kubernetes Ingress with TLS/SSL  

This repo is demoing the configuration for Ingress and HTTPS/TLS/SSL in Kubernetes.  

In Kubernetes, we can expose the services publicly by choosing the type LoadBalancer. That will create a public IP address for each service. But, we want to reduce the number of IP adresses to make some saving. And we want to map a URL like mycompany.com/login and mycompany.com/products to the right service object.  
Well, this could be done through Kubernetes Ingress resources.  

Kubernetes API doesn't provide an implementation for an ingress controller. So, we need to install it ourself. 

Many ingress controllers are supported for Kubernetes:  

1) Nginx Controller 
2) HAProxy Ingress, Contour, Citrix Ingress Controller  
3) API Gatways like Traeffic, Kong and Ambassador  
4) Service mesh like Istio  
5) Cloud managed ingress controllers like Application Gateway Ingress Controller (AGIC), AWS ALB Ingress Controller, Ingress GCE  

The first part will start by configuring Ingress:

1) Installing an ingress controller (NGINX) into Kubernetes.
2) Deploying 2 different applications/services.
3) Configuring ingress to route traffic depending on the URL.  

The second part will deal with configuring SSL/TLS using Cert Manager.  

```bash

-----USING HELM-----------------
1) FIRST CREATE A RESERVED REGIONAL IP ADDRESS

2) # Add the Helm chart for Nginx Ingress
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update

3) # Install the Helm (v3) chart for nginx ingress controller
# (If using Bash instead of Powershell, replace ` with \)
helm install app-ingress ingress-nginx/ingress-nginx \
     --namespace ingress \
     --create-namespace \
     --set controller.replicaCount=2 \
     --set controller.service.loadBalancerIP=XXXXXXXXXXXX

# the XXXXXXXX represents the RESERVED REGIONAL STATIC IP CREATED


4) # Create a namespace for Cert Manager
kubectl create namespace cert-manager

5) # Get the Helm Chart for Cert Manager
helm repo add jetstack https://charts.jetstack.io
helm repo update

pre-6) Install CDRS (new annotications and object config for the clusterIssuer, Issuer and cert manager)
kubectl apply --validate=false -f https://github.com/jetstack/cert-manager/releases/download/v0.15.1/cert-manager.crds.yaml


6) # Install Cert Manager using Helm charts
helm install cert-manager jetstack/cert-manager \
    --namespace cert-manager \
    --version v0.14.0


7) # Install the Cluster Issuer
kubectl apply -f ssl-tls-ingress.yaml (same namespace as your deployment and services)


8) # Install the Ingress resource configured with TLS/SSL
kubectl apply -f ingress-resource-ssl (same namespace as your deployment and services and cluster issuer)








for more info checkout
- https://github.com/HoussemDellai/kubernetes-ingress-tls-ssl-https
- https://www.youtube.com/watch?v=M7t0cpQyzKA&t=88s

```
