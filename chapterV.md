### Application Environment, Security

    Custom Resources (CRD)
    Service Account and Authentication
    Admision Control
    Application Configuration
    Security Context
    Manage compute resources

#### CRD (Custom Resource Definition)

    Is an extension of the kubernetes APU. It allows you to define
    your own custom Kubernetes ojbect types and interact with them.
 
    A CustomResourceDefinition defines a custom resources
    Custom Controller act upon custom resources.

#### Examples

The name of the CRD must be the same as the plural name of the CRD plus
the api group. The api group is the same as the namespace.

``` yaml
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  name: beehives.acloud.guru
spec:
 group: acloud.guru
 names:
  plural: beehives
  singular: beehive
  kind: Beehive
  shortNames:
  - bh
 scope: Namespaced
 versions:
  - name: v1
    served: true
    storage: true
    schema:
     openAPIV3Schema:
      type: object
      properties:
        spec:
          type: object
          properties:
            supers:
                type: integer
            bees:
                type: integer        
```    
Usage of the CRD

```yaml
apiVersion: acloud.guru/v1
kind: Beehive
metadata:
  name: my-beehive
spec:
 supers: 1
 bees: 1000
    
```

#### ServiceAccount

A ServiceAccount allows proessses within containers to 
authenticate with the Kubernetes API Server. They can be
assigned permissions via role-based accsss control, just like
regular user accounts.


```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: my-service-account

```
Create the role

``` yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: list-role-pod
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["list"]
```

Create the role binding

``` yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: list-role-pod-binding
subjects:
- kind: ServiceAccount
  name: my-service-account
  namespace: default
roleRef:
 kind: Role
 name: list-role-pod
 apiGroup: rbac.authorization.k8s.io
``` 

#### Exploring Admision Control

Admission Control intercept requests to the kubernetes API after
authentication and authorization, but before any objects are
persisted. They can be used to validate, deny o even modify 
the request.


``` yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
  namespace: new-namesapce
spec;   
  containers:
  - name: busybox
    image: busybox:stable
    command: ['sh', '-c', 'while true; do echo "Hello, Kubernetes!"; sleep 5; done
``` 

You can modify the --enable-admission-plugins:NodeRestriction, NamespaceAutoProvision

Exam tips
- Tip 1: Admission controllers intercept request to the Kuberentes API
an can be used to validate, deny or even modify the request.

#### Managing compute resource usage

 | Requests                                                                               | Limits| 
 |----------------------------------------------------------------------------------------| ----- |
 | Provides Kubernetes with an idea of how many resources a container is expected to use  | Provides an upper limit on how many resources a container is allowed to use|
 | The cluster uses this information to select a node that has enough resources available | The cluster uses this information to terminate the container process if it attemps to use more than the allowed amount.|

ResourceQuota:
- Limits the total amount of compute resources that can be used in a namespace

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: resource-pod
  namespace: resource-namespace
spec:
  containers:
  - name: busybox
    image: busybox:stable
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
    command: ['sh', '-c', 'while true; do echo "Hello, Kubernetes!"; sleep 5; done

```

modify /etc/kubernetes/manifests/kube-apiserver.yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: kube-apiserver
  namespace: kube-system
spec:
  containers:
  - command:
    - kube-apiserver
    - --enable-admission-plugins=NodeRestriction,ResourceQuota
    - --request-timeout=10s
    - --service-account-signing-key-file=/etc/kubernetes/pki/sa.key
    image: k8s.gcr.io/kube-apiserver:v1.18.

```
Creating ResourceQuoa
Upper limit for a newspace
```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-resources
  namespace: resource-namespace
spec:
  hard:
    requests.cpu: "1"
    requests.memory: 1Gi
    limits.cpu: "2"
    limits.memory: 2Gi
```
Attemp to create a pod that exceeds the quota

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: resource-pod
  namespace: resource-namespace
spec:
  containers:
  - name: busybox
    image: busybox:stable
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
    command: ['sh', '-c', 'while true; do echo "Hello, Kubernetes!"; sleep 5; done']

```

Tip 1: A resource request informs the cluster of the expected resource usage
for a container. It is used to select a node that has enough resources available.

Tip 2: A resource limit is an upper limit on the amount of resources a container
is allowed to use. It is used to terminate the container process if it attemps to use
more than the allowed amount.

Tip 3: A ResourceQuota limits the total amount of compute resources that can be used
in a namespace. If a user attemps to create a pod that exceeds the quota, the pod will
be rejected.

### Lab1: Managing Resources Usage in Kubernetes
- Modify the request and limit in the pod definition
- Create a resource quota in the namespace

#### Configuring Applications with ConfigMaps and Secrets

ConfigMaps: Allow you to decouple configuration artifacts from image content to keep containerized applications portable.
Secrets: Allow you to decouple sensitive content from image content to keep containerized applications portable.

| Volume Mounts | Environment Variables |
|---------------|-----------------------|
| Data apppears in the container's file system at runtime | Data appears in the container's environment at runtime |
| Each top-lvel key in the config data beccomes a file name | You can specify specific keys and variable|

Create a configmap

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-config-map
data:
  myKey: myValue
  app.cfg: |
    [server]
    port=8080
    timeout=30
    [db]
    host=localhost
    port=3306
    user=root
    password=secret
```
#### Security Context

 UserId and GroupID
 

Security Contexts allow you to control the security settings for a pod or container. 
They can be used to:
- Run a container as a non-root user
- Run a container with a read-only root filesystem
- Run a container with a supplemental group


