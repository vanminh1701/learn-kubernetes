### Job/CronJob
- A job is designed to run a containerized task successfully to completion
- CronJobs run periodically according to a schedule
- The restartPolicy for a Job/CronJob must be OnFailure or Never
- Use activeDeadlineSeconds in the job spec to terminate the job if it runs too long

### Multiple-container Pod
When: the containers need to be tightly coupled, sharing resources such as network and sotrage volumes.

# Design patterns
Sidecar: performs a task to assit the main container
Ambassador: proxy network traffic to from main container
Adaptor: transform the container output in some ways

### Volume
The volumes field in the Pod spec defines detail about volumes used in the Pod
The volumeMOunts field in the container spec mounts a volume to a specific container at a specific location
`hostPath` volumes mount data from a specific location on the host (k8s node)
`emptyDIr` volumes provides temporary storage that uses the host file system and are removed if the Pod is deleted.
 
# PersistentVolume
Defines an abstract storage resource ready to be consumed by pods
Defines details about the type and amount of storage provided

# PersitentVolumeClaim
Defines a request for staorage, including details on the type of storage needed
Automatically binds to an availble PersistentVolume that meets the eprovided requirements.
MOunted in a Pod like any volume

### Deployment
actively manage a desired state for a set of replica Pods
The Pod template provides the Pod configuration that the Deployment will use to create new Pods
The replicas field stes the number of replicas. Edit:
```
k scale deployment/DEPLOYMENT_NAME --replicas=X
k edit deployment DEPLOYMENT_NAME => edit .spec.replicas field
Edit direct in yaml file => apply again 

```

### Rolling update
A rolling update gradually rolls out changes to a Deployment's Pod template by gradually replacing replicas with new ones.
User `kubectl rollout status` to check the status of a rolling update
Roll back the latest rolling update with: `kubectl rollout undo`

# Blue/green deployment
1. create green deployment
2. edit the blue's Service to route traffic to green deployment (edit the pod selector)
3. remove blue deployment

# Canary deployment
1. create the main deployment
2. create the canary deployment
3. edit the Service to match the same label in both main & canary environment

Tips:
can use multiple Deployments to setup blue/green environments in K8s
use labels and selectors on services to direct user traffic to different Pods
a simple way to set up a canary environment in k8s is to use a Service that selects Pods from 2 different Deployments. Very the number of replicas to direct fewer users to the canary environment.

### Probes & Healthcheck
Liveness probes check if a container is healthy so that it can be restart if it is not 
Readiness probes check whether the container is fully startup and ready to be used
Probes can run a command inside the container, make a HTTP reqeust, or attempt a TCP socker connection to determine container status 

### Debugging
Use `kubectl get pods` to check the status of all Pods in a Namespace
Use `kubectl describe` to get detailed information about K8s objects
Use `kubectl logs` to retrieve container logs
Check cluster-level logs if you still cannot locate any releveant information

### Admission Control
Admisson controllers intercept requests to the Kubernetes API and can be used to validate and/or modify them
Enable admisson controllers using the `--enable-admission-plugins` flag for kube-apiserver

### ResourceQuota
A resource request inform the cluster of the expected resource usage for a container. It is used to select a Node that has enough resources available to run the Pod
A resource limit sets an upper limit on how mnay resources a container can use. If the container process attempts to go above this limit, the container process will be terminated.
A ResourceQuota limits the amount of resources that can be used within a specific Namespace. If a user attempts to create or modify objects in that Namespace such that the quota would be exceeded, the requeest will be dined.
