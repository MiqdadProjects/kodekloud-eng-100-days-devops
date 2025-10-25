# 🌟 Task 51 - Perform Rolling Update for Nginx Deployment in Kubernetes

**📌 Task Description**

The **Nautilus Application Development team** has introduced updates to an application running on a Kubernetes cluster, utilizing the `nginx` web server. These updates are encapsulated in a new image, `nginx:1.19`. The team requires a rolling update to deploy this image to an existing deployment named `nginx-deployment`. The task ensures minimal downtime and verifies that all pods are operational post-update.

**👉 Task Requirements**:
- Perform a rolling update on the `nginx-deployment` deployment to use the `nginx:1.19` image.
- Ensure the container name within the deployment is `nginx-container`.
- Verify that all pods are running after the update.
- Use `kubectl` on the jump host, which is pre-configured to interact with the Kubernetes cluster.

**💡 Note**: Infrastructure details, if needed, are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to the Jump Host

```bash
ssh thor@jump_host
```

**Purpose**: Connect to the jump host where `kubectl` is configured to manage the Kubernetes cluster.

---

## 🔹 Step 2: Verify Existing Deployment

```bash
kubectl get deployments
```

**Purpose**: Confirm the `nginx-deployment` exists and check its current status.

**Expected Output**:
```
NAME              READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   1/1     1            1           2d
```

**Success Indicators**:
- ✅ Deployment name: `nginx-deployment`
- ✅ Ready: `<number>/<number>` (e.g., `1/1`)
- ✅ Up-to-date and available replicas match desired state

---

## 🔹 Step 3: Perform the Rolling Update

```bash
kubectl set image deployment/nginx-deployment nginx-container=nginx:1.19
```

**Purpose**: Update the `nginx-container` in the `nginx-deployment` to use the `nginx:1.19` image, triggering a rolling update.

**Expected Output**:
```
deployment.apps/nginx-deployment image updated
```

**Alternative YAML Approach** (for reference or manual update):
```bash
kubectl edit deployment nginx-deployment
```

**Update YAML (Excerpt)**:
```yaml
spec:
  template:
    spec:
      containers:
        - name: nginx-container
          image: nginx:1.19
```

**Save and Exit**: Press `Esc`, type `:wq`, press `Enter`.

**Key Notes**:
- The `kubectl set image` command modifies the deployment’s container image, initiating a rolling update.
- The rolling update ensures zero downtime by gradually replacing old pods with new ones.
- The container name (`nginx-container`) must match the existing deployment’s container name.

---

## 🔹 Step 4: Monitor the Rolling Update

```bash
kubectl rollout status deployment/nginx-deployment
```

**Purpose**: Monitor the progress of the rolling update to ensure it completes successfully.

**Expected Output**:
```
deployment "nginx-deployment" successfully rolled out
```

---

## 🔹 Step 5: Verify Pod Status Post-Update

```bash
kubectl get pods -o wide
```

**Purpose**: Confirm all pods in the `nginx-deployment` are running with the updated image.

**Expected Output**:
```
NAME                               READY   STATUS    RESTARTS   AGE   IP           NODE
nginx-deployment-xyz123456-abc789   1/1     Running   0          30s   10.0.0.5     node01
```

**Success Indicators**:
- ✅ Pod names start with `nginx-deployment-`
- ✅ Status: `Running`
- ✅ Ready: `1/1`
- ✅ No restarts
- ✅ Recent `AGE` indicating new pods

---

## 🔹 Step 6: Verify Deployment Details

```bash
kubectl describe deployment nginx-deployment
```

**Purpose**: Check detailed information about the deployment, including the updated image and pod status.

**Expected Output (Excerpt)**:
```
Name:                   nginx-deployment
Namespace:              default
Selector:               app=nginx
Replicas:               1 desired | 1 updated | 1 total
Pod Template:
  Containers:
   nginx-container:
    Image:              nginx:1.19
    State:              Running
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  2m    deployment-controller  Scaled up replica set nginx-deployment-xyz123456 to 1
```

**Success Indicators**:
- ✅ Image updated to `nginx:1.19`
- ✅ Container name: `nginx-container`
- ✅ Replicas running as expected

---

## 🔹 Step 7: Test Pod Accessibility (Optional)

```bash
kubectl port-forward deployment/nginx-deployment 8080:80
```

**Purpose**: Forward the deployment’s port 80 to localhost:8080 for testing.

**In a new terminal**:
```bash
curl http://localhost:8080
```

**Expected Output**:
```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
...
<h1>Welcome to nginx!</h1>
...
</html>
```

---

## 🔹 Step 8: Check Container Logs

```bash
kubectl logs -l app=nginx
```

**Purpose**: Verify the Nginx container started correctly with the updated image.

**Expected Logs**:
```
[notice] 1#1: start worker processes
[notice] 1#1: start worker process 32
```

---

## 🔹 Step 9: Roll Back Update (Optional, if needed)

```bash
kubectl rollout undo deployment/nginx-deployment
kubectl rollout status deployment/nginx-deployment
```

**Purpose**: Revert to the previous image if the update fails or introduces issues.

**Expected Output**:
```
deployment "nginx-deployment" rolled back
deployment "nginx-deployment" successfully rolled out
```

---

## 🔹 Step 10: Clean Up (Optional)

```bash
kubectl delete deployment nginx-deployment
```

**Purpose**: Remove the deployment if testing is complete.

**Expected Output**:
```
deployment.apps "nginx-deployment" deleted
```

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **Jump Host**:

```bash
# Connect to jump host
ssh thor@jump_host

# Verify deployment
kubectl get deployments

# Perform rolling update and verify
kubectl set image deployment/nginx-deployment nginx-container=nginx:1.19
kubectl rollout status deployment/nginx-deployment
kubectl get pods -o wide
kubectl describe deployment nginx-deployment
```

**Alternative YAML-based commands** (if manual update preferred):
```bash
# Edit deployment
kubectl edit deployment nginx-deployment
# Update image to nginx:1.19 in the editor
# Verify
kubectl rollout status deployment/nginx-deployment
kubectl get pods -o wide
```

---

## 💡 Common Kubernetes Deployment Issues & Fixes

### **Issue 1: Update Fails to Complete**
**Problem**: `kubectl rollout status` shows the update is stuck.
**Fix**: Check pod events and deployment status.
```bash
kubectl describe deployment nginx-deployment
kubectl describe pod -l app=nginx
```

### **Issue 2: Image Pull Failure**
**Problem**: Pods in `ErrImagePull` or `ImagePullBackOff`.
**Fix**: Verify image name and tag.
```bash
docker pull nginx:1.19
kubectl rollout restart deployment nginx-deployment
```

### **Issue 3: Pods in CrashLoopBackOff**
**Problem**: Pods restart repeatedly; `STATUS: CrashLoopBackOff`.
**Fix**: Check logs for errors.
```bash
kubectl logs -l app=nginx
kubectl describe pod -l app=nginx
```

### **Issue 4: Incorrect Container Name**
**Problem**: `kubectl set image` fails with "container not found".
**Fix**: Verify the container name in the deployment.
```bash
kubectl get deployment nginx-deployment -o yaml | grep container
kubectl edit deployment nginx-deployment
```

---

## 🔧 Troubleshooting Deployment Failures

### **Error: Pods Not Running**
**Symptoms**: `kubectl get pods` shows `Error` or `CrashLoopBackOff`.
**Solution**: Check logs and events.
```bash
kubectl logs -l app=nginx
kubectl get events --field-selector involvedObject.name=nginx-deployment
```

### **Error: Image Not Found**
**Symptoms**: "Failed to pull image nginx:1.19".
**Solution**: Ensure image exists and cluster has registry access.
```bash
kubectl describe pod -l app=nginx
```

### **Error: Rolling Update Stuck**
**Symptoms**: `kubectl rollout status` does not complete.
**Solution**: Check for resource constraints or pod issues.
```bash
kubectl describe deployment nginx-deployment
kubectl top pod -l app=nginx
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was performing a rolling update on the `nginx-deployment` to use the `nginx:1.19` image while ensuring all pods remain operational with minimal downtime.

**💡 Solution Approach**:
1. **Deployment Verification**: Confirmed the `nginx-deployment` exists using `kubectl get deployments`.
2. **Rolling Update**: Updated the image to `nginx:1.19` using `kubectl set image`.
3. **Monitoring**: Used `kubectl rollout status` to track the update progress.
4. **Verification**: Checked pod status and image version with `kubectl get pods` and `kubectl describe`.
5. **Testing**: Optionally tested accessibility with `kubectl port-forward`.

**🎯 Key Success Factors**:
- **Correct Command**: Used `kubectl set image` with the exact container name (`nginx-container`).
- **Accurate Image**: Specified `nginx:1.19` as required.
- **Monitoring**: Ensured update completion with `kubectl rollout status`.
- **Verification**: Confirmed all pods running with updated image.
- **Minimal Downtime**: Leveraged rolling update to maintain availability.

**⚠️ Critical Deployment Fixes**:
- Verify container name (`nginx-container`) matches the deployment’s configuration.
- Ensure `nginx:1.19` image is available in the registry.
- Monitor rollout to detect and resolve issues promptly.
- Validate pod status post-update.

**🔒 Kubernetes Best Practices Applied**:
- **Rolling Updates**: Used rolling updates to ensure zero downtime.
- **Version Control**: Specified exact image version (`nginx:1.19`).
- **Monitoring**: Tracked rollout status for reliability.
- **Validation**: Used `kubectl describe` to confirm configuration.

**⚠️ Important Troubleshooting Concepts**:
- **Events**: Use `kubectl get events` to diagnose update issues.
- **Logs**: Check container logs for runtime errors.
- **Describe**: Use `kubectl describe` for detailed deployment and pod status.
- **Rollback**: Use `kubectl rollout undo` for quick recovery if needed.

---

## ⚠️ Important Production Notes

🔧 **Update Management**: Monitor rolling updates with `kubectl rollout status` to ensure success.
🔐 **Image Security**: Use trusted registries and specific image versions (e.g., `nginx:1.19`) to avoid vulnerabilities.
📊 **Resource Monitoring**: Use `kubectl top` to verify resource usage post-update.
🛡️ **Service Exposure**: Ensure a service (e.g., ClusterIP or NodePort) is configured for production access.

*Last Updated: October 25, 2025*