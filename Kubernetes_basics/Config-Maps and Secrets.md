## Kubernetes ConfigMaps and Secrets - In-depth Guide with Labs

---

### 🧩 1. Introduction
Kubernetes applications are often deployed in dynamic environments. To improve application portability and environment management, Kubernetes provides two special objects: **ConfigMaps** and **Secrets**. These allow you to decouple configuration data from container images.

---

### 🧠 2. What are ConfigMaps and Secrets?

- **ConfigMap**: Stores non-sensitive key-value pairs, like environment variables, URLs, or config files.
- **Secret**: Stores sensitive information like passwords, tokens, or keys in base64-encoded format.

---

### 🔍 3. Why Decouple Configuration?

**Benefits:**
- Environment-specific configurations
- No need to rebuild images for config changes
- Improved security (Secrets)
- Clear separation of concerns

---

### 🛠️ 4. Lab: Creating ConfigMaps

#### A. Imperative (Literal Values)
```bash
kubectl create configmap my-config --from-literal=env=dev --from-literal=version=1.0
```

#### B. Declarative (YAML File)
```yaml
# my-config.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-config
  namespace: default
data:
  env: dev
  version: "1.0"
```
```bash
kubectl apply -f my-config.yaml
```

#### C. From File
```bash
kubectl create configmap my-config-file --from-file=config.properties
```

---

### 🧪 5. Lab: Using ConfigMaps in Pods

#### A. As Environment Variables (Single Key)
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-cm-single
spec:
  containers:
    - name: app
      image: busybox
      command: ["/bin/sh", "-c", "env && sleep 3600"]
      env:
        - name: ENVIRONMENT
          valueFrom:
            configMapKeyRef:
              name: my-config
              key: env
```

#### B. As Environment Variables (All Keys)
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-cm-all
spec:
  containers:
    - name: app
      image: busybox
      command: ["/bin/sh", "-c", "env && sleep 3600"]
      envFrom:
        - configMapRef:
            name: my-config
```

#### C. As Volume Mount
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-cm-volume
spec:
  volumes:
    - name: config-vol
      configMap:
        name: my-config-file
  containers:
    - name: app
      image: busybox
      command: ["/bin/sh", "-c", "ls /etc/config && sleep 3600"]
      volumeMounts:
        - name: config-vol
          mountPath: /etc/config
```

---

### 🔐 6. Lab: Creating Secrets

#### A. Imperative
```bash
kubectl create secret generic my-secret --from-literal=password=myS3cret
```

#### B. Declarative (Base64 encoding required)
```bash
echo -n 'myS3cret' | base64  # Output: bXlTM2NyZXQ=
```
```yaml
# my-secret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-secret
type: Opaque
data:
  password: bXlTM2NyZXQ=
```
```bash
kubectl apply -f my-secret.yaml
```

#### C. From File
```bash
echo -n 'mypassword' > ./password.txt
kubectl create secret generic db-password --from-file=password.txt
```

---

### 🔑 7. Lab: Using Secrets in Pods

#### A. As Environment Variable
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-secret-env
spec:
  containers:
    - name: app
      image: busybox
      command: ["/bin/sh", "-c", "env && sleep 3600"]
      env:
        - name: DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: my-secret
              key: password
```

#### B. As Volume Mount
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-secret-volume
spec:
  volumes:
    - name: secret-vol
      secret:
        secretName: db-password
  containers:
    - name: app
      image: busybox
      command: ["/bin/sh", "-c", "cat /etc/secret/password && sleep 3600"]
      volumeMounts:
        - name: secret-vol
          mountPath: /etc/secret
```

---

### 🧹 8. Clean-Up Commands
```bash
kubectl delete configmap my-config my-config-file
kubectl delete secret my-secret db-password
kubectl delete pod pod-cm-single pod-cm-all pod-cm-volume pod-secret-env pod-secret-volume
```

---

### 📝 9. Key Takeaways
- Use **ConfigMaps** for non-sensitive config data.
- Use **Secrets** for sensitive data like passwords or API tokens.
- You can inject both as **environment variables** or **mounted volumes**.
- Avoid hardcoding environment-specific data in Docker images.
- **Declarative YAML** is best practice for production-grade applications.

Would you like me to now add examples for updating or versioning ConfigMaps and Secrets in CI/CD pipelines?

