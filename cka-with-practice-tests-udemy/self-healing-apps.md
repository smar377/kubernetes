### Self Healing Applications

Kubernetes supports self-healing applications through `ReplicaSets` and `Replication Controllers`. The replication controller helps in ensuring that a Pod is re-created automatically when the application within the Pod crashes. It helps in ensuring enough replicas of the application are running at all times.

Kubernetes provides additional support to check the health of applications running within Pods and take necessary actions through **Liveness and Readiness Probes**. However these are not required for the CKA exam and as such they are not covered here.
