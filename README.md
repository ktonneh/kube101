# kubernetes101
Repo Contains Snippets of commands and useful resources for kubernetes


# Installing ca-certs on kubernetes cluster with Helm

kubectl create namespace cert-manager

Before installing the chart, you must first install the cert-manager CustomResourceDefinition resources. 

This is performed in a separate step to allow you to easily uninstall and reinstall cert-manager without deleting your installed custom resources.

$ kubectl apply -f https://github.com/jetstack/cert-manager/releases/download/v1.5.4/cert-manager.crds.yaml


# Add the Jetstack Helm repository

$ helm repo add jetstack https://charts.jetstack.io

$ helm repo update


## Install the cert-manager helm chart
$ helm install cert-manager --namespace cert-manager --version v1.5.4 jetstack/cert-manager




# REFERENCES

https://artifacthub.io/packages/helm/cert-manager/cert-manager?modal=security-report


https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nginx-ingress-on-digitalocean-kubernetes-using-helm 
