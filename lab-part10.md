- Create a blue/green Deployment setup for the existing web-frontend Deployment in the bluegreen Namespace.
You can find a Deployment manifest for web-frontend on the CLI server at /home/cloud_user/bluegreen/web-frontend.yml. Feel free to copy this manifest in order to create your green Deployment.
Give the green Deployment the name web-frontend-green and set the env label to green in the Pod template.
Once you have created the green Deployment and verified that it is working, modify the web-frontend-svc
Service so that it points only to the new green Deployment's Pods. This is a NodePort Service, and you can test it by reaching out to port 30081 on any of the cluster nodes, for example: curl k8s-control:30081.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-frontend-green
  namespace: bluegreen
spec:
  replicas: 2
  selector:
    matchLabels:
      app: web-frontend
      env: main
      env: green
  template:
    metadata:
      labels:
        app: web-frontend
        env: main
        env: green
    spec:
      containers:
      - name: nginx
        image: nginx:stable
        ports:
        - containerPort: 80
        volumeMounts:
        - name: labels
          mountPath: /usr/share/nginx/html
      volumes:
      - name: labels
        downwardAPI:
          items:
          - path: index.html
            fieldRef:
              fieldPath: metadata.labels
```

```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-frontend-svc
  namespace: bluegreen
spec:
  ports:
  - nodePort: 30081
    port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: web-frontend
    env: green
  sessionAffinity: None
  type: NodePort
```

- Create a canary Deployment setup for the existing auth Deployment in the canary Namespace.
There is a manifest file for the existing Deployment at /home/cloud_user/canary/auth.yml. You can copy 
this file to create your canary Deployment.
Give the canary Deployment the name auth-canary, and set the env label in the Pod template to canary.
There is a Service called auth-svc, also in the canary Namespace. Modify this Service to direct
traffic to both the main and canary Deployments. Configure your setup so that there will be 4 
total replicas, including both the main Deployment and the canary Deployment, and so that 
approximately 25% of traffic will go to the canary Pod(s). Note that you may need to modify 
the original main Deployment in order to accomplish this.
he Service is a NodePort Service, and you can test it by reaching out to port 30082 on any 
of the cluster nodes, for example: curl k8s-control:30082

