In a Kubernetes (K8s) cluster, the number of **master nodes** (also called **control plane nodes**) depends on the desired **availability and fault tolerance**:

### 1. **Single-Master Cluster**
- **1 master node**
- Suitable for **development, testing**, or small non-critical environments
- If the master node fails, the cluster becomes unmanageable (but workloads may still run)

### 2. **Highly Available (HA) Cluster**
- **3, 5, or more master nodes** (always an **odd number**)
- Ensures **quorum** in etcd (the distributed key-value store used by K8s)
- Common setup is **3 master nodes**, which tolerates 1 node failure

### etcd Quorum Rule (Very Important):
- You need a **majority (n/2 + 1)** of etcd nodes available to maintain quorum
  - For 3 masters → quorum = 2
  - For 5 masters → quorum = 3

So, in summary:

| Use Case | # of Masters | Notes |
|----------|---------------|-------|
| Dev/Test | 1 | No HA, single point of failure |
| Production (HA) | 3 | Recommended minimum for HA |
| Large Scale/Extra HA | 5 or 7 | Higher fault tolerance |

Let me know if you're planning a cluster and I can help you decide the best setup.

Great question! Let's break down **how 3 master nodes work in a Kubernetes (K8s) HA cluster**, how the **workflow** operates, and what happens when switching between masters or in failure scenarios.

---

## 🔧 **Overview: 3 Master Nodes in Kubernetes**
In a 3-master setup, each node runs **control plane components**:

- `kube-apiserver`
- `etcd`
- `controller-manager`
- `scheduler`
- (optionally) `kubelet` and `kube-proxy` (if they are also worker nodes)

All master nodes are **active** (except `controller-manager` and `scheduler`, which use leader election to avoid conflicts).

---

## 📥 **Cluster Components Communication**

### 1. **kube-apiserver (Stateless & Load Balanced)**
- All API requests from kubectl, dashboards, or components go through `kube-apiserver`.
- These are **stateless** – multiple instances can run, usually behind a **load balancer** (e.g., HAProxy, NGINX, or cloud LB).

### 2. **etcd (Quorum Required)**
- Stores the entire state of the cluster.
- In a 3-node etcd setup (1 per master), **2 nodes must be healthy** for the cluster to function.
- If **1 fails**, cluster still works. If **2 fail**, the cluster is **down**.

### 3. **controller-manager & scheduler**
- These are **active on one node only**, chosen through **leader election**.
- If the leader node goes down, a new leader is elected among the remaining two.

---

## 🔁 **Workflow & Master Switching Scenarios**

### 📌 **Normal Operation**
- API requests are load-balanced to any of the 3 kube-apiservers.
- Requests are served as long as quorum (2/3 etcd nodes) is maintained.
- Only one controller-manager and scheduler are active (others are passive).

### 💥 **Failure Scenario: 1 Master Node Down**
- etcd quorum still maintained (2/3), so cluster functions normally.
- If the down node was the **leader**, the controller-manager/scheduler leadership is transferred.
- No manual intervention needed if cluster is properly set up.

### 💀 **Failure Scenario: 2 Master Nodes Down**
- etcd quorum is **lost** → cluster becomes **unavailable** (no control plane access).
- Workloads may continue running (if nodes are up), but **no new scheduling, scaling, or API interaction** is possible.
- Recovery: Bring back at least 1 master to restore quorum.

---

## 🔁 **Master Switching / Failover Process**

1. **APIServer**:
   - Always accessed via **Load Balancer** (VIP or cloud LB)
   - You don’t switch manually; LB routes to healthy apiservers

2. **etcd**:
   - No manual switching unless troubleshooting
   - Failover handled internally using **Raft consensus**

3. **controller-manager & scheduler**:
   - Automatic **leader election** (using `endpoints` in kube-system namespace)
   - No manual action needed unless debugging an issue

---

## ✅ Best Practices

- Always deploy kube-apiservers behind a **highly available load balancer**
- Monitor etcd health using `etcdctl endpoint status`
- Avoid manual etcd member deletion unless necessary
- Use tools like **kubeadm**, **kOps**, or **RKE** for easier HA setup
- Enable **Pod Disruption Budgets (PDB)** and **node affinity** for resilience

---

Let me know if you want a visual diagram of this setup or specific commands for health checks and failover troubleshooting.
