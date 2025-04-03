### **What is a Kubernetes Pod?**  
A **Pod** is the smallest deployable unit in Kubernetes. It represents a **single instance of a running process** in the cluster. Pods can contain **one or more containers** that share the same **network and storage**.
- In K8s cluster, pod repersent a running process
- In K8s cluster, for communication between two pod it needs either IP address or DNS name
- Every pod as unique IP address
- Best practices, for one pod maintain one container
- POD always runs on Slave nodes in the K8s cluster
---

## **Components of a Pod**
A **Pod** consists of the following key components:

### **1. Containers (Main Component)**
- The **actual application** runs inside one or more containers in a pod.
- first container is main and remaining containers are support/Helper/Side car containers
- Containers in a pod **share networking and storage**.
- Example: A pod running an **Nginx web server** container.

### **2. Storage (Volumes)**
- **Persistent storage** shared between containers in the same pod.
- Can be:
  - **EmptyDir** (temporary storage)
  - **PersistentVolumeClaim** (for persistent storage)
  - **ConfigMap / Secret** (for configuration files and credentials)

### **3. Networking**
- Each pod gets a **unique IP address**.
- Containers inside a pod **communicate using `localhost`**.
- Pods talk to each other through **ClusterIP Services**.

### **4. Init Containers (Optional)**
- Special containers that **run before the main container starts**.
- Used for setup tasks like **fetching configs, database migrations, or setting permissions**.

### **5. Pod Metadata**
- **Labels & Selectors**: Used for identifying and managing pods.
- **Annotations**: Store additional information (e.g., monitoring data).

### **6. Pod Lifecycle & Restart Policy**
- Pods follow a lifecycle:
  - **Pending → Running → Succeeded/Failed**
- The **restart policy** (`Always`, `OnFailure`, `Never`) defines how Kubernetes restarts containers inside a pod.
### **7. pod commands**
--sh 
kubectl get pod --all-namespaces
--
- it display all running pods in all namespaces

--sh
kubectl run nginx-demo --image=nginx --port=80 --namespace=prob
--
-it is used to create a pod in the desired namespace
**--sh**
kubectl get pod -n <namespace>
**--sh**
kubectl get pod -n <namespace> -o wide
**--sh**
kubectl describe po nginx-demo
**--sh**
kubectl logs nginx-demo
**--sh**
kubectl logs -f nginx-demo
---

## **Example Pod YAML**
Here’s a simple pod definition with an **Nginx container**:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-nginx
  labels:
    app: web
spec:
  containers:
    - name: nginx-container
      image: nginx:latest
      ports:
        - containerPort: 80
```

Would you like more details on any of these components? 🚀
