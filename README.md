\# Kubernetes Resource Allocation, Scheduling and Load Balancing



\## Overview



This experiment analyzes resource allocation, pod scheduling, horizontal scaling, service discovery, and traffic distribution in a three-node Kubernetes cluster using KIND.



The application uses NGINX containers deployed across two worker nodes. Kubernetes resource requests and limits are configured to observe how workloads are scheduled and managed.



\## Objectives



\- Inspect the nodes in a three-node Kubernetes cluster.

\- Monitor CPU and memory utilization.

\- Configure CPU and memory requests and limits.

\- Analyze pod scheduling across worker nodes.

\- Scale an application from 6 to 12 replicas.

\- Configure a Kubernetes NodePort Service.

\- Verify service endpoints and EndpointSlices.

\- Demonstrate traffic distribution across multiple backend pods.



\## Architecture



\- \*\*Cluster:\*\* KIND

\- \*\*Nodes:\*\* 1 control-plane and 2 worker nodes

\- \*\*Container runtime:\*\* containerd

\- \*\*Application:\*\* NGINX

\- \*\*Monitoring:\*\* Metrics Server

\- \*\*Client:\*\* BusyBox

\- \*\*Orchestration:\*\* Kubernetes



\## Resource Configuration



Each NGINX pod was configured with:



| Resource | Request | Limit |

|---|---:|---:|

| CPU | 100m | 200m |

| Memory | 64Mi | 128Mi |



The deployment was initially created with 6 replicas and subsequently scaled to 12 replicas.



\## Experiment Workflow



1\. Inspected cluster nodes using `kubectl get nodes -o wide`.

2\. Installed Metrics Server and monitored node utilization using `kubectl top nodes`.

3\. Created an NGINX Deployment with CPU and memory requests and limits.

4\. Inspected pod status, scheduling, and node distribution.

5\. Scaled the deployment from 6 to 12 replicas.

6\. Analyzed allocated resources using `kubectl describe nodes`.

7\. Created a NodePort Service named `nginx-service` on port `30080`.

8\. Verified service endpoints and EndpointSlices.

9\. Tested application access using Kubernetes port-forwarding.

10\. Assigned unique responses to all 12 NGINX pods.

11\. Created a temporary BusyBox client and sent 30 HTTP requests through the Kubernetes Service.

12\. Observed responses from all 12 backend pods.

13\. Deleted the experiment resources after verification.



\## Load Balancing Demonstration



To demonstrate traffic distribution, each NGINX pod was configured to return its own pod name.



A temporary BusyBox client sent 30 HTTP requests to:



`http://nginx-service`



The responses contained the names of all 12 backend pods.



\*\*Result:\*\* All 12 backend pods were observed serving requests through the Kubernetes Service.



This demonstrates traffic distribution across multiple service endpoints. It does not imply perfectly equal traffic distribution.



\## Observations



\- All three cluster nodes reached the Ready state.

\- The 12 application pods were distributed evenly: 6 on each worker node.

\- All 12 pods reached Running and Ready status.

\- CPU and memory resource requests and limits were successfully configured.

\- The Service discovered all 12 application endpoints.

\- Repeated HTTP requests through the Service produced responses from all 12 backend pods.

\- Application access through port-forwarding was successful.



\## Screenshots



| Screenshot | Description |

|---|---|

| 01-cluster-nodes.png | Three-node cluster |

| 02-node-metrics.png | CPU and memory monitoring |

| 03-deployment-config.png | Deployment YAML |

| 04-initial-deployment.png | Initial 6 replicas |

| 05-scaled-deployment.png | Scaled 12 replicas |

| 06-pod-distribution.png | Pod distribution |

| 07a-control-plane-allocation.png | Control-plane allocation |

| 07b-worker-allocation.png | Worker 1 allocation |

| 07c-worker2-allocation.png | Worker 2 allocation |

| 08-pod-resource-details.png | Individual pod resource configuration |

| 09-load-balancing-test.png | Traffic distribution test |



\## Cleanup



The following experiment resources were deleted:



\- `lb-test` temporary client pod

\- `nginx-service` NodePort Service

\- `resource-demo` Deployment



The pre-existing `web` Deployment was preserved.



\## Conclusion



The experiment demonstrated Kubernetes resource monitoring, resource allocation, workload scheduling, horizontal scaling, service discovery, and traffic distribution across multiple backend pods in a three-node KIND cluster.

