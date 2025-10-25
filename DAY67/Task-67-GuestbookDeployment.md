# 🌟 Task 67 - Deploy Guestbook Application with Redis and Frontend in Kubernetes

**📌 Task Description**

The **Nautilus Application development team** has developed a guestbook application to manage guest/visitor entries, which is ready for deployment on a Kubernetes cluster. The infrastructure includes a Redis master, Redis slave, and frontend tier, each with specific deployment and service configurations. The task involves creating these components and ensuring the guestbook application is accessible via a NodePort service.

**👉 Task Requirements**:
- **Redis Master Deployment (`redis-master`)**:
  - Replicas: `1`
  - Container:
    - Name: `master-redis-xfusion`
    - Image: `redis`
    - Resources: CPU request `100m`, Memory request `100Mi`
    - Port: `6379` (Redis default)
  - Labels: `app: redis-master` (for selector and template metadata)
- **Redis Master Service (`redis-master`)**:
  - Selector: `app: redis-master`
  - Port: `6379`
  - TargetPort: `6379`
  - Type: ClusterIP (default)
- **Redis Slave Deployment (`redis-slave`)**:
  - Replicas: `2`
  - Container:
    - Name: `slave-redis-xfusion`
    - Image: `gcr.io/google_samples/gb-redisslave:v3`
    - Resources: CPU request `100m`, Memory request `100Mi`
    - Environment variable: `GET_HOSTS_FROM: dns`
    - Port: `6379` (Redis default)
  - Labels: `app: redis-slave` (for selector and template metadata)
- **Redis Slave Service (`redis-slave`)**:
  - Selector: `app: redis-slave`
  - Port: `6379`
  - TargetPort: `6379`
  - Type: ClusterIP (default)
- **Frontend Deployment (`frontend`)**:
  - Replicas: `3`
  - Container:
    - Name: `php-redis-xfusion`
    - Image: `gcr.io/google-samples/gb-frontend@sha256:a908df8486ff66f2c4daa0d3d8a2fa09846a1fc8efd65649c0109695c7c5cbff`
    - Resources: CPU request `100m`, Memory request `100Mi`
    - Environment variable: `GET_HOSTS_FROM: dns`
    - Port: `80`
  - Labels: `app: frontend` (for selector and template metadata)
- **Frontend Service (`frontend`)**:
  - Selector: `app: frontend`
  - Type: `NodePort`
  - Port: `80`
  - TargetPort: `80`
  - NodePort: `30009`
- Verify the guestbook application via the **App** button in the KodeKloud lab interface.
- Use `kubectl` on the jump host, which is pre-configured to interact with the Kubernetes cluster.

**💡 Note**: Infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to the Jump Host

```bash
ssh thor@jump_host
```

**Purpose**: Connect to the jump host where `kubectl` is configured to manage the Kubernetes cluster.

---

## 🔹 Step 2: Create the YAML File

```bash
vi guestbook.yaml
```

**Purpose**: Create a single Kubernetes YAML file to define the Redis master, Redis slave, and frontend deployments and services.

---

## 🔹 Step 3: Add YAML Content

**YAML Content**:

```yaml
# =========================
# REDIS MASTER DEPLOYMENT
# =========================
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-master
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis-master
  template:
    metadata:
      labels:
        app: redis-master
    spec:
      containers:
      - name: master-redis-xfusion
        image: redis
        ports:
        - containerPort: 6379
        resources:
          requests:
            cpu: "100m"
            memory: "100Mi"
---
# =========================
# REDIS MASTER SERVICE
# =========================
apiVersion: v1
kind: Service
metadata:
  name: redis-master
spec:
  selector:
    app: redis-master
  ports:
  - port: 6379
    targetPort: 6379
---
# =========================
# REDIS SLAVE DEPLOYMENT
# =========================
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-slave
spec:
  replicas: 2
  selector:
    matchLabels:
      app: redis-slave
  template:
    metadata:
      labels:
        app: redis-slave
    spec:
      containers:
      - name: slave-redis-xfusion
        image: gcr.io/google_samples/gb-redisslave:v3
        ports:
        - containerPort: 6379
        env:
        - name: GET_HOSTS_FROM
          value: "dns"
        resources:
          requests:
            cpu: "100m"
            memory: "100Mi"
---
# =========================
# REDIS SLAVE SERVICE
# =========================
apiVersion: v1
kind: Service
metadata:
  name: redis-slave
spec:
  selector:
    app: redis-slave
  ports:
  - port: 6379
    targetPort: 6379
---
# =========================
# FRONTEND DEPLOYMENT
# =========================
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
      - name: php-redis-xfusion
        image: gcr.io/google-samples/gb-frontend@sha256:a908df8486ff66f2c4daa0d3d8a2fa09846a1fc8efd65649c0109695c7c5cbff
        ports:
        - containerPort: 80
        env:
        - name: GET_HOSTS_FROM
          value: "dns"
        resources:
          requests:
            cpu: "100m"
            memory: "100Mi"
---
# =========================
# FRONTEND SERVICE (NodePort)
# =========================
apiVersion: v1
kind: Service
metadata:
  name: frontend
spec:
  type: NodePort
  selector:
    app: frontend
  ports:
  - port: 80
    targetPort: 80
    nodePort: 30009
```

**Key Configurations**:
- **Redis Master Deployment (`redis-master`)**:
  - `apiVersion: apps/v1`, `kind: Deployment`: Defines a deployment.
  - `metadata.name: redis-master`: Sets the deployment name.
  - `spec.replicas: 1`: Runs one pod.
  - `spec.selector.matchLabels.app: redis-master`: Matches pods with the label.
  - `spec.template.metadata.labels.app: redis-master`: Labels the pods.
  - `spec.template.spec.containers`:
    - `name: master-redis-xfusion`: Sets the container name.
    - `image: redis`: Uses the default Redis image.
    - `ports.containerPort: 6379`: Exposes Redis’s default port.
    - `resources.requests`: Sets CPU to `100m`, memory to `100Mi`.
- **Redis Master Service (`redis-master`)**:
  - `metadata.name: redis-master`: Sets the service name.
  - `spec.selector.app: redis-master`: Targets pods with the label.
  - `spec.ports`: Maps `port: 6379` to `targetPort: 6379`, protocol `TCP` (default).
  - `spec.type`: ClusterIP (default).
- **Redis Slave Deployment (`redis-slave`)**:
  - `metadata.name: redis-slave`: Sets the deployment name.
  - `spec.replicas: 2`: Runs two pods.
  - `spec.selector.matchLabels.app: redis-slave`: Matches pods with the label.
  - `spec.template.metadata.labels.app: redis-slave`: Labels the pods.
  - `spec.template.spec.containers`:
    - `name: slave-redis-xfusion`: Sets the container name.
    - `image: gcr.io/google_samples/gb-redisslave:v3`: Uses the specified image.
    - `ports.containerPort: 6379`: Exposes Redis’s default port.
    - `env`: Sets `GET_HOSTS_FROM: dns`.
    - `resources.requests`: Sets CPU to `100m`, memory to `100Mi`.
- **Redis Slave Service (`redis-slave`)**:
  - `metadata.name: redis-slave`: Sets the service name.
  - `spec.selector.app: redis-slave`: Targets pods with the label.
  - `spec.ports`: Maps `port: 6379` to `targetPort: 6379`, protocol `TCP` (default).
  - `spec.type`: ClusterIP (default).
- **Frontend Deployment (`frontend`)**:
  - `metadata.name: frontend`: Sets the deployment name.
  - `spec.replicas: 3`: Runs three pods.
  - `spec.selector.matchLabels.app: frontend`: Matches pods with the label.
  - `spec.template.metadata.labels.app: frontend`: Labels the pods.
  - `spec.template.spec.containers`:
    - `name: php-redis-xfusion`: Sets the container name.
    - `image`: Uses the specified SHA256-tagged image.
    - `ports.containerPort: 80`: Exposes HTTP port.
    - `env`: Sets `GET_HOSTS_FROM: dns`.
    - `resources.requests`: Sets CPU to `100m`, memory to `100Mi`.
- **Frontend Service (`frontend`)**:
  - `metadata.name: frontend`: Sets the service name.
  - `spec.type: NodePort`: Exposes the service externally.
  - `spec.selector.app: frontend`: Targets pods with the label.
  - `spec.ports`: Maps `port: 80` to `targetPort: 80`, `nodePort: 30009`.

**Save and Exit**: Press `Esc`, type `:wq`, press `Enter`.

---

## 🔹 Step 4: Verify YAML Syntax

```bash
cat guestbook.yaml
```

**Purpose**: Confirm the YAML file contains the correct configurations.

**Validation Checklist**:
- **Redis Master Deployment**:
  - ✅ `name: redis-master`, `replicas: 1`
  - ✅ Labels: `app: redis-master` in `selector` and `template.metadata`
  - ✅ Container: `name: master-redis-xfusion`, `image: redis`, `port: 6379`
  - ✅ Resources: `cpu: 100m`, `memory: 100Mi`
- **Redis Master Service**:
  - ✅ `name: redis-master`
  - ✅ Selector: `app: redis-master`
  - ✅ Ports: `port: 6379`, `targetPort: 6379`
- **Redis Slave Deployment**:
  - ✅ `name: redis-slave`, `replicas: 2`
  - ✅ Labels: `app: redis-slave` in `selector` and `template.metadata`
  - ✅ Container: `name: slave-redis-xfusion`, `image: gcr.io/google_samples/gb-redisslave:v3`, `port: 6379`
  - ✅ Environment: `GET_HOSTS_FROM: dns`
  - ✅ Resources: `cpu: 100m`, `memory: 100Mi`
- **Redis Slave Service**:
  - ✅ `name: redis-slave`
  - ✅ Selector: `app: redis-slave`
  - ✅ Ports: `port: 6379`, `targetPort: 6379`
- **Frontend Deployment**:
  - ✅ `name: frontend`, `replicas: 3`
  - ✅ Labels: `app: frontend` in `selector` and `template.metadata`
  - ✅ Container: `name: php-redis-xfusion`, `image: gcr.io/google-samples/gb-frontend@sha256:...`, `port: 80`
  - ✅ Environment: `GET_HOSTS_FROM: dns`
  - ✅ Resources: `cpu: 100m`, `memory: 100Mi`
- **Frontend Service**:
  - ✅ `name: frontend`, `type: NodePort`
  - ✅ Selector: `app: frontend`
  - ✅ Ports: `port: 80`, `targetPort: 80`, `nodePort: 30009`
- ✅ Correct YAML indentation (2 spaces) and valid separators (`---`)

---

## 🔹 Step 5: Apply the Configurations

```bash
kubectl apply -f guestbook.yaml
```

**Purpose**: Deploy the Redis master, Redis slave, and frontend deployments and services to the Kubernetes cluster.

**Expected Output**:
```
deployment.apps/redis-master created
service/redis-master created
deployment.apps/redis-slave created
service/redis-slave created
deployment.apps/frontend created
service/frontend created
```

---

## 🔹 Step 6: Verify Deployments and Pods

```bash
kubectl get deploy,pod
```

**Purpose**: Confirm all deployments are running and pods are operational.

**Expected Output**:
```
NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/redis-master    1/1     1            1           30s
deployment.apps/redis-slave     2/2     2            2           30s
deployment.apps/frontend        3/3     3            3           30s

NAME                                    READY   STATUS    RESTARTS   AGE
pod/redis-master-xyz123-abc789          1/1     Running   0          30s
pod/redis-slave-xyz456-def123           1/1     Running   0          30s
pod/redis-slave-xyz789-ghi456           1/1     Running   0          30s
pod/frontend-xyz123-jkl789              1/1     Running   0          30s
pod/frontend-xyz456-mno123              1/1     Running   0          30s
pod/frontend-xyz789-pqr456              1/1     Running   0          30s
```

**Success Indicators**:
- ✅ Deployments: `redis-master` (1/1), `redis-slave` (2/2), `frontend` (3/3) ready
- ✅ Pod status: `Running`, `1/1` ready, no restarts

---

## 🔹 Step 7: Verify Services

```bash
kubectl get svc
```

**Purpose**: Confirm all services are created with correct port mappings.

**Expected Output**:
```
NAME            TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
redis-master    ClusterIP   10.96.123.456   <none>        6379/TCP         30s
redis-slave     ClusterIP   10.96.123.789   <none>        6379/TCP         30s
frontend        NodePort    10.96.123.123   <none>        80:30009/TCP     30s
```

**Success Indicators**:
- ✅ Service names: `redis-master`, `redis-slave`, `frontend`
- ✅ Types: `ClusterIP` for Redis services, `NodePort` for frontend
- ✅ Port mappings: `6379/TCP` for Redis, `80:30009/TCP` for frontend
- ✅ Endpoints populated (verify with `kubectl describe svc`)

---

## 🔹 Step 8: Access Guestbook Application

**Action**: In the KodeKloud lab interface, click the **App** button to access the guestbook application.

**Purpose**: Verify the application is accessible via the NodePort (30009).

**Expected Output** (via browser):
- The guestbook application interface loads successfully, allowing guest entry management.

**Alternative CLI Test** (if no App button is available):
```bash
kubectl get nodes -o wide
```

**Note the node’s external IP** (e.g., `192.168.1.100`), then:
```bash
curl http://<node-ip>:30009
```

**Expected Output**:
- HTML content of the guestbook application’s landing page.

---

## 🔹 Step 9: Verify Deployment Details (Optional)

```bash
kubectl describe deployment redis-master
kubectl describe deployment redis-slave
kubectl describe deployment frontend
```

**Purpose**: Confirm the deployments’ configurations, including images, ports, and environment variables.

**Expected Output (Excerpt for `redis-master`)**:
```
Name:                   redis-master
Pod Template:
  Labels:               app=redis-master
  Containers:
   master-redis-xfusion:
    Image:        redis
    Port:         6379/TCP
    Requests:
      cpu:        100m
      memory:     100Mi
```

**Expected Output (Excerpt for `redis-slave`)**:
```
Name:                   redis-slave
Pod Template:
  Labels:               app=redis-slave
  Containers:
   slave-redis-xfusion:
    Image:        gcr.io/google_samples/gb-redisslave:v3
    Port:         6379/TCP
    Environment:
      GET_HOSTS_FROM:  dns
    Requests:
      cpu:        100m
      memory:     100Mi
```

**Expected Output (Excerpt for `frontend`)**:
```
Name:                   frontend
Pod Template:
  Labels:               app=frontend
  Containers:
   php-redis-xfusion:
    Image:        gcr.io/google-samples/gb-frontend@sha256:a908df...
    Port:         80/TCP
    Environment:
      GET_HOSTS_FROM:  dns
    Requests:
      cpu:        100m
      memory:     100Mi
```

---

## 🔹 Step 10: Check Pod Logs (Optional)

```bash
kubectl logs -l app=redis-master
kubectl logs -l app=redis-slave
kubectl logs -l app=frontend
```

**Purpose**: Verify the containers started correctly.

**Expected Logs** (examples):
- **Redis Master**:
  ```
  1:M 25 Oct 2025 16:00:00.000 * Running mode=standalone, port=6379.
  1:M 25 Oct 2025 16:00:00.000 * Server initialized
  ```
- **Redis Slave**:
  ```
  1:S 25 Oct 2025 16:00:00.000 * Connecting to MASTER redis-master
  1:S 25 Oct 2025 16:00:00.000 * MASTER <-> SLAVE sync started
  ```
- **Frontend**:
  ```
  [25/Oct/2025:16:00:00 +0000] PHP application starting
  ```

---

## 🔹 Step 11: Verify Redis Connectivity (Optional)

```bash
kubectl exec -it $(kubectl get pods -l app=redis-master -o name | head -n 1) -- redis-cli ping
```

**Expected Output**:
```
PONG
```

---

## 🔹 Step 12: Clean Up (Optional)

```bash
kubectl delete -f guestbook.yaml
```

**Purpose**: Remove all deployments and services if testing is complete.

**Expected Output**:
```
deployment.apps "redis-master" deleted
service "redis-master" deleted
deployment.apps "redis-slave" deleted
service "redis-slave" deleted
deployment.apps "frontend" deleted
service "frontend" deleted
```

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **Jump Host**:

```bash
# Connect to jump host
ssh thor@jump_host

# Create YAML file
cat > guestbook.yaml << 'EOF'
# REDIS MASTER DEPLOYMENT
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-master
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis-master
  template:
    metadata:
      labels:
        app: redis-master
    spec:
      containers:
      - name: master-redis-xfusion
        image: redis
        ports:
        - containerPort: 6379
        resources:
          requests:
            cpu: "100m"
            memory: "100Mi"
---
# REDIS MASTER SERVICE
apiVersion: v1
kind: Service
metadata:
  name: redis-master
spec:
  selector:
    app: redis-master
  ports:
  - port: 6379
    targetPort: 6379
---
# REDIS SLAVE DEPLOYMENT
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-slave
spec:
  replicas: 2
  selector:
    matchLabels:
      app: redis-slave
  template:
    metadata:
      labels:
        app: redis-slave
    spec:
      containers:
      - name: slave-redis-xfusion
        image: gcr.io/google_samples/gb-redisslave:v3
        ports:
        - containerPort: 6379
        env:
        - name: GET_HOSTS_FROM
          value: "dns"
        resources:
          requests:
            cpu: "100m"
            memory: "100Mi"
---
# REDIS SLAVE SERVICE
apiVersion: v1
kind: Service
metadata:
  name: redis-slave
spec:
  selector:
    app: redis-slave
  ports:
  - port: 6379
    targetPort: 6379
---
# FRONTEND DEPLOYMENT
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
      - name: php-redis-xfusion
        image: gcr.io/google-samples/gb-frontend@sha256:a908df8486ff66f2c4daa0d3d8a2fa09846a1fc8efd65649c0109695c7c5cbff
        ports:
        - containerPort: 80
        env:
        - name: GET_HOSTS_FROM
          value: "dns"
        resources:
          requests:
            cpu: "100m"
            memory: "100Mi"
---
# FRONTEND SERVICE (NodePort)
apiVersion: v1
kind: Service
metadata:
  name: frontend
spec:
  type: NodePort
  selector:
    app: frontend
  ports:
  - port: 80
    targetPort: 80
    nodePort: 30009
EOF

# Deploy and verify
kubectl apply -f guestbook.yaml
kubectl get deploy,pod,svc
```

---

## 💡 Common Kubernetes Deployment/Service Issues & Fixes

### **Issue 1: Pods Not Running**
**Problem**: `kubectl get pods` shows `Error` or `CrashLoopBackOff`.
**Fix**: Check logs and events.
```bash
kubectl logs -l app=redis-master
kubectl logs -l app=redis-slave
kubectl logs -l app=frontend
kubectl describe pod -l app=redis-master
```

### **Issue 2: Image Pull Failure**
**Problem**: Pods in `ErrImagePull` or `ImagePullBackOff`.
**Fix**: Verify image names and tags.
```bash
docker pull redis
docker pull gcr.io/google_samples/gb-redisslave:v3
docker pull gcr.io/google-samples/gb-frontend@sha256:a908df8486ff66f2c4daa0d3d8a2fa09846a1fc8efd65649c0109695c7c5cbff
kubectl rollout restart deployment redis-master
kubectl rollout restart deployment redis-slave
kubectl rollout restart deployment frontend
```

### **Issue 3: Service Not Accessible**
**Problem**: Guestbook app not accessible via `<node-ip>:30009`.
**Fix**: Verify service configuration and endpoints.
```bash
kubectl describe svc frontend
kubectl get endpoints frontend
```

### **Issue 4: DNS Resolution Failure**
**Problem**: Redis slave or frontend fails due to `GET_HOSTS_FROM: dns`.
**Fix**: Ensure DNS is configured and services are resolvable.
```bash
kubectl exec -it $(kubectl get pods -l app=frontend -o name | head -n 1) -- nslookup redis-master
kubectl exec -it $(kubectl get pods -l app=redis-slave -o name | head -n 1) -- nslookup redis-master
```

---

## 🔧 Troubleshooting Failures

### **Error: Pod Not Running**
**Symptoms**: `kubectl get pods` shows `Error` or `CrashLoopBackOff`.
**Solution**: Check logs and events for specific errors.
```bash
kubectl logs -l app=frontend
kubectl get events --field-selector involvedObject.name=frontend
```

### **Error: Service Not Routing**
**Symptoms**: Accessing `<node-ip>:30009` fails.
**Solution**: Verify service selector and endpoints.
```bash
kubectl describe svc frontend
kubectl get endpoints frontend
```

### **Error: YAML Syntax Error**
**Symptoms**: `kubectl apply` fails with parsing error.
**Solution**: Validate YAML syntax.
```bash
kubectl apply -f guestbook.yaml --dry-run=client
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was deploying a multi-tier guestbook application with Redis master, Redis slave, and frontend components, ensuring correct image usage, resource requests, environment variables, and service exposure via NodePort.

**💡 Solution Approach**:
1. **Redis Master**: Configured `redis-master` deployment and service with `redis` image and port `6379`.
2. **Redis Slave**: Configured `redis-slave` with 2 replicas, `gb-redisslave:v3` image, and `GET_HOSTS_FROM: dns`.
3. **Frontend**: Configured `frontend` with 3 replicas, SHA256-tagged image, and NodePort `30009`.
4. **Verification**: Confirmed all deployments and services are running and the guestbook app is accessible.

**🎯 Key Success Factors**:
- **Correct Configurations**: Used exact images and ports as specified.
- **DNS Integration**: Set `GET_HOSTS_FROM: dns` for service discovery.
- **Resource Requests**: Applied CPU and memory requests for stability.
- **Verification**: Ensured all pods are `Running` and services have endpoints.
- **Testing**: Confirmed guestbook functionality via the App button.

**⚠️ Critical Fixes**:
- Ensure exact image names, especially the SHA256-tagged frontend image.
- Match selector labels across deployments and services.
- Use `nodePort: 30009` within valid range (30000-32767).
- Verify DNS resolution for `GET_HOSTS_FROM`.

**🔒 Kubernetes Best Practices Applied**:
- **Label Consistency**: Used distinct labels for each tier (`redis-master`, `redis-slave`, `frontend`).
- **Resource Requests**: Specified CPU and memory for predictable scheduling.
- **Service Exposure**: Used NodePort for testing, with clear port mappings.
- **Verification**: Checked pod status, service endpoints, and application access.

**⚠️ Important Troubleshooting Concepts**:
- **Service Endpoints**: Check `kubectl get endpoints` for routing issues.
- **Pod Logs**: Use `kubectl logs` to diagnose startup failures.
- **Events**: Use `kubectl get events` to identify deployment or service issues.
- **DNS Resolution**: Verify `nslookup` for service discovery.

---

## ⚠️ Important Production Notes

🔧 **Deployment Debugging**: Use `kubectl describe`, `kubectl logs`, and `kubectl exec` for issue resolution.
🔐 **Image Security**: Pin specific image versions in production to avoid updates.
📊 **Resource Limits**: Add CPU/memory limits to prevent resource overuse.
🛡️ **Service Exposure**: Use Ingress or LoadBalancer for frontend in production instead of NodePort.
🗄️ **Persistent Storage**: Add PersistentVolumes for Redis data in production.

*Last Updated: October 25, 2025*