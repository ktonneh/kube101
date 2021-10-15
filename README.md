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

while running the above command got error '''Error: INSTALLATION FAILED: cannot re-use a name that is still in use'''

To Resolve, run

# helm upgrade --install cert-manager --namespace cert-manager --version v1.5.4 jetstack/cert-manager

# success

Release "cert-manager" has been upgraded. Happy Helming!
NAME: cert-manager
LAST DEPLOYED: Fri Oct 15 18:24:31 2021
NAMESPACE: cert-manager
STATUS: deployed
REVISION: 2
TEST SUITE: None
NOTES:
cert-manager v1.5.4 has been deployed successfully!

In order to begin issuing certificates, you will need to set up a ClusterIssuer
or Issuer resource (for example, by creating a 'letsencrypt-staging' issuer).

More information on the different types of issuers and how to configure them
can be found in our documentation:

https://cert-manager.io/docs/configuration/

For information on how to configure cert-manager to automatically provision
Certificates for Ingress resources, take a look at the `ingress-shim`
documentation:


# Uninstall the chart

$ helm delete my-release

The command removes all the Kubernetes components associated with the chart and deletes the release.

If you want to completely uninstall cert-manager from your cluster, you will also need to delete the previously installed CustomResourceDefinition resources:

$ kubectl delete -f https://github.com/jetstack/cert-manager/releases/download/v1.5.4/cert-manager.crds.yaml

# REFERENCES

https://artifacthub.io/packages/helm/cert-manager/cert-manager?modal=security-report


https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nginx-ingress-on-digitalocean-kubernetes-using-helm 
