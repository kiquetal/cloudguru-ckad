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

    
