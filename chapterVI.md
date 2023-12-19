kubectl create namespace np-test-a

kubectl create namespace np-test-b

kubectl label namespace np-test-a team=ateam

kubectl label namespace np-test-b team=bteam

``` yaml
apiVersion: v1
kind: Pod
metadata:
	name: server-pod
	namespace: np-test-a
	labels:
		app: np-test-server
spec:
	containers:
	- name: nginx
		image: nginx:stable
		ports:
		- containerPort: 80
```

``` yaml

apiVersion: v1
kind: Pod
metadata:
	name: client-pod
	namespace: np-test-b
	labels:
		app: np-test-client
spec:
	containers:
	- name: busybox
		image: radial/busyboxplus:curl
		command: ['sh','-c','while true;do curl -m 2 <serverpod>; sleep5;done']

```

``` yaml

apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
	name: np-test-a-default-deny-ingress
	namespace: np-test-a
spec:
	podSelector: {}
	policyTypes:
	- Ingress
```

The podSelector {} include all pods inside the namespace.

Tip1: If a Pod is not selected by any NetworkPolicy , the pod is non-isolated,

and all traffic is allowed

Tip2: If a Pod is selected by any NetworkPolicy, traffic will be blocked unless it is

allowed by at least 1 NetworkPolicy that selects the Pod

Tipo3: If you combine a namespaceSelector and a podSelector within the same rule,

the traffic must meet both the Pod- and Namespace related conditions in order to be allowed.

Apologies for the confusion in my previous response. You are correct, there is an inconsistency in the example provided.

In the given NetworkPolicy example:

``` yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
	name: np-test-b-allow-nginx
	namespace: np-test-b
spec:
	podSelector:
		matchLabels:
			app: np-test-client
	ingress:
	- from:
		- namespaceSelector:
			matchLabels:
				team: ateam
		- podSelector:
			matchLabels:
				app: np-test-server

```

The `podSelector` is set to `matchLabels: app: np-test-client`, but in the `ingress` section, it specifies `np-test-server` as the label. This is an inconsistency in the example. I apologize for the confusion caused by this mistake.

To clarify, the `podSelector` in the `ingress` section should match the label of the Pods that you want to allow incoming traffic from. In this example, it should be `matchLabels: app: np-test-client`.

Again, I apologize for the confusion in my previous responses. Thank you for pointing out the mistake. Please let me know if you have any further questions or need any more clarification.

Create a NetworkPolicy to allow

``` yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
	name: np-test-client-allow
	namespace: np-test-a
spec:
	podSelector:
		matchLabels:
			app: np-test-server
ingress:
	- from:
		- namespaceSelector:
				matchLabels:
					team: bteam
			podSelector:
				matchLabels:
					app: np-test-client
		port:
			- protocol: TCP
				port: 80
```

For a egress

deny outgoing from np-test-b

``` yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
	name: np-test-b-default-deny-egress
	namespace: np-test-b
spec:
	podSelector: {}
	policyTypes:
	- Egress
```

``` yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
	name: np-test-client-allow-egress
	namespace: np-test-b
spec:
	podSelector:
		matchLabels:
			app: np-test-client
	policyTypes:
	- Egress
	egress:
		- to:
				- namespaceSelector:
						matchLabels:
							team: ateam
			ports:
			- protocol: TCP
				port: 80
```

Ingress routing

An Ingress is a kubernetes object that manages access to services from outside the cluster.

Service 7

SSL termination.

``` yaml
apiVersion: v1
kind: Pod
metadata:
	name: ingress-test-pod
	labels:
		app: ingress-test
spec:
	containers:
	- name: nginx
		image: nginx:stable
		ports:
		- containerPort: 80
			
```
Creating a Service

``` yaml
apiVersion: v1
kind: Service
metadata:
    name: ingress-test-service
spec:
    selector:
        app: ingress-test
    ports:
    - protocol: TCP
        port: 80
        targetPort: 80
```



Creating an Ingress

``` yaml

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
    name: ingress-test-ingress
spec:
    ingressClassName: nginx
    rules:
    - host: ingress-test.com
        http:
            paths:
            - path: /
                pathType: Prefix
                backend:
                    service:
                        name: ingress-test-service
                        port:
                            number: 80

```

