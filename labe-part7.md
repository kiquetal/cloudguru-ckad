- Run a Job on a schedule
- Create a deployment expose a port,use a environment variable called NGINX_PORT

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: pwd-runner
  namespace: one
spec:
  schedule: "*/1 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: pwd-runner
            image: busybox:stable
            command: ['sh', '-c', 'pwd']
          restartPolicy: Never
          activeDeadlineSeconds: 10
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-dep
  namespace: two
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx-dep
        image: nginx:stable
        ports:
        - containerPort: 8081
        env:
        - name: NGINX_PORT
          value: "8081"
```
