### Application Observability and Maintenance

- Probes: Liveness, Readiness, Startup
Readiness probe: is the application ready to receive traffic?
- If the readiness probe fails, the pod is removed from the service endpoint.
- If the liveness probe fails, the pod is restarted.
- If the startup probe fails, the pod is restarted.


    
