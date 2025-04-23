## Multi-Container Pods: Easy Guide with Examples

### What Are Multi-Container Pods?
In Kubernetes, a Pod is like a small box that holds one or more containers (which are like lightweight apps). Normally, each Pod runs just one container, but sometimes it makes sense to run more than one in the same Pod. This is called a **multi-container Pod**.

In a multi-container Pod:
- All containers live on the same machine.
- They can talk to each other using `localhost`.
- They can share the same storage.

> **Why use this?** When containers need to work closely together or share information, it's easier to put them in the same Pod.

---

### When Should You Use Multi-Container Pods?
**Good times to use them:**
- Two containers need to share files or logs.
- You want to use a helper app (like something that forwards logs or connects to a database).

**When not to use them:**
- If the containers don't need to talk to each other.
- If you want them to run on different machines.

---

## How to Make a Multi-Container Pod
You can't use simple `kubectl run` commands to create multi-container Pods. You need to write a YAML file.

### Sample YAML File
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-multi-pod
spec:
  containers:
  - name: web
    image: nginx
  - name: helper
    image: busybox
    command: ["sh", "-c"]
    args: ["while true; do echo hello; sleep 30; done"]
```

### Lab Activities
1. **Run a single-container Pod:**
```bash
kubectl run single-nginx --image=nginx
```
2. **Run the above YAML file:**
```bash
echo '<YAML code above>' > my-multi-pod.yaml
kubectl apply -f my-multi-pod.yaml
```
3. **View container logs:**
```bash
kubectl logs my-multi-pod -c web
kubectl logs my-multi-pod -c helper
```
4. **Enter a container:**
```bash
kubectl exec -ti my-multi-pod -c web -- /bin/bash
```

---

## Sharing Storage in Pods

### Using `emptyDir` (Temporary Storage)
```yaml
volumes:
- name: shared
  emptyDir: {}
```
Use it in containers like this:
```yaml
volumeMounts:
- mountPath: /data
  name: shared
```

### Using `hostPath` (From Host Machine)
```yaml
volumes:
- name: host-vol
  hostPath:
    path: /tmp
```

> **Note:** Only use `hostPath` if you're sure. It links the Pod to a specific machine.

---

## Popular Pod Design Patterns

### 1. **Ambassador Pattern**
Helps a container connect to an outside service, like a database.

**Lab:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: ambassador-demo
spec:
  containers:
  - name: web
    image: nginx
  - name: mysql-ambassador
    image: bitnami/mysql-proxy
    env:
    - name: MYSQL_PROXY_BACKEND_HOST
      value: "mysql.example.com"
    - name: MYSQL_PROXY_BACKEND_PORT
      value: "3306"
```
```bash
kubectl apply -f ambassador-demo.yaml
kubectl exec -ti ambassador-demo -c web -- /bin/bash
```

### 2. **Adapter Pattern**
Used when a container needs to change data format. For example, changing Apache logs into JSON.

**Lab:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: adapter-demo
spec:
  volumes:
  - name: log-vol
    emptyDir: {}
  containers:
  - name: nginx
    image: nginx
    volumeMounts:
    - name: log-vol
      mountPath: /var/log/nginx
  - name: adapter
    image: busybox
    command: ["sh", "-c"]
    args:
    - "while true; do cat /var/log/nginx/access.log | sed 's/^/{\"log\": \"/;s/$/\"}/'; sleep 5; done"
    volumeMounts:
    - name: log-vol
      mountPath: /var/log/nginx
```
```bash
kubectl apply -f adapter-demo.yaml
kubectl logs adapter-demo -c adapter
```

---

## Practice Lab: Logs with Sidecar

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: log-example
spec:
  containers:
  - name: app
    image: nginx
    volumeMounts:
    - mountPath: /var/log/nginx
      name: logvol
  - name: logger
    image: busybox
    command: ["sh", "-c"]
    args: ["tail -f /var/log/nginx/access.log"]
    volumeMounts:
    - mountPath: /var/log/nginx
      name: logvol
  volumes:
  - name: logvol
    emptyDir: {}
```

```bash
kubectl apply -f log-example.yaml
kubectl logs log-example -c logger
```

---

## Comparing Ways to Create Pods

| Using Command Line (`kubectl run`) | Writing YAML Files |
|------------------------------------|---------------------|
| Fast and simple                    | More flexible       |
| Best for tests                     | Best for production |
| Can't make multi-container Pods    | Can do everything   |

---

## Cleaning Up
```bash
kubectl delete pod my-multi-pod
kubectl delete -f log-example.yaml
kubectl delete -f ambassador-demo.yaml
kubectl delete -f adapter-demo.yaml
```

---
