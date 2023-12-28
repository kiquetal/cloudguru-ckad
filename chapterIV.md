### Application Observability and Maintenance

- Probes: Liveness, Readiness, Startup
Readiness probe: is the application ready to receive traffic?
- If the readiness probe fails, the pod is removed from the service endpoint.
- If the liveness probe fails, the pod is restarted.
- If the startup probe fails, the pod is restarted.
Startup probe: for slow starting applications
Liveness probe: is the application still running?

#### Example of a Pod with a Liveness Probe

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: liveness-pod
spec:
  containers:
  - name: busybox
    image: busybox:stable
    command: ['sh', '-c', 'while true; do echo "Hello, Kubernetes!"; sleep 5; done']
    livenessProbe:
       exec:
        command:
        - echo
        - "health check"
       initialDelaySeconds: 5
       periodSeconds: 5
```

#### Implement readiness pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: readiness-pod
spec:
  containers:
  - name: nginx
    image: nginx:stable
    livenessProbe:
      httpGet:
        path: /
        port: 80
      initialDelaySeconds: 3
      periodSeconds: 5
    readinessProbe:
      httpGet:
        path: /
        port: 80
      initialDelaySeconds: 15
      periodSeconds: 5
```

#### Monitor kubernetes Applications

kubectl top pod
kubectl top node


#### Rolling Updates

- kubectl set image deployment nginx-deployment nginx=nginx:1.9.1 --record

- kubectl edit deployment nginx-deployment

kubectl rollout status deployment nginx-deployment
the previous command will show the status of the rollout

For the undo

kubectl rollout undo deployment nginx-deployment

#### Blue-Green Deployments

A blue/green deployment strategy involves using 2 identical 
envirnment, usually called blue and green.

The blue environment is the current production environment.
The green environment is the new environment that will be used

Canary: deploy a new version of the application to a subset of users
A small percentage of users will be routed to the new version of the application

#### Helm

Is a package management tool for applications that run in Kubernetes. It allows you to
easily install software in your cluster, alongside the necessary kuberentes configuration.



