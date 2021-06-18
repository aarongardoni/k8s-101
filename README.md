# k8s-101

## Install nginx ingress controller

### Docker Desktop
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.47.0/deploy/static/provider/cloud/deploy.yaml
```

or see [here](https://kubernetes.github.io/ingress-nginx/deploy/).

## Create a playground namespace.
```
kubectl create namespace playground
```

## Use kubens to configure the namespace

```
kubens playground
```

This means our context is set to the `playground` namespace so we don't have to append
`--namespace money` to all our commands.

## Apply configmap

```
kubectl apply -f configmap-apple.yaml
```

## Create secret

```
kubectl create secret generic echo-secret-banana \
  --from-literal=username=yoyo \
  --from-literal=password='p4ssw0rd'
```

## Apply deployments

```
kubectl apply -f deployment-apple.yaml -f deployment-banana.yaml
```

### Check out the deployments

```
kubectl get deployments
```
or
```
kubectl get pods -o wide
```

Shell into a pod and curl the other pod via its IP. 
```
kubectl exec -it <pod-name> bash
$ curl <other-pods-IP>
```

## Apply the services

```
kubectl apply -f service-apple.yaml -f service-banana.yaml
```

### Checkout the services
```
kubectl get services
```

Shell into a pod and curl the other pod via the service. 
```
kubectl exec -it <pod-name> bash
$ curl echo-service-<apple or banana>.playground.svc.cluster.local:8080
```

Hit a service from outside the cluster via kubectl port forwarding.
```
kubectl port-forward service/echo-service-apple 8080
curl localhost:8080
```

View the logs of a pod

```
kubectl logs <pod-name>
```

## Apply the ingresses

```
kubectl apply -f ingress-apple.yaml -f ingress-banana.yaml
```

No need to edit /etc/hosts -> checkout https://nip.io

### Hit the ingresses

Now that there is an ingress pointing to the service, there is no need to
port forward as the ingresses accepts traffic external to the cluster. 

```
curl -X POST apple.127.0.0.1.nip.io/hello --data '{ "hello": "apple" }'
```
or
```
curl -X POST apple.127.0.0.1.nip.io/hello --data '{ "hello": "banana" }'
```

Clean up

```
kubectl delete namespace playground
```
