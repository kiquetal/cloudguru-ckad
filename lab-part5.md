- Create a PersistentVolume
- Create a Pod that uses the PersistentVolume

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-data
spec:
  storageClassName: static
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /var/pvdata
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pv-pod
spec:
  containers:
  - name: pv-pod
    image: busybox:stable
    command: ['sh', '-c', 'echo "Hello, Kubernetes!" > /var/data/index.html; sleep 3600']
    volumeMounts:
    - name: pv-storage
      mountPath: /var/data
  volumes:
  - name: pv-storage
    persistentVolumeClaim:
      claimName: pv-claim
```

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pv-claim
spec:
  storageClassName: static
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

```yaml

