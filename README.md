# Kubernetes Resource Allocation, Pod Scheduling, Scaling & Load Balancing

## Overview

This experiment demonstrates Kubernetes resource management and traffic distribution using a three-node Kubernetes cluster created with **KIND (Kubernetes in Docker)**.

An NGINX application was deployed across two worker nodes to study resource allocation, pod scheduling, horizontal scaling, service discovery, and load balancing.

CPU and memory requests and limits were configured for application pods. The deployment was initially created with six replicas and subsequently scaled to twelve replicas. A Kubernetes NodePort Service was configured to expose the application internally through a stable service endpoint.

A traffic distribution experiment was performed using a temporary BusyBox client, which generated 30 HTTP requests to the Kubernetes Service. Responses from all twelve backend pods were observed.

## Objectives

- Create and inspect a three-node Kubernetes cluster using KIND.
- Monitor CPU and memory utilization of cluster nodes.
- Configure CPU and memory requests and limits for application containers.
- Understand Kubernetes pod scheduling and resource allocation.
- Deploy an NGINX application using Kubernetes Deployments.
- Scale the application from 6 to 12 replicas.
- Examine pod distribution across worker nodes.
- Analyze resource allocation using Kubernetes node descriptions.
- Configure a NodePort Service for application access.
- Inspect Service endpoints and EndpointSlices.
- Demonstrate traffic distribution across multiple backend pods.

## Technology Stack

| Component | Technology |
|---|---|
| Container orchestration | Kubernetes |
| Cluster environment | KIND |
| Container runtime | containerd |
| Application server | NGINX |
| Monitoring | Metrics Server |
| Test client | BusyBox |
| Command-line interface | kubectl |
| Service type | NodePort |

## System Architecture

The experiment uses a three-node Kubernetes cluster consisting of one control-plane node and two worker nodes.

**Cluster configuration:**

- 1 Control Plane Node
- 2 Worker Nodes
- 12 NGINX Application Pods
- 1 Kubernetes NodePort Service
- 1 Temporary BusyBox Client Pod
- Metrics Server for resource monitoring

### Architecture Diagram

```text
                 Kubernetes Cluster (KIND)
                            |
                  +-------------------+
                  |   Control Plane   |
                  |    Scheduling     |
                  |   Management      |
                  +-------------------+
                            |
              +-------------+-------------+
              |                           |
       +-------------+             +-------------+
       |  Worker 1   |             |  Worker 2   |
       |             |             |             |
       | 6 NGINX     |             | 6 NGINX     |
       | Pods        |             | Pods        |
       +-------------+             +-------------+
              |                           |
              +-------------+-------------+
                            |
                   nginx-service
                     NodePort
                      30080
                            |
                    BusyBox Client
                            |
                    HTTP Requests
                            |
                 Backend Pod Responses
```

## Resource Allocation Configuration

Each NGINX container was configured with the following CPU and memory requests and limits.

| Resource | Request | Limit |
|---|---:|---:|
| CPU | 100m | 200m |
| Memory | 64Mi | 128Mi |

**Understanding resource requests and limits:**

- **CPU Request:** The amount of CPU allocated for scheduling calculations.
- **CPU Limit:** The maximum CPU usage permitted for the container.
- **Memory Request:** The amount of memory considered during pod scheduling.
- **Memory Limit:** The maximum memory the container can use before it may be terminated due to an out-of-memory condition.

The deployment was initially configured with 6 replicas and later scaled to 12 replicas.

## Experimental Workflow

### 1. Cluster Inspection

Inspected the Kubernetes cluster and verified the status of all three nodes.

```bash
kubectl get nodes -o wide
```

All three nodes reached the `Ready` state.

### 2. Resource Monitoring

Installed Metrics Server and monitored CPU and memory utilization.

```bash
kubectl top nodes
```

This provided an overview of resource consumption across the cluster nodes.

### 3. Deployment Creation

Created an NGINX Deployment with CPU and memory requests and limits.

The initial deployment contained six replicas.

### 4. Pod Scheduling Analysis

Inspected the status and placement of application pods.

```bash
kubectl get pods -o wide
```

Verified that Kubernetes scheduled the application pods across the two worker nodes.

### 5. Horizontal Scaling

Scaled the NGINX deployment from six to twelve replicas.

```bash
kubectl scale deployment resource-demo --replicas=12
```

Verified the updated replica count.

```bash
kubectl get deployments
kubectl get pods -o wide
```

### 6. Resource Allocation Analysis

Inspected node-level resource allocation.

```bash
kubectl describe nodes
```

Analyzed CPU and memory requests and limits associated with scheduled workloads.

### 7. Service Configuration

Created a Kubernetes NodePort Service named `nginx-service` using port `30080`.

The Service provided a stable endpoint for accessing the NGINX backend pods.

### 8. Endpoint Verification

Verified the Service endpoints and EndpointSlices.

```bash
kubectl get endpoints nginx-service
kubectl get endpointslices
```

Confirmed that the Service discovered all twelve application pods as backend endpoints.

### 9. Application Access Verification

Tested application accessibility using Kubernetes port-forwarding.

This verified that the NGINX application was responding successfully.

### 10. Backend Identification

Configured each NGINX pod to return its own pod name in the HTTP response.

This made it possible to identify which backend pod handled each request.

### 11. Load Balancing Test

Created a temporary BusyBox client pod and sent 30 HTTP requests to the Kubernetes Service.

```text
http://nginx-service
```

The responses were examined to identify the backend pods serving the requests.

Responses from all twelve backend pods were observed.

### 12. Resource Cleanup

After completing the experiment, deleted the temporary client pod, NodePort Service, and NGINX Deployment.

The pre-existing `web` Deployment was preserved.

## Load Balancing Demonstration

The load balancing experiment was performed to observe how Kubernetes distributes incoming requests across multiple backend pods.

Each NGINX pod was configured to return a unique response containing its pod name.

A temporary BusyBox client generated 30 HTTP requests to the `nginx-service` Service.

**Test Configuration:**

| Parameter | Value |
|---|---|
| Kubernetes Service | nginx-service |
| Service Type | NodePort |
| NodePort | 30080 |
| Backend Replicas | 12 |
| HTTP Requests | 30 |
| Test Client | BusyBox |

**Observed Result:**

Responses containing the names of all twelve backend pods were observed during the test.

This confirms that requests were successfully routed to multiple backend endpoints through the Kubernetes Service.

*Note: The experiment demonstrates backend participation, not perfectly equal traffic distribution. Kubernetes Service routing does not guarantee an equal number of requests to every pod.*

## Experimental Observations

| No. | Observation | Result |
|---|---|---|
| 1 | Three-node cluster creation | Successful |
| 2 | Node readiness verification | All nodes Ready |
| 3 | CPU and memory monitoring | Successful |
| 4 | Resource requests and limits | Configured |
| 5 | Initial deployment | 6 replicas |
| 6 | Horizontal scaling | 12 replicas |
| 7 | Pod scheduling | 6 pods on each worker |
| 8 | Pod readiness | All 12 pods Running and Ready |
| 9 | Service discovery | 12 endpoints discovered |
| 10 | Application accessibility | Successful |
| 11 | Load balancing test | All 12 backend pods observed |
| 12 | Resource cleanup | Completed |

## Screenshots

The following screenshots document the experiment execution and its results.

| Screenshot | Description |
|---|---|
| `01-cluster-nodes.png` | Three-node KIND cluster |
| `02-node-metrics.png` | CPU and memory monitoring |
| `03-deployment-config.png` | NGINX Deployment configuration |
| `04-initial-deployment.png` | Initial deployment with 6 replicas |
| `05-scaled-deployment.png` | Deployment scaled to 12 replicas |
| `06-pod-distribution.png` | Pod distribution across worker nodes |
| `07a-control-plane-allocation.png` | Control-plane resource allocation |
| `07b-worker-allocation.png` | Worker 1 resource allocation |
| `07c-worker2-allocation.png` | Worker 2 resource allocation |
| `08-pod-resource-details.png` | Individual pod resource configuration |
| `09-load-balancing-test.png` | HTTP traffic distribution test |

## Cleanup

The experiment resources were deleted after successful verification.

**Resources removed:**

- `lb-test` – Temporary BusyBox client pod.
- `nginx-service` – NodePort Service.
- `resource-demo` – NGINX Deployment.

**Preserved resource:**

- `web` – Pre-existing Deployment.

## Conclusion

This experiment successfully demonstrated Kubernetes resource allocation, pod scheduling, horizontal scaling, service discovery, and load balancing using a three-node KIND cluster.

The NGINX application was deployed with CPU and memory requests and limits, initially using six replicas and subsequently scaled to twelve replicas.

The twelve application pods were distributed evenly across the two worker nodes, with six pods on each node. Resource utilization and allocation were inspected using Metrics Server and Kubernetes commands.

A NodePort Service was configured to expose the application, and all twelve backend endpoints were successfully discovered. The load balancing experiment, performed using a temporary BusyBox client, demonstrated that HTTP requests were served by multiple backend pods, with all twelve observed during testing.

Overall, the experiment provided practical understanding of Kubernetes workload management, resource allocation, scheduling, horizontal scaling, service discovery, and traffic distribution.
