# 🌟 Task 59 - Troubleshoot and Fix Redis Deployment in Kubernetes

**📌 Task Description**

The **Nautilus DevOps team** deployed a Redis application on a Kubernetes cluster last week, but a recent change caused the application to go down. The task is to troubleshoot and fix the issues with the `redis-deployment` deployment, ensuring the pods return to a running state. The issues involve an incorrect image name and ConfigMap name.

**👉 Task Requirements**:
- Fix the `redis-deployment` deployment:
  - Correct the image name from `redis:alpin` to `redis:alpine`.
  - Correct the ConfigMap name from `redis-cofig` to `redis-config` in the volume configuration.
- Ensure all pods are in the `Running` state after the fix.
- Use `kubectl` on the jump host, which is pre-configured to interact with the Kubernetes cluster.

**💡 Note**: Infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to the Jump Host

```bash
ssh thor@jump_host
```

**Purpose**: Connect to the jump host where `kubectl` is configured to manage the Kubernetes cluster.

---

## 🔹 Step 2: Investigate the Deployment Issue

```bash
kubectl get deployments
kubectl get pods
```

**Purpose**: Check the status of the `redis-deployment` and its pods to confirm the issue.

**Expected Deployment Output**:
```
NAME              READY   UP-TO-DATE   AVAILABLE   AGE
redis-deployment   0/1     0            0           1h
```

**Expected Pod Output**:
```
NAME                              READY   STATUS             RESTARTS   AGE
redis-deployment-xyz123456-abc789 0/1     ImagePullBackOff   0          1h
```

**Observations**:
- Pods are not running (e.g., `ImagePullBackOff` or `CrashLoopBackOff`).
- Likely causes: Incorrect image name (`redis:alpin`) or ConfigMap reference (`redis-cofig`).

---

## 🔹 Step 3: Inspect Deployment Details

```bash
kubectl describe deployment redis-deployment
```

**Purpose**: Identify specific errors related to the image or ConfigMap.

**Expected Output (Excerpt)**:
```
Events:
  Type     Reason             Age   From                   Message
  ----     ------             ----  ----                   -------
  Warning  Failed             1m    deployment-controller  Failed to create pod: ErrImagePull
  Warning  FailedMount        1m    kubelet                Unable to attach or mount volumes: configmap "redis-cofig" not found
```

**Findings**:
- **Image Issue**: `redis:alpin` is invalid (typo; should be `redis:alpine`).
- **ConfigMap Issue**: `redis-cofig` is referenced but does not exist (should be `redis-config`).

---

## 🔹 Step 4: Edit the Deployment

```bash
kubectl edit deployment redis-deployment
```

**Purpose**: Modify the deployment to correct the image and ConfigMap names.

**Changes to Make**:
1. **Fix Image Name**:
   - Locate: `image: redis:alpin`
   - Change to: `image: redis:alpine`
2. **Fix ConfigMap Name**:
   - Locate: `configMapRef: name: redis-cofig` (in the volume configuration)
   - Change to: `configMapRef: name: redis-config`

**Reference Deployment YAML (for clarity)**:
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
        volumeMounts:
        - name: redis-config-volume
          mountPath: /etc/redis
      volumes:
      - name: redis-config-volume
        configMap:
          name: redis-config
```

**Save and Exit**: Press `Esc`, type `:wq`, press `Enter`.

**Note**: The actual YAML may include additional fields (e.g., resource limits, labels). Ensure only the specified changes (image and ConfigMap name) are made.

---

## 🔹 Step 5: Verify ConfigMap Existence

```bash
kubectl get configmap redis-config
```

**Purpose**: Confirm the `redis-config` ConfigMap exists to avoid mount errors.

**Expected Output**:
```
NAME           DATA   AGE
redis-config   1      2d
```

**If ConfigMap is missing**:
- Create or verify the correct ConfigMap name via:
  ```bash
  kubectl describe configmap
  ```
- Contact the team for the correct ConfigMap configuration if `redis-config` does not exist.

---

## 🔹 Step 6: Verify Deployment and Pod Status

```bash
kubectl get deployments
kubectl get pods
```

**Purpose**: Confirm the deployment is updated and pods are running.

**Expected Deployment Output**:
```
NAME              READY   UP-TO-DATE   AVAILABLE   AGE
redis-deployment   1/1     1            1           1h
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

## 🔹 Step 7: Verify Deployment Details

```bash
kubectl describe deployment redis-deployment
```

**Purpose**: Confirm the corrected image and ConfigMap are applied.

**Expected Output (Excerpt)**:
```
Pod Template:
  Containers:
   redis-container:
    Image:        redis:alpine
    Volume Mounts:
      /etc/redis from redis-config-volume (rw)
  Volumes:
   redis-config-volume:
    Type:       ConfigMap
    Name:       redis-config
```

**Success Indicators**:
- ✅ Image: `redis:alpine`
- ✅ ConfigMap: `redis-config`
- ✅ No error events (e.g., `ErrImagePull` or `FailedMount`)

---

## 🔹 Step 8: Test Redis Accessibility (Optional)

```bash
kubectl exec -it $(kubectl get pods -l app=redis -o name | head -n 1) -- redis-cli ping
```

**Purpose**: Verify the Redis container is operational.

**Expected Output**:
```
PONG
```

---

## 🔹 Step 9: Check Redis Logs (Optional)

```bash
kubectl logs -l app=redis
```

**Purpose**: Confirm the Redis container started correctly.

**Expected Logs** (example):
```
1:M 25 Oct 2025 16:00:00.000 * Running mode=standalone, port=6379.
1:M 25 Oct 2025 16:00:00.000 * Server initialized
1:M 25 Oct 2025 16:00:00.000 * Ready to accept connections
```

---

## 🔹 Step 10: Clean Up (Optional)

```bash
kubectl delete deployment redis-deployment
```

**Purpose**: Remove the deployment if testing is complete.

**Expected Output**:
```
deployment.apps "redis-deployment" deleted
```

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **Jump Host**:

```bash
# Connect to jump host
ssh thor@jump_host

# Investigate and fix deployment
kubectl get deployments
kubectl get pods
kubectl describe deployment redis-deployment
kubectl edit deployment redis-deployment  # Change image to redis:alpine, configMap to redis-config
kubectl get configmap redis-config
kubectl get deployments
kubectl get pods
kubectl describe deployment redis-deployment
```

---

## 💡 Common Kubernetes Deployment Issues & Fixes

### **Issue 1: Image Pull Failure**
**Problem**: Pods in `ErrImagePull` or `ImagePullBackOff` due to `redis:alpin`.
**Fix**: Correct the image name to `redis:alpine`.
```bash
kubectl edit deployment redis-deployment
docker pull redis:alpine
```

### **Issue 2: ConfigMap Not Found**
**Problem**: Pods fail with `FailedMount` due to `redis-cofig`.
**Fix**: Correct the ConfigMap name to `redis-config`.
```bash
kubectl get configmap
kubectl edit deployment redis-deployment
```

### **Issue 3: Pods Not Running**
**Problem**: Pods in `CrashLoopBackOff` or `Error`.
**Fix**: Check logs and events for errors.
```bash
kubectl logs -l app=redis
kubectl describe pod -l app=redis
```

### **Issue 4: Deployment Not Updating**
**Problem**: Pods do not reflect changes after `kubectl edit`.
**Fix**: Restart the deployment.
```bash
kubectl rollout restart deployment redis-deployment
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

### **Error: Image Not Found**
**Symptoms**: "Failed to pull image redis:alpin".
**Solution**: Ensure correct image name and registry access.
```bash
kubectl describe pod -l app=redis
```

### **Error: ConfigMap Mount Failure**
**Symptoms**: "Unable to attach or mount volumes: configmap redis-cofig not found".
**Solution**: Verify ConfigMap exists and name is correct.
```bash
kubectl get configmap redis-config
kubectl describe deployment redis-deployment
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was identifying and fixing two errors in the `redis-deployment`: an incorrect image name (`redis:alpin`) and an invalid ConfigMap name (`redis-cofig`), which caused the pods to fail.

**💡 Solution Approach**:
1. **Issue Identification**: Used `kubectl describe` to confirm image and ConfigMap errors.
2. **Deployment Correction**: Edited the deployment to fix `image: redis:alpine` and `configMap: redis-config`.
3. **ConfigMap Verification**: Checked for `redis-config` existence.
4. **Deployment Update**: Applied changes via `kubectl edit`.
5. **Verification**: Confirmed pods are running with `kubectl get pods` and `kubectl describe`.

**🎯 Key Success Factors**:
- **Accurate Diagnosis**: Identified typos in image and ConfigMap names.
- **Correct Fixes**: Updated `redis:alpine` and `redis-config` in the deployment.
- **Verification**: Ensured pods reached `Running` state.
- **Testing**: Optionally validated Redis functionality with `redis-cli`.

**⚠️ Critical Fixes**:
- Correct image name from `redis:alpin` to `redis:alpine`.
- Correct ConfigMap name from `redis-cofig` to `redis-config`.
- Verify ConfigMap existence before applying changes.
- Monitor pod status post-update.

**🔒 Kubernetes Best Practices Applied**:
- **Troubleshooting**: Used `kubectl describe` and `kubectl logs` for systematic diagnosis.
- **Minimal Changes**: Modified only the necessary fields (image, ConfigMap).
- **Validation**: Confirmed ConfigMap and pod status.
- **Restart Strategy**: Leveraged `kubectl edit` for quick fixes.

**⚠️ Important Troubleshooting Concepts**:
- **Events**: Use `kubectl get events` to diagnose deployment issues.
- **Logs**: Check pod logs for runtime errors.
- **Describe**: Use `kubectl describe` for detailed configuration checks.
- **ConfigMap Verification**: Ensure referenced ConfigMaps exist.

---

## ⚠️ Important Production Notes

🔧 **Deployment Debugging**: Use `kubectl describe`, `kubectl logs`, and `kubectl exec` for thorough issue resolution.
🔐 **Image Security**: Pin specific `redis` versions (e.g., `redis:7.0-alpine`) in production to avoid unexpected updates.
📊 **Resource Limits**: Add `resources` (e.g., CPU/memory limits) to prevent overuse.
🛡️ **Service Exposure**: Configure a service (e.g., ClusterIP) for Redis access in production.

*Last Updated: October 25, 2025*