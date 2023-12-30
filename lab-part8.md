- Find the Pod with the Highest CPU Usage

Locate the Pod within the cpu Namespace that is using the most CPU resources.

Save the name of that Pod in the file /home/cloud_user/metrics/high-cpu-pod-name.txt.


```bash
kubectl top pod -n cpu
```
