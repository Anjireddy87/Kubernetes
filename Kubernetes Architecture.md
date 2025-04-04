## **🔹 Kubernetes Architecture Overview**  

Kubernetes (**K8s**) follows a **Master-Worker Node** architecture. It is designed for automating deployment, scaling, and management of containerized applications.  

---

### **🔹 1. Control Plane (Master Node)**
The **Control Plane** manages the entire cluster. It consists of multiple components that maintain the desired state, schedule workloads, and manage networking.  

#### **🛠 Key Control Plane Components:**
| Component      | Description |
|---------------|------------|
| **API Server (`kube-apiserver`)** | The **entry point** for all K8s operations. It validates and processes API requests. |
| **Scheduler (`kube-scheduler`)** | Assigns new Pods to available Nodes based on resource availability & constraints. |
| **Controller Manager (`kube-controller-manager`)** | Runs controllers that maintain cluster state (e.g., ReplicaSet, Node Controller). |
| **ETCD** | A distributed **key-value store** that holds all cluster state information. |

---

### **🔹 2. Worker Nodes**
The **Worker Nodes** run application workloads (containers). Each node communicates with the Control Plane.

#### **🛠 Key Worker Node Components:**
| Component      | Description |
|---------------|------------|
| **Kubelet** | Talks to the API Server & ensures containers run as expected. |
| **Container Runtime** | Runs the actual containers (Docker, containerd, CRI-O, etc.). |
| **Kube Proxy** | Handles networking (IP tables, load balancing, forwarding traffic). |

---

### **🔹 3. K8s Cluster Workflow**
1️⃣ **User requests a deployment (via `kubectl` or API).**  
2️⃣ **API Server (`kube-apiserver`) validates & stores it in ETCD.**  
3️⃣ **Scheduler assigns the Pod to a Worker Node.**  
4️⃣ **Kubelet pulls the container image & starts it via the container runtime.**  
5️⃣ **Kube Proxy manages service discovery & networking.**  

---

### **🔹 4. K8s High-Level Diagram**
```
+-------------------------------------------------------+
|                **Control Plane (Master Node)**        |
|                                                       |
|  [ API Server ] -- [ Scheduler ] -- [ Controller Mgr ]|
|         |                                              |
|     [ ETCD (Cluster State) ]                          |
+-------------------------------------------------------+
                 |       |       |
     +-----------+       |       +-----------+
     |                   |                   |
+----v----+       +------v------+       +----v----+
| Worker  |       | Worker Node |       | Worker  |
| Node 1  |       |  Node 2     |       | Node 3  |
|         |       |             |       |         |
| [Kubelet]       |  [Kubelet]   |       | [Kubelet] |
| [ContainerD]    |  [Docker]    |       | [CRI-O] |
| [Kube Proxy]    |  [Kube Proxy]|       | [Kube Proxy] |
+----------------+  +------------+  +-----------------+
```

---

### **🔹 5. K8s Key Objects**
| Object | Description |
|--------|------------|
| **Pod** | Smallest deployable unit, runs containers. |
| **Deployment** | Manages replicas of Pods for scalability. |
| **ReplicaSet** | Ensures a set number of Pods are running. |
| **Service** | Exposes Pods to the network (ClusterIP, NodePort, LoadBalancer). |
| **ConfigMap/Secret** | Stores config data & sensitive info. |
| **Ingress** | Manages external access to Services (HTTP/HTTPS). |

---

### **🔹 6. K8s Networking & Service Discovery**
- **Pod-to-Pod Communication** → Handled by Kubernetes networking (CNI plugins like Calico, Flannel).  
- **Service Discovery** → Services expose Pods internally via ClusterIP.  
- **External Access** → `Ingress` or `LoadBalancer` services are used.  

---

### **💡 Summary**
✅ **Master Node** (Control Plane) manages cluster state.  
✅ **Worker Nodes** run the applications (Pods, Containers).  
✅ **API Server & ETCD** store and control cluster state.  
✅ **Scheduler assigns workloads**, while **Controllers manage state**.  
✅ **Kubelet ensures Pods are running**, and **Kube Proxy handles networking**.  

Let me know if you need a **deeper dive** into any component! 🚀🔥
