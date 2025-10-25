# 🌟 Task 50 - Create HTTPD Pod with Resource Limits in Kubernetes

**📌 Task Description**

The **Nautilus DevOps team** has identified performance issues in Kubernetes-hosted applications due to resource constraints. To mitigate this, a team member is tasked with creating a pod with specific resource limits on the Kubernetes cluster accessible from the jump host.

**👉 Task Requirements**:
- Create a pod named `httpd-pod`.
- Use a single container named `httpd-container` with the `httpd:latest` image.
- Set resource limits:
  - **Requests**: Memory: `15Mi`, CPU: `100m`
  - **Limits**: Memory: `20Mi`, CPU: `100m`
- Use `kubectl` on the jump host, which is pre-configured to interact with the Kubernetes cluster.
- Apply the configuration and verify the pod is running with the specified resource limits.

**💡 Note**: Infrastructure details, if needed, are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to the Jump Host

```bash
ssh thor@jump_host
```

**Purpose**: Connect to the jump host where `kubectl` is configured to manage the Kubernetes cluster.

---

## 🔹 Step 2: Create the Pod YAML File

```bash
vi httpd-pod.yaml
```

**Purpose**: Create a Kubernetes YAML file to define the `httpd-pod` with resource limits.

---

## 🔹 Step 3: Add Pod YAML Content

**Pod YAML**:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: httpd-pod
spec:
  containers:
    - name: httpd-container
      image: httpd:latest
      resources:
        requests:
          memory: "15Mi"
          cpu: "100m"
        limits:
          memory: "20Mi"
          cpu: "100m"
```

**Key Configurations**:
- **apiVersion: v1**: Specifies the Kubernetes API version for pods.
- **kind: Pod**: Defines the resource type as a pod.
- **metadata.name: httpd-pod**: Sets the pod name to `httpd-pod`.
- **spec.containers**: Defines a single container:
  - `name: httpd-container`: Sets the container name.
  - `image: httpd:latest`: Uses the `httpd:latest` image from Docker Hub.
  - `resources.requests`: Sets minimum resource requirements (`memory: 15Mi`, `cpu: 100m`).
  - `resources.limits`: Sets maximum resource limits (`memory: 20Mi`, `cpu: 100m`).

**Save and Exit**: Press `Esc`, type `:wq`, press `Enter`.

---

## 🔹 Step 4: Verify Pod YAML Syntax

```bash
cat httpd-pod.yaml
```

**Purpose**: Confirm the YAML file contains the correct configuration.

**Validation Checklist**:
- ✅ `apiVersion: v1` and `kind: Pod`
- ✅ Pod name: `httpd-pod`
- ✅ Container name: `httpd-container`
- ✅ Image: `httpd:latest`
- ✅ Resource requests: `memory: 15Mi`, `cpu: 100m`
- ✅ Resource limits: `memory: 20Mi`, `cpu: 100m`
- ✅ Correct YAML indentation (2 spaces)

---

## 🔹 Step 5: Apply the Pod Configuration

```bash
kubectl apply -f httpd-pod.yaml
```

**Purpose**: Deploy the pod to the Kubernetes cluster.

**Expected Output**:
```
pod/httpd-pod created
```

---

## 🔹 Step 6: Verify Pod Creation

```bash
kubectl get pods
```

**Purpose**: Confirm the `httpd-pod` is running.

**Expected Output**:
```
NAME        READY   STATUS    RESTARTS   AGE
httpd-pod   1/1     Running   0          10s
```

**Success Indicators**:
- ✅ Pod name: `httpd-pod`
- ✅ Status: `Running`
- ✅ Ready: `1/1`
- ✅ No restarts

---

## 🔹 Step 7: Verify Pod Details and Resource Limits

```bash
kubectl describe pod httpd-pod
```

**Purpose**: Check detailed information about the pod, including resource limits and container status.

**Expected Output (Excerpt)**:
```
Name:         httpd-pod
Namespace:    default
Containers:
  httpd-container:
    Image:        httpd:latest
    Limits:
      cpu:        100m
      memory:     20Mi
    Requests:
      cpu:        100m
      memory:     15Mi
    State:        Running
```

**Success Indicators**:
- ✅ Correct resource requests and limits displayed
- ✅ Container state: `Running`
- ✅ Image: `httpd:latest`

---

## 🔹 Step 8: Test Pod Accessibility (Optional)

```bash
kubectl port-forward pod/httpd-pod 8080:80
```

**Purpose**: Forward the pod’s port 80 to localhost:8080 for testing.

**In a new terminal**:
```bash
curl http://localhost:8080
```

**Expected Output**:
```
<html><body><h1>It works!</h1></body></html>
```

---

## 🔹 Step 9: Check Container Logs

```bash
kubectl logs httpd-pod
```

**Purpose**: Verify the HTTPD container started correctly.

**Expected Logs**:
```
AH00558: httpd: Could not reliably determine the server's fully qualified domain name...
[Sat Oct 25 15:46:00.000000 2025] [mpm_event:notice] [pid 1] AH00489: Apache/2.4.57 (Unix) configured
```

---

## 🔹 Step 10: Verify Resource Usage (Optional)

```bash
kubectl top pod httpd-pod
```

**Purpose**: Check the pod’s resource usage to ensure it aligns with the specified limits.

**Expected Output**:
```
NAME        CPU(cores)   MEMORY(bytes)
httpd-pod   10m          12Mi
```

**Note**: Actual usage may vary but should not exceed `100m` CPU and `20Mi` memory.

---

## 🔹 Step 11: Clean Up (Optional)

```bash
kubectl delete -f httpd-pod.yaml
```

**Purpose**: Remove the pod if testing is complete.

**Expected Output**:
```
pod "httpd-pod" deleted
```

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **Jump Host**:

```bash
# Connect to jump host
ssh thor@jump_host

# Create pod YAML
cat > httpd-pod.yaml << 'EOF'
apiVersion: v1
kind: Pod
metadata:
  name: httpd-pod
spec:
  containers:
    - name: httpd-container
      image: httpd:latest
      resources:
        requests:
          memory: "15Mi"
          cpu: "100m"
        limits:
          memory: "20Mi"
          cpu: "100m"
EOF

# Deploy and verify
kubectl apply -f httpd-pod.yaml
kubectl get pods
kubectl describe pod httpd-pod
```

---

## 💡 Common Kubernetes Pod Issues & Fixes

### **Issue 1: Pod Stuck in Pending**
**Problem**: `kubectl get pods` shows `Pending` status.
**Fix**: Check events for scheduling issues, such as insufficient resources.
```bash
kubectl describe pod httpd-pod
```
**Common Causes**: Cluster lacks enough CPU (`100m`) or memory (`15Mi`) for requests.

### **Issue 2: Image Pull Failure**
**Problem**: Pod in `ErrImagePull` or `ImagePullBackOff`.
**Fix**: Verify image name and tag.
```bash
docker pull httpd:latest
kubectl delete pod httpd-pod
kubectl apply -f httpd-pod.yaml
```

### **Issue 3: Container CrashLoopBackOff**
**Problem**: Pod restarts repeatedly; `STATUS: CrashLoopBackOff`.
**Fix**: Check logs for errors.
```bash
kubectl logs httpd-pod
kubectl describe pod httpd-pod
```

### **Issue 4: Resource Limits Not Applied**
**Problem**: `kubectl describe pod` does not show expected resource limits.
**Fix**: Verify YAML syntax and reapply.
```bash
kubectl apply -f httpd-pod.yaml --dry-run=client
kubectl edit pod httpd-pod
```

---

## 🔧 Troubleshooting Pod Failures

### **Error: Pod Not Running**
**Symptoms**: `kubectl get pods` shows `Error` or `CrashLoopBackOff`.
**Solution**: Check logs and events.
```bash
kubectl logs httpd-pod
kubectl get events --field-selector involvedObject.name=httpd-pod
```

### **Error: Insufficient Resources**
**Symptoms**: `Pending` status with "Insufficient cpu/memory" in events.
**Solution**: Verify cluster capacity or adjust requests/limits.
```bash
kubectl describe node
```

### **Error: YAML Syntax Error**
**Symptoms**: `kubectl apply` fails with parsing error.
**Solution**: Validate YAML syntax.
```bash
kubectl apply -f httpd-pod.yaml --dry-run=client
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was creating a Kubernetes pod with precise specifications for name, image, and resource limits, ensuring it runs successfully within the cluster’s resource constraints.

**💡 Solution Approach**:
1. **YAML Creation**: Defined `httpd-pod.yaml` with correct `apiVersion`, `kind`, `metadata`, and `resources`.
2. **Resource Configuration**: Set `requests` and `limits` for memory and CPU as specified.
3. **Image Specification**: Used `httpd:latest` as required.
4. **Deployment**: Applied the YAML using `kubectl apply`.
5. **Verification**: Checked pod status and resource limits with `kubectl get pods` and `kubectl describe`.

**🎯 Key Success Factors**:
- **Correct YAML Structure**: Ensured proper `apiVersion: v1` and pod specifications.
- **Accurate Naming**: Used `httpd-pod` and `httpd-container` as specified.
- **Resource Limits**: Correctly set `requests` and `limits` for CPU and memory.
- **Image Validation**: Confirmed `httpd:latest` availability.
- **Testing**: Verified pod status and resource allocation.

**⚠️ Critical Pod Fixes**:
- Use `apiVersion: v1` for pod resources.
- Ensure exact names for pod (`httpd-pod`) and container (`httpd-container`).
- Validate resource units (`Mi` for memory, `m` for CPU millicores).
- Check YAML indentation (2 spaces).

**🔒 Kubernetes Best Practices Applied**:
- **Resource Management**: Set appropriate `requests` and `limits` to prevent resource contention.
- **Minimal Configuration**: Kept YAML simple with only required fields.
- **Dry Run Testing**: Used `--dry-run=client` to validate YAML.
- **Log Checking**: Verified container logs for startup confirmation.

**⚠️ Important Troubleshooting Concepts**:
- **Events**: Use `kubectl get events` to diagnose scheduling issues.
- **Logs**: Check container logs for runtime errors.
- **Describe**: Use `kubectl describe` for detailed pod status.
- **Resource Monitoring**: Use `kubectl top` to verify resource usage.

---

## ⚠️ Important Production Notes

🔧 **Pod Debugging**: Use `kubectl describe`, `kubectl logs`, and `kubectl top` for systematic issue resolution.
🔐 **Image Security**: Pin specific `httpd` versions (e.g., `httpd:2.4.57`) in production to avoid unexpected updates.
📊 **Resource Tuning**: Adjust `requests` and `limits` based on application performance metrics.
🛡️ **Networking**: Expose pods via services (e.g., ClusterIP or NodePort) for production access.

*Last Updated: October 25, 2025*