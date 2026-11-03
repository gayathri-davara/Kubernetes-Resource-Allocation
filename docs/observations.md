\# Experiment Observations



\## 1. Cluster Configuration



| Component | Configuration |

|---|---|

| Cluster type | KIND |

| Control-plane nodes | 1 |

| Worker nodes | 2 |

| Total nodes | 3 |

| Kubernetes version | v1.37.0 |



All three nodes were in the Ready state.



\## 2. Pod Scheduling



| Stage | Replicas | Worker 1 | Worker 2 |

|---|---:|---:|---:|

| Initial deployment | 6 | 3 | 3 |

| Scaled deployment | 12 | 6 | 6 |



Application pods were scheduled on worker nodes. The control-plane node had a NoSchedule taint.



\## 3. Resource Configuration Per Pod



| Resource | Request | Limit |

|---|---:|---:|

| CPU | 100m | 200m |

| Memory | 64Mi | 128Mi |



\### Aggregate Resource Configuration



| Replicas | CPU Requests | Memory Requests | CPU Limits | Memory Limits |

|---|---:|---:|---:|---:|

| 6 | 600m | 384Mi | 1200m | 768Mi |

| 12 | 1200m | 768Mi | 2400m | 1536Mi |



These are configured resource requests and limits, not measured actual consumption.



\## 4. Node Resource Allocation at 12 Replicas



| Node | CPU Requests | CPU Limits | Memory Requests | Memory Limits |

|---|---:|---:|---:|---:|

| Control-plane | 950m | 0 | 290Mi | 340Mi |

| Worker 1 | 800m | 1200m | 634Mi | 768Mi |

| Worker 2 | 700m | 1200m | 434Mi | 768Mi |



Node allocation figures include system workloads and other pods, not just resource-demo.



\## 5. Load Balancing Test



\- Backend replicas: 12

\- HTTP requests sent: 30

\- Client: BusyBox pod

\- Service: nginx-service

\- Backend identities observed: 12 different pod names

\- Failed requests visible in the captured output: 0



All 12 backend pods were observed responding through the Kubernetes Service. The test demonstrates distribution across multiple endpoints, not equal distribution.



\## 6. Application Connectivity



Direct access to the KIND node IP from Windows timed out because the KIND node network was not directly exposed.



Application access was successfully verified using Kubernetes port-forwarding at `http://localhost:8080`.



\## 7. Final Result



The experiment demonstrated three-node cluster inspection, resource monitoring, resource allocation, pod scheduling, horizontal scaling, service endpoint discovery, application connectivity, and traffic distribution across multiple NGINX replicas.

