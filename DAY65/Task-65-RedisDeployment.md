# 🌟 Task 65 - Deploy Redis with ConfigMap in Kubernetes

**📌 Task Description**

The **Nautilus application development team** has identified performance issues with an application and decided to deploy Redis as an in-memory caching utility for testing in a Kubernetes cluster. This task involves creating a ConfigMap for Redis configuration and a deployment with specific volume mounts, resource requests, and port settings to ensure Redis is up and running.

**👉 Task Requirements**:
- Create a ConfigMap named `my-redis-config`:
  - Key: `redis-config`
  - Value: `maxmemory 2mb`
- Create a deployment named `redis-deployment`:
  - Replicas: `1`
  - Container:
    - Name: `redis-container`
    - Image: `redis:alpine`
    - CPU request: `1`
    - Port: `6379`
  - Volume mounts:
    - Name: `data`, mount path: `/redis-master-data`, type: `emptyDir`
    - Name: `redis-config`, mount path: `/redis-master`, type: ConfigMap (`my-redis-config`)
  - Labels: `app: redis` (for selector and template metadata)
- Ensure the deployment is in an up and running state.
- Use `kubectl` on the jump host, which is pre-configured to interact with the Kubernetes cluster.

**💡 Note**: Infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to the Jump Host

```bash
ssh thor@jump_host
```

**Purpose**: Connect to the jump host where `kubectl` is configured to manage the Kubernetes cluster.

---

## 🔹 Step 2: Create the ConfigMap YAML File

```bash
vi ConfigMap.yaml
```

**Purpose**: Create a YAML file to define the `my-redis-config` ConfigMap with the specified Redis configuration.

---

## 🔹 Step 3: Add ConfigMap YAML Content

**ConfigMap YAML**:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-redis-config
data:
  redis-config: |
    maxmemory 2mb
```

**Key Configurations**:
- **apiVersion: v1**, **kind: ConfigMap**: Defines a ConfigMap resource.
- **metadata.name: my-redis-config**: Sets the ConfigMap name.
- **data.redis-config**: Specifies `maxmemory 2mb` for Redis memory configuration.

**Save and Exit**: Press `Esc`, type `:wq`, press `Enter`.

---

## 🔹 Step 4: Create the Deployment YAML File

```bash
vi redis-deployment.yaml
```

**Purpose**: Create a YAML file to define the `redis-deployment` with the specified container, volumes, and resource settings.

---

## 🔹 Step 5: Add Deployment YAML Content

**Deployment YAML**:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
      - name: redis-container
        image: redis:alpine
        ports:
        - containerPort: 6379
        resources:
          requests:
            cpu: "1"
        volumeMounts:
        - name: data
          mountPath: /redis-master-data
        - name: redis-config
          mountPath: /redis-master
      volumes:
      - name: data
        emptyDir: {}
      - name: redis-config
        configMap:
          name: my-redis-config
```

**Key Configurations**:
- **apiVersion: apps/v1**, **kind: Deployment**: Defines a deployment.
- **metadata.name: redis-deployment**: Sets the deployment name.
- **spec.replicas: 1**: Runs one pod.
- **spec.selector.matchLabels.app: redis**: Matches pods with the label.
- **spec.template.metadata.labels.app: redis**: Labels the pods.
- **spec.template.spec.containers**:
  - **name: redis-container**: Sets the container name.
  - **image: redis:alpine**: Uses the specified image.
  - **ports.containerPort: 6379**: Exposes Redis’s default port.
  - **resources.requests.cpu: "1"**: Requests 1 CPU.
  - **volumeMounts**:
    - **data** at `/redis-master-data`.
    - **redis-config** at `/redis-master`.
- **spec.template.spec.volumes**:
  - **data**: Uses `emptyDir` for temporary storage.
  - **redis-config**: Links to the `my-redis-config` ConfigMap.

**Save and Exit**: Press `Esc`, type `:wq`, press `Enter`.

---

## 🔹 Step 6: Verify YAML Syntax

```bash
cat ConfigMap.yaml
cat redis-deployment.yaml
```

**Purpose**: Confirm both YAML files contain the correct configurations.

**Validation Checklist**:
- **ConfigMap**:
  - ✅ `name: my-redis-config`
  - ✅ `data.redis-config: maxmemory 2mb`
- **Deployment**:
  - ✅ `name: redis-deployment`
  - ✅ Replicas: `1`
  - ✅ Labels: `app: redis` in `selector` and `template.metadata`
  - ✅ Container: `name: redis-container`, `image: redis:alpine`
  - ✅ Port: `6379`
  - ✅ Resources: `cpu: "1"`
  - ✅ Volume mounts: `data` at `/redis-master-data`, `redis-config` at `/redis-master`
  - ✅ Volumes: `data` as `emptyDir`, `redis-config` from ConfigMap `my-redis-config`
- ✅ Correct YAML indentation (2 spaces)

---

## 🔹 Step 7: Apply the Configurations

```bash
kubectl apply -f ConfigMap.yaml
kubectl apply -f redis-deployment.yaml
```

**Purpose**: Deploy the ConfigMap and Redis deployment to the Kubernetes cluster.

**Expected Output**:
```
configmap/my-redis-config created
deployment.apps/redis-deployment created
```

---

## 🔹 Step 8: Verify ConfigMap

```bash
kubectl get configmap
kubectl describe configmap my-redis-config
```

**Purpose**: Confirm the ConfigMap was created with the correct data.

**Expected `get configmap` Output**:
```
NAME              DATA   AGE
my-redis-config   1      30s
```

**Expected `describe configmap` Output (Excerpt)**:
```
Name:         my-redis-config
Namespace:    default
Data
====
redis-config:
----
maxmemory 2mb
```

**Success Indicators**:
- ✅ Name: `my-redis-config`
- ✅ Data: `redis-config` with `maxmemory 2mb`

---

## 🔹 Step 9: Verify Deployment and Pod Status

```bash
kubectl get deployments
kubectl get pods
```

**Purpose**: Confirm the deployment is running and pods are operational.

**Expected Deployment Output**:
```
NAME              READY   UP-TO-DATE   AVAILABLE   AGE
redis-deployment   1/1     1            1           30s
```

**Expected Pod Output**:
```
NAME                              READY   STATUS    RESTARTS   AGE
redis-deployment-xyz123456-abc789 1/1     Running   0          30s
```

**Success Indicators**:
- ✅ Deployment: `1/1` ready
- ✅ Pod status: `Running`
- ✅ Ready: `1/1`
- ✅ No restarts

---

## 🔹 Step 10: Verify Deployment Details

```bash
kubectl describe deployment redis-deployment
```

**Purpose**: Confirm the deployment’s configuration, including image, volumes, and resources.

**Expected Output (Excerpt)**:
```
Name:                   redis-deployment
Pod Template:
  Labels:               app=redis
  Containers:
   redis-container:
    Image:        redis:alpine
    Port:         6379/TCP
    Requests:
      cpu:        1
    Volume Mounts:
      /redis-master-data from data (rw)
      /redis-master from redis-config (rw)
  Volumes:
   data:
     Type:       EmptyDir
   redis-config:
     Type:       ConfigMap
     Name:       my-redis-config
```

**Success Indicators**:
- ✅ Image: `redis:alpine`
- ✅ Port: `6379`
- ✅ CPU request: `1`
- ✅ Volume mounts: `/redis-master-data`, `/redis-master`
- ✅ Volumes: `data` as `EmptyDir`, `redis-config` from `my-redis-config`

---

## 🔹 Step 11: Verify Redis Functionality (Optional)

```bash
kubectl exec -it $(kubectl get pods -l app=redis -o name | head -n 1) -- redis-cli ping
```

**Purpose**: Confirm the Redis container is operational.

**Expected Output**:
```
PONG
```

---

## 🔹 Step 12: Verify ConfigMap Mount (Optional)

```bash
kubectl exec -it $(kubectl get pods -l app=redis -o name | head -n 1) -- cat /redis-master/redis-config
```

**Purpose**: Confirm the ConfigMap is mounted correctly.

**Expected Output**:
```
maxmemory 2mb
```

---

## 🔹 Step 13: Check Redis Logs (Optional)

```bash
kubectl logs -l app=redis
```

**Purpose**: Verify the Redis container started correctly.

**Expected Logs** (example):
```
1:M 25 Oct 2025 16:00:00.000 * Running mode=standalone, port=6379.
1:M 25 Oct 2025 16:00:00.000 * Server initialized
1:M 25 Oct 2025 16:00:00.000 * Ready to accept connections
```

---

## 🔹 Step 14: Clean Up (Optional)

```bash
kubectl delete -f redis-deployment.yaml
kubectl delete -f ConfigMap.yaml
```

**Purpose**: Remove the deployment and ConfigMap if testing is complete.

**Expected Output**:
```
deployment.apps "redis-deployment" deleted
configmap "my-redis-config" deleted
```

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **Jump Host**:

```bash
# Connect to jump host
ssh thor@jump_host

# Create ConfigMap YAML
cat > ConfigMap.yaml << 'EOF'
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-redis-config
data:
  redis-config: |
    maxmemory 2mb
EOF

# Create deployment YAML
cat > redis-deployment.yaml << 'EOF'
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
      - name: redis-container
        image: redis:alpine
        ports:
        - containerPort: 6379
        resources:
          requests:
            cpu: "1"
        volumeMounts:
        - name: data
          mountPath: /redis-master-data
        - name: redis-config
          mountPath: /redis-master
      volumes:
      - name: data
        emptyDir: {}
      - name: redis-config
        configMap:
          name: my-redis-config
EOF

# Deploy and verify
kubectl apply -f ConfigMap.yaml
kubectl apply -f redis-deployment.yaml
kubectl get configmap
kubectl get deployments
kubectl get pods
kubectl describe deployment redis-deployment
```

---

## 💡 Common Kubernetes Deployment Issues & Fixes

### **Issue 1: ConfigMap Not Found**
**Problem**: Pod fails with `Failed to mount ConfigMap`.
**Fix**: Verify ConfigMap exists and name matches.
```bash
kubectl get configmap my-redis-config
kubectl describe pod -l app=redis
```

### **Issue 2: Pod Not Running**
**Problem**: `kubectl get pods` shows `Error` or `CrashLoopBackOff`.
**Fix**: Check logs and events.
```bash
kubectl logs -l app=redis
kubectl describe pod -l app=redis
```

### **Issue 3: Image Pull Failure**
**Problem**: Pods in `ErrImagePull` or `ImagePullBackOff`.
**Fix**: Verify image name and tag.
```bash
docker pull redis:alpine
kubectl rollout restart deployment redis-deployment
```

### **Issue 4: Insufficient CPU**
**Problem**: Pod fails to schedule due to CPU constraints.
**Fix**: Check node resources and adjust CPU request if needed.
```bash
kubectl describe node
kubectl edit deployment redis-deployment
```

---

## 🔧 Troubleshooting Deployment Failures

### **Error: Pod Not Running**
**Symptoms**: `kubectl get pods` shows `Error` or `CrashLoopBackOff`.
**Solution**: Check logs and events.
```bash
kubectl logs -l app=redis
kubectl get events --field-selector involvedObject.name=redis-deployment
```

### **Error: ConfigMap Mount Failure**
**Symptoms**: Pod fails with volume mount error.
**Solution**: Verify ConfigMap and volume configuration.
```bash
kubectl describe configmap my-redis-config
kubectl describe pod -l app=redis
```

### **Error: YAML Syntax Error**
**Symptoms**: `kubectl apply` fails with parsing error.
**Solution**: Validate YAML syntax.
```bash
kubectl apply -f redis-deployment.yaml --dry-run=client
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was creating a ConfigMap and Redis deployment with specific configurations, including a CPU request, two volume mounts (`emptyDir` and ConfigMap), and ensuring the deployment is running correctly.

**💡 Solution Approach**:
1. **ConfigMap Creation**: Defined `my-redis-config` with `maxmemory 2mb`.
2. **Deployment Configuration**: Created `redis-deployment` with `redis:alpine`, 1 replica, CPU request, and volume mounts.
3. **Deployment**: Applied both YAMLs using `kubectl apply`.
4. **Verification**: Confirmed ConfigMap, deployment, and pod status.

**🎯 Key Success Factors**:
- **Correct ConfigMap**: Set `maxmemory 2mb` in `redis-config`.
- **Deployment Setup**: Used `redis:alpine`, 1 CPU, and correct volume mounts.
- **Verification**: Ensured pod is `Running` and ConfigMap is mounted.
- **Testing**: Validated Redis functionality with `redis-cli ping`.

**⚠️ Critical Fixes**:
- Ensure `my-redis-config` ConfigMap is created before the deployment.
- Use exact image `redis:alpine`.
- Mount volumes correctly at `/redis-master-data` and `/redis-master`.
- Set CPU request to `1`.

**🔒 Kubernetes Best Practices Applied**:
- **ConfigMap Usage**: Stored configuration separately for flexibility.
- **Resource Requests**: Specified CPU request for scheduling.
- **Minimal Configuration**: Kept YAMLs simple with required fields.
- **Verification**: Used `kubectl describe` and `kubectl logs` for validation.

**⚠️ Important Troubleshooting Concepts**:
- **ConfigMap Verification**: Check `kubectl describe configmap` for data integrity.
- **Pod Logs**: Use `kubectl logs` to diagnose Redis issues.
- **Events**: Use `kubectl get events` to identify deployment issues.
- **Exec Debugging**: Use `kubectl exec` to verify volume mounts.

---

## ⚠️ Important Production Notes

🔧 **Deployment Debugging**: Use `kubectl describe`, `kubectl logs`, and `kubectl exec` for issue resolution.
🔐 **Image Security**: Pin specific `redis` versions (e.g., `redis:7.0-alpine`) in production.
📊 **Resource Management**: Add memory limits and adjust CPU requests based on workload.
🛡️ **Persistent Storage**: Replace `emptyDir` with persistent volumes for production data.

*Last Updated: October 25, 2025*