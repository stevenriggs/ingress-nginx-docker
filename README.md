# Kubernetes NGINX Ingress Controller

The Kubernetes Ingress resource manages external access to the services in a Kubernetes cluster. The Ingress can provide load balancing, SSL termination and name-based virtual hosting.  In order for the Ingress resource to work, the cluster must have an Ingress Controller running.  This will likely be the first object to deploy to a new Kubernetes cluster.

- https://kubernetes.io/docs/concepts/services-networking/ingress/
- https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/

## Reference
- https://github.com/kubernetes/ingress-nginx


## Kubernetes deployment to the local workstation (macOS only)

### Prep your local workstation (macOS only)
1. Clone this repo and work in it's root directory
1. Install Docker Desktop for Mac (https://www.docker.com/products/docker-desktop)
1. In Docker Desktop > Preferences > Kubernetes, check 'Enable Kubernetes'
1. Click on the Docker item in the Menu Bar. Mouse to the 'Kubernetes' menu item and ensure that 'docker-for-desktop' is selected.

### Setup a wildcard cert if you are hosting multiple sites under one domain in your Kubernetes instance.
~~~
kubectl create namespace ingress
kubectl create secret tls ingress-wildcard-cert-secret --key YourPrivate.key --cert YourCert.pem --namespace ingress

# Then uncomment this line in the kubernetes-deployment.yaml (see sed example further down this readme)

#- --default-ssl-certificate=$(POD_NAMESPACE)/ingress-wildcard-cert-secret
~~~

### Deploy
~~~
kubectl apply -f ./kubernetes/ingress-nginx.yaml
~~~

### See the status of the pods
~~~
kubectl get pods --namespace ingress
kubectl describe pods --namespace ingress
kubectl logs SPLUNKPODNAME --namespace ingress
kubectl get svc --namespace ingress
~~~

### Delete
~~~
kubectl delete -f ./kubernetes/ingress-nginx.yaml
~~~

## How to override values in the Kubernetes deployment

### Use this pattern in a script
~~~
# copy the file to a temp file
cp ./kubernetes/ingress-nginx.yaml yaml.tmp

# replace the values with sed in the temp file
sed -i '' 's/replicas:.*/replicas: 1/' yaml.tmp

# deploy from the temp file
kubectl apply yaml.tmp

# delete the temp file
rm -f yaml.tmp
~~~