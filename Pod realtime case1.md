That’s a great idea! You’ve encountered a **real-world issue** with your Jenkins pod getting **evicted due to low ephemeral storage** — a common problem in Kubernetes environments. Here’s a nicely structured **case study/documentation** format you can use in a `.md` file or knowledge base.
---


---

## 📝 Kubernetes Pod Eviction – Case Study: Jenkins Pod

### 📌 **Overview**
In this case, a Jenkins pod deployed in the `prob` namespace was found in `Error` state due to an eviction. The issue was related to **ephemeral storage exhaustion** on the node hosting the pod.

---

### 🔍 **Environment Details**
- **Namespace:** `prob`
- **Pod Name:** `jenkins-pod`
- **Node:** `ip-192-168-1-86`
- **Container Image:** `jenkins/jenkins`
- **Issue Observed On:** `April 5, 2025`

---

### 📟 **Commands Used**

```bash
kubectl get po -n prob
kubectl logs jenkins-pod -n prob
kubectl describe po jenkins-pod -n prob
```

---

### 🚨 **Issue Description**

When attempting to check logs:
```bash
kubectl logs jenkins-pod -n prob
```
The logs could not be retrieved because the pod was **evicted**.

From `kubectl describe`:
```
Reason: Evicted
Message: The node was low on resource: ephemeral-storage.
Threshold quantity: 1080480196, available: 1027408Ki.
Container jenkins-pod was using 4620Ki.
```

---

### 🧠 **Root Cause**
The pod was **evicted** because the **ephemeral storage** usage on the node exceeded the configured threshold. Kubernetes automatically evicts pods in such cases to free up resources.

---

### 🛠️ **Resolution Steps**

1. **Checked node’s ephemeral storage usage:**
   ```bash
   df -h /var/lib/kubelet
   ```

2. **Freed up space** by:
   - Cleaning up old Docker/containerd images:
     ```bash
     sudo crictl rmi --prune
     ```
   - Removing unused logs, files, or temporary data on the node.

3. **Optional (if frequent issue):**  
   Consider setting **resource requests/limits** for ephemeral storage in the pod spec:
   ```yaml
   resources:
     requests:
       ephemeral-storage: "500Mi"
     limits:
       ephemeral-storage: "1Gi"
   ```

4. **Recreated the pod**:
   ```bash
   kubectl delete pod jenkins-pod -n prob
   kubectl apply -f jenkins-pod.yaml  # Or let the Deployment/ReplicaSet recreate it
   ```

---

### ✅ **Best Practices to Avoid in Future**
- Set **ephemeral-storage requests and limits** in pod definitions.
- Monitor node disk usage with **Prometheus + Grafana**.
- Use **taints and tolerations** or **node affinity** to control pod placement.
- Regularly clean up unused resources (images, volumes, etc.)

---

### 📌 **Conclusion**
The pod eviction was caused due to a **lack of ephemeral storage** on the node. By understanding the eviction reason and monitoring resource usage, we can prevent such issues from impacting workloads in the future.

---

Would you like me to help convert this into a Markdown file for download? Or do you want a template you can reuse for other cases too?
