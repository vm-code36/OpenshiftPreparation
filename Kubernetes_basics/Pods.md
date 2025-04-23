## 🧠 In-Depth Notes on Kubernetes Pods, Jobs, and CronJobs

### 📦 What is a Pod?
- A **Pod** is the smallest deployable unit in Kubernetes.
- It encapsulates **one or more containers**, co-located on the same node and sharing:
  - Network namespace (i.e., can reach each other via `localhost`)
  - Storage volumes (via shared volumes)

### 🚫 You Never Deploy Containers Directly
- You **never launch Docker containers directly** in Kubernetes.
- Always create **Pods**, which are then transformed into containers.

### 🧱 Why Use Pods?
- Designed for inter-process communication at scale.
- Helps in managing **multi-container microservices**.
- Better than using Docker networking directly for complex applications.

### 🔀 Key Pod Characteristics
- **Single Node Scope**: A Pod never spans multiple nodes.
- **Stateless Design**: Pods are meant to be ephemeral and stateless.
- **IP Assignment**: Each Pod gets a unique IP for communication across the cluster.

### 🖼 Example: WordPress on Kubernetes
- Needs NGINX + PHP-FPM + MySQL
- NGINX and PHP-FPM should be **in the same Pod** for shared filesystem and localhost communication.
- MySQL should be **in a separate Pod** for decoupled, durable data storage.

---

## 🧱 Single vs Multi-Container Pods

### Why Have Multiple Containers in a Pod?
- Use cases:
  - **Helper/Sidecar containers** (e.g., logging, data sync)
  - **Ambassador containers** (proxy between networks)
  - **Adapter containers** (convert output formats)
- All containers:
  - Share the same **IP address**, **port space**, and **volumes**
  - Can communicate via `localhost`

### Benefits of Multi-Container Pods
- Closely coupled components can easily communicate.
- Share filesystem and configs.
- Simplifies design of tightly integrated microservices.

### Benefits of Single-Container Pods
- Better **isolation**.
- Easier **scaling** of individual services.
- Simpler **resource limits** and monitoring.

---

## 🧪 Lab Exercise 8: Multi-Container Pod (Imperative) ❌ Not Possible
Imperative method **does not support** creating Pods with multiple containers directly.
> Use declarative syntax (YAML) instead.

## 🧪 Lab Exercise 9: Multi-Container Pod (Declarative)
```yaml
# multi-container-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: multi-container-pod
spec:
  containers:
  - name: nginx
    image: nginx
  - name: sidecar
    image: busybox
    command: ["/bin/sh", "-c", "while true; do echo Hello from sidecar; sleep 10; done"]
```
```bash
kubectl apply -f multi-container-pod.yaml
kubectl get pods
kubectl logs multi-container-pod -c sidecar
```

---

## 🧪 Lab Exercise 1: Create a Single-Container Pod (NGINX)
```bash
kubectl run nginx-pod --image=nginx:latest --restart=Never
kubectl get pods
kubectl describe pod nginx-pod
```

## 🧪 Lab Exercise 2: Create the Same Pod Declaratively
```yaml
# nginx-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
  - name: nginx
    image: nginx:latest
```
```bash
kubectl apply -f nginx-pod.yaml
```

## 🧪 Lab Exercise 3: Port Forward and Access NGINX
```bash
kubectl port-forward pod/nginx-pod 8080:80
# Visit http://localhost:8080
```

## 🧪 Lab Exercise 4: Add and Query Labels
```bash
kubectl label pod nginx-pod app=web tier=frontend
kubectl get pods --show-labels
kubectl get pods -l app=web
```

## 🧪 Lab Exercise 5: Add an Annotation
```yaml
# annotated-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  annotations:
    support-email: "support@example.com"
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx
```
```bash
kubectl apply -f annotated-pod.yaml
```

## 🧪 Lab Exercise 6: Launch a Job
```yaml
# hello-job.yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: hello-job
spec:
  template:
    spec:
      restartPolicy: OnFailure
      containers:
      - name: hello
        image: busybox
        command: ["/bin/sh", "-c"]
        args: ["echo Hello World"]
```
```bash
kubectl apply -f hello-job.yaml
kubectl get jobs
kubectl get pods
kubectl logs <job-pod-name>
```

## 🧪 Lab Exercise 7: Launch a CronJob
```yaml
# hello-cronjob.yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: hello-cronjob
spec:
  schedule: "*/1 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          restartPolicy: OnFailure
          containers:
          - name: hello
            image: busybox
            command: ["/bin/sh", "-c"]
            args: ["echo Hello World"]
```
```bash
kubectl apply -f hello-cronjob.yaml
kubectl get cronjobs
kubectl get jobs
kubectl get pods
```

---

### 🛠 Creating Pods
- **Imperative syntax**:
  ```bash
  kubectl run nginx-pod --image=nginx:latest
  ```
  - 🔹 *Quick testing or temporary pods*
  - 🔸 *Not suitable for multi-container Pods*

- **Declarative syntax**:
  ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    name: nginx-pod
  spec:
    containers:
    - name: nginx
      image: nginx:latest
  ```
  ```bash
  kubectl apply -f nginx-pod.yaml
  ```
  - 🔹 *Version-controlled, reproducible*
  - 🔹 *Supports complex specs like multi-container, volume mounts, probes*

### 🔍 Inspecting Pods
- List Pods:
  ```bash
  kubectl get pods
  ```
- Describe a Pod:
  ```bash
  kubectl describe pod nginx-pod
  ```
- Output YAML/JSON:
  ```bash
  kubectl get pod nginx-pod -o yaml
  ```
- Show Pod's node:
  ```bash
  kubectl get pod -o wide
  ```

### 🌐 Accessing a Pod
- Forward Pod port to local system:
  ```bash
  kubectl port-forward pod/nginx-pod 8080:80
  ```

### 🧭 Executing into a Pod's Container
```bash
kubectl exec -ti nginx-pod -- /bin/bash
```

### 🗑 Deleting a Pod
```bash
kubectl delete pod nginx-pod
# or declaratively:
kubectl delete -f nginx-pod.yaml
```

---



