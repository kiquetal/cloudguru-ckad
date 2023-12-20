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

