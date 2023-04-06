# devoxx23 - Kubernetes for beginners

## Play with `kubectl` 

Get info about the cluster : 

`kubectl cluster-info`

Get the nodes : 

`kubectl get nodes`

With labels : 

`kubectl get nodes --show-labels`

Create a namespace : 

`kubectl create namespace devoxxfr`

Set the context to this namespace : 

`kubectl config set-context --current --namespace=devoxxfr`


### Your first deployment 

Monitor the events :

`watch kubectl get events --sort-by=.metadata.creationTimestamp`

Create deployment :

`kubectl create deployment devoxxfr23 --image sebi2706/devoxxfr23:1.0`

Display pods : 

`kubectl get pods` 

with labels : 

`kubectl get pods --show-labels`

Get into the pod : 

`kubectl exec -it pod-id -- /bin/bash`

Inside the pod : 

`curl localhost:8080/hello`

Describe pod :

`kubectl describe pod pod-id`

Delete a pod : 

`kubectl delete pod pod-id`

Scale deployment : 

`kubectl scale deployment devoxxfr23 --replicas 3` 

### Expose your deployment 

`kubectl expose deployment devoxxfr23 --port 8080 --type=LoadBalancer`

Get service : 

`kubectl get service`

If public IP is available : `curl public_ip:8080/hello`

### Roll-out new image 

`kubectl set image deployment/devoxxfr23 devoxxfr23=sebi2706/devoxxfr23:2.0`

## Apply manifests 

Create new namespace :

`kubectl create deployment devoxxfr-manifests`

`kubectl config set-context --current --namespace=devoxxfr-manifests`

### Apply deployment manifest

`kubectl apply -f manifests/devoxxfr23-v1-deployment.yml`

Edit deployment : 

`kubectl edit deployment/devoxxfr23`

Look for `replicas` , set it to `2` , save and exit.

### Apply Service manifest 

`kubectl apply -f manifests/devoxxfr23-service.yml`

## Requests and Limits 

Setting just the cpu request (very high) and check pod going in `pending` status : 

`kubectl apply -f manifests/devoxxfr23-v1-deployment-10cpus.yml`

Setting requests and limits : 

`kubectl apply -f manifests/devoxxfr23-v1-deployment-limits.yml`

## Health probes

`kubectl apply -f manifests/devoxxfr23-v1-deployment-health.yml`

Go into the pod : 

`kubectl exec -it pod-id -- /bin/bash`

Force the readiness probe to fail : 

`curl localhost:8080/health/misbehave`

Observe pod's status.

Make pod ready again : 

`curl localhost:8080/health/behave`

Make pod a zombie and make liveness check fail : 

`curl localhost:8080/health/shot`

Observice pod getting restarted. 

## Playing with service `selector` 

Deploy a second version of the deployment : 

`kubectl apply manifests/devoxxfr23-v2-deployment-health.yml`

2 new pods should appear (`replicas` is set to `2` for v2)

Edit service : 

`kubectl edit svc/devoxxfr23`

Look for `selector` section is set it to :

```
selector:
    app.kubernetes.io/name: devoxxfr23-v2
    app.kubernetes.io/version: "2.0"
```

Save and exit. 

