-  Create a Pod with Resource Requests
-  Create and consume a Secret

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: apple
  namespace: dev
spec:
  containers:
  - name: apple
    image: nginx:stable
    command: ['sh', '-c', 'echo "Hello, Kubernetes!" > /var/data/index.html; sleep 3600']
    resources:
      requests:
        memory: "256Mi"
        cpu: "250m"
```

```yaml  
apiVersion: v1
kind: Secret
metadata:
  name: secret-code
  namespace: secure
type: Opaque
data:
  code: dHJ1c3RubzEK
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: secret-keeper
  namespace: secure
spec:
  containers:
  - name: secret-keeper
    image: busybox:stable
    command: ['sh', '-c', 'echo $SECRET_STUFF']
    env:
    - name: SECRET_STUFF
      valueFrom:
        secretKeyRef:
          name: secret-code
          key: code
```
