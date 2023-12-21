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



