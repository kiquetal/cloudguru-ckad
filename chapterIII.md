#### Cron Jobs and Jobs

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: my-job
spec:
  template:
    spec:
      containers:
      - name: my-job
        image: busybox:stable
        command: ['sh', '-c', 'echo "Hello, Kubernetes!"']
      restartPolicy: Never
  backoffLimit: 4
  activeDeadlineSeconds: 30
```
Cronjob
```yaml
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: my-cron-job
spec:
  schedule: "*/1 * * * *"

  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: my-cron-job
            image: busybox:stable
            command: ['sh', '-c', 'echo "Hello, Kubernetes!"']
      restartPolicy: Never
      backoffLimit: 4
      activeDeadlineSeconds: 10
```
