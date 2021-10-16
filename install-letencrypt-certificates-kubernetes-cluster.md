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

$ helm upgrade --install cert-manager --namespace cert-manager --version v1.5.4 jetstack/cert-manager

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

# setup issuer to issue TLS certificates

Create an issuer that issues Let's Encrypt certificates
# prod-issuer.yaml

apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    # Email address used for ACME registration
    email: email@email.com
    server: https://acme-v02.api.letsencrypt.org/directory
    privateKeySecretRef:
      # Name of a secret used to store the ACME account private key
      name: letsencrypt-prod-private-key
    # Add a single challenge solver, HTTP01 using nginx
    solvers:
    - http01:
        ingress:
          class: nginx


Above config defines a ClusterIssuer that contacts Let's Encrypt in order to issue certificates.

You also provide email to be notified of urgent notices e.g certificate expiry.

# Roll it out with kubectl

$ kubectl apply -f prod-issuer.yaml


# sample ingress
# example-app-ingress.yaml

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example-app-ingress
  annotations:
    kubernetes.io/ingress.class: "nginx"
    cert-manager.io/cluster-issuer: "letsencrypt-prod"
spec:
  tls:
  - hosts:
    - exampleapp.domain.com
    secretName: example-app-tls
  rules:
  - host: "exampleapp.domain.com"
    http:
      paths:
      - pathType: Prefix
        path: "/"
        backend:
          service:
            name: example-service
            port:
              number: 80

# check certificate status

kubectl describe certificate cert-name

# output

  Type    Reason     Age    From          Message
  ----    ------     ----   ----          -------
  Normal  Issuing    4m14s  cert-manager  Issuing certificate as Secret does not exist
  Normal  Generated  4m13s  cert-manager  Stored new private key in temporary Secret resource "cert-name-tls-5xcmd"
  Normal  Requested  4m13s  cert-manager  Created new CertificateRequest resource "cert-name-tls-x8mcx"
  Normal  Issuing    2m25s  cert-manager  The certificate has been successfully issued

# delete a certificate from kubernetes cluster

kubectl delete certificates cert-name

# Uninstall the chart

$ helm delete my-release

The command removes all the Kubernetes components associated with the chart and deletes the release.

If you want to completely uninstall cert-manager from your cluster, you will also need to delete the previously installed CustomResourceDefinition resources:

$ kubectl delete -f https://github.com/jetstack/cert-manager/releases/download/v1.5.4/cert-manager.crds.yaml


# Issues and possible resolutions

# 1. on digital ocean kubernetes cluster, encountered the error :  self-check failed

# resolution

edit ingress service and add the below and annotations 

service.beta.kubernetes.io/do-loadbalancer-hostname: "dm.example.com" #



# REFERENCES

https://www.digitalocean.com/community/questions/issue-with-waiting-for-http-01-challenge-propagation-failed-to-perform-self-check-get-request-from-acme-challenges

https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nginx-ingress-with-cert-manager-on-digitalocean-kubernetes#step-5-%E2%80%94-enabling-pod-communication-through-the-load-balancer-optional

https://artifacthub.io/packages/helm/cert-manager/cert-manager?modal=security-report


https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nginx-ingress-on-digitalocean-kubernetes-using-helm 


https://www.fairwinds.com/blog/automated-ssl-certs-for-kubernetes-with-letsencrypt-and-cert-manager 

https://www.digitalocean.com/community/questions/let-s-encrypt-on-load-balancer-and-ingress-k8s 
