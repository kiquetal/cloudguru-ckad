- Create a ConfigMap to Store an `haproxy` Config File
- Update a Service to Serve on a New Port.
- Re-configure a Pod to use an haproxy ambassador container

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: haproxy-config
  namespace: haproxy
data:
  haproxy.cfg: |
    frontend ambassador
      bind *:7171
      default_backend api_svc
    backend api_svc
      server svc api-svc:8181
```
Modify the pod to use ambassado with localhost

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: client
spec:
  containers:
  - name: main
    image: radial/busyboxplus:curl
    command: ['sh', '-c', 'while true; do curl -m 3 localhost:7171; sleep 5; done']
  - name: ambassador
    image: haproxy:2.4
    command: ['haproxy', '-f', '/etc/haproxy/haproxy.cfg', '-db']
    volumeMounts:
    - name: config
      mountPath: /etc/haproxy
  volumes:
  - name: config
    configMap:
      name: haproxy-config
```
