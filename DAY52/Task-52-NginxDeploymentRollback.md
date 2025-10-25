# 🌟 Task 52 - Rollback Nginx Deployment in Kubernetes

**📌 Task Description**

The **Nautilus DevOps team** deployed a new release for an application earlier today, but a customer reported a bug in this release. To address this, the team needs to revert the `nginx-deployment` deployment to its previous revision. The task involves performing a rollback on the Kubernetes cluster accessible from the jump host and verifying the application’s functionality post-rollback.

**👉 Task Requirements**:
- Perform a rollback on the `nginx-deployment` deployment to its previous revision.
- Verify the deployment and pods are operational after the rollback.
- Test the application by accessing the Nginx page via the website button (assumed to be a KodeKloud lab interface feature).
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
nginx-deployment   1/1     1            1           2h
```

**Success Indicators**:
- ✅ Deployment name: `nginx-deployment`
- ✅ Ready: `<number>/<number>` (e.g., `1/1`)
- ✅ Up-to-date and available replicas match desired state

---

## 🔹 Step 3: Check Deployment Revision History

```bash
kubectl rollout history deployment nginx-deployment
```

**Purpose**: View the revision history of `nginx-deployment` to confirm previous revisions exist for rollback.

**Expected Output**:
```
deployment.apps/nginx-deployment
REVISION  CHANGE-CAUSE
1         <none>
2         <none>
```

**Note**: The latest revision (e.g., `2`) represents the current buggy release, and the previous revision (e.g., `1`) is the target for rollback.

---

## 🔹 Step 4: Perform the Rollback

```bash
kubectl rollout undo deployment/nginx-deployment
```

**Purpose**: Revert the `nginx-deployment` to the previous revision, restoring the earlier configuration (e.g., image version).

**Expected Output**:
```
deployment.apps/nginx-deployment rolled back
```

**Alternative Command (to specific revision, if needed)**:
```bash
kubectl rollout undo deployment/nginx-deployment --to-revision=1
```

---

## 🔹 Step 5: Monitor the Rollback

```bash
kubectl rollout status deployment/nginx-deployment
```

**Purpose**: Monitor the progress of the rollback to ensure it completes successfully.

**Expected Output**:
```
deployment "nginx-deployment" successfully rolled out
```

---

## 🔹 Step 6: Verify Pod Status Post-Rollback

```bash
kubectl get pods -o wide
```

**Purpose**: Confirm all pods in the `nginx-deployment` are running with the previous configuration.

**Expected Output**:
```
NAME                               READY   STATUS    RESTARTS   AGE   IP           NODE
nginx-deployment-abc123456-def789   1/1     Running   0          30s   10.0.0.5     node01
```

**Success Indicators**:
- ✅ Pod names start with `nginx-deployment-`
- ✅ Status: `Running`
- ✅ Ready: `1/1`
- ✅ No restarts
- ✅ Recent `AGE` indicating new pods from rollback

---

## 🔹 Step 7: Verify Deployment Details

```bash
kubectl describe deployment nginx-deployment
```

**Purpose**: Check detailed information about the deployment, including the reverted image and pod status.

**Expected Output (Excerpt)**:
```
Name:                   nginx-deployment
Namespace:              default
Selector:               app=nginx
Replicas:               1 desired | 1 updated | 1 total
Pod Template:
  Containers:
   nginx-container:
    Image:              nginx:<previous-version>
    State:              Running
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  2m    deployment-controller  Scaled up replica set nginx-deployment-abc123456 to 1
```

**Note**: The image version will reflect the previous revision (e.g., `nginx:1.18` if the buggy release was `nginx:1.19`).

---

## 🔹 Step 8: Test Application Accessibility

**Action**: In the KodeKloud lab interface, click the **Website** button to access the Nginx page.

**Purpose**: Verify the application is accessible and functioning correctly after the rollback.

**Expected Output** (via browser or curl):
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

**Alternative CLI Test** (if no website button is available):
```bash
kubectl port-forward deployment/nginx-deployment 8080:80
```

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

## 🔹 Step 9: Check Container Logs

```bash
kubectl logs -l app=nginx
```

**Purpose**: Verify the Nginx container started correctly with the reverted image.

**Expected Logs**:
```
[notice] 1#1: start worker processes
[notice] 1#1: start worker process 32
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

# Verify deployment and perform rollback
kubectl get deployments
kubectl rollout history deployment nginx-deployment
kubectl rollout undo deployment/nginx-deployment
kubectl rollout status deployment/nginx-deployment
kubectl get pods -o wide
kubectl describe deployment nginx-deployment
```

---

## 💡 Common Kubernetes Rollback Issues & Fixes

### **Issue 1: Rollback Fails to Complete**
**Problem**: `kubectl rollout status` shows the rollback is stuck.
**Fix**: Check pod events and deployment status.
```bash
kubectl describe deployment nginx-deployment
kubectl describe pod -l app=nginx
```

### **Issue 2: No Previous Revision**
**Problem**: `kubectl rollout history` shows only one revision.
**Fix**: Verify revision history; rollback is not possible if only one revision exists.
```bash
kubectl rollout history deployment nginx-deployment
```

### **Issue 3: Pods in CrashLoopBackOff**
**Problem**: Pods restart repeatedly post-rollback; `STATUS: CrashLoopBackOff`.
**Fix**: Check logs for errors.
```bash
kubectl logs -l app=nginx
kubectl describe pod -l app=nginx
```

### **Issue 4: Image Pull Failure**
**Problem**: Pods in `ErrImagePull` or `ImagePullBackOff` after rollback.
**Fix**: Verify the previous image is available.
```bash
docker pull nginx:<previous-version>
kubectl rollout restart deployment nginx-deployment
```

---

## 🔧 Troubleshooting Rollback Failures

### **Error: Pods Not Running**
**Symptoms**: `kubectl get pods` shows `Error` or `CrashLoopBackOff`.
**Solution**: Check logs and events.
```bash
kubectl logs -l app=nginx
kubectl get events --field-selector involvedObject.name=nginx-deployment
```

### **Error: Image Not Found**
**Symptoms**: "Failed to pull image nginx:<previous-version>".
**Solution**: Ensure the previous image exists and cluster has registry access.
```bash
kubectl describe pod -l app=nginx
```

### **Error: Rollback Stuck**
**Symptoms**: `kubectl rollout status` does not complete.
**Solution**: Check for resource constraints or pod issues.
```bash
kubectl describe deployment nginx-deployment
kubectl top pod -l app=nginx
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was performing a rollback on the `nginx-deployment` to revert to the previous revision due to a reported bug, ensuring all pods remain operational with minimal disruption.

**💡 Solution Approach**:
1. **Deployment Verification**: Confirmed the `nginx-deployment` exists using `kubectl get deployments`.
2. **Revision Check**: Used `kubectl rollout history` to verify previous revisions.
3. **Rollback Execution**: Performed the rollback with `kubectl rollout undo`.
4. **Monitoring**: Tracked rollback progress with `kubectl rollout status`.
5. **Verification**: Checked pod status and image version with `kubectl get pods` and `kubectl describe`.
6. **Testing**: Validated application functionality via the website button or `kubectl port-forward`.

**🎯 Key Success Factors**:
- **Correct Rollback Command**: Used `kubectl rollout undo` to revert to the previous revision.
- **Revision Awareness**: Verified revision history to ensure rollback feasibility.
- **Monitoring**: Ensured rollback completion with `kubectl rollout status`.
- **Verification**: Confirmed all pods running with the previous image.
- **Testing**: Validated Nginx page accessibility to confirm bug resolution.

**⚠️ Critical Rollback Fixes**:
- Verify `nginx-deployment` exists before rollback.
- Check revision history to ensure a previous revision is available.
- Monitor rollout status to detect issues early.
- Validate pod status and image version post-rollback.

**🔒 Kubernetes Best Practices Applied**:
- **Rollback Strategy**: Used rolling updates for safe reversion.
- **Revision Tracking**: Leveraged `kubectl rollout history` for auditability.
- **Monitoring**: Monitored rollout status for reliability.
- **Validation**: Used `kubectl describe` to confirm configuration.

**⚠️ Important Troubleshooting Concepts**:
- **Events**: Use `kubectl get events` to diagnose rollback issues.
- **Logs**: Check container logs for runtime errors.
- **Describe**: Use `kubectl describe` for detailed deployment and pod status.
- **Revision History**: Ensure multiple revisions exist for rollback.

---

## ⚠️ Important Production Notes

🔧 **Rollback Management**: Always verify revision history before rollback to avoid unexpected behavior.
🔐 **Image Security**: Ensure previous image versions are available in the registry and from trusted sources.
📊 **Monitoring**: Use `kubectl top` to verify resource usage post-rollback.
🛡️ **Service Continuity**: Ensure a service (e.g., ClusterIP or NodePort) remains configured for production access.

*Last Updated: October 25, 2025*