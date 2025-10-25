# 🌟 Task 49 - Create Nginx Deployment in Kubernetes

**📌 Task Description**

The **Nautilus DevOps team** is advancing its Kubernetes expertise for application management. A team member has been tasked with creating a deployment on the Kubernetes cluster accessible from the jump host. The deployment must meet specific requirements for its configuration.

**👉 Task Requirements**:
- Create a deployment named `nginx`.
- Use the `nginx:latest` image.
- Ensure the image tag is explicitly specified as `nginx:latest`.
- Use `kubectl` on the jump host, which is pre-configured to interact with the Kubernetes cluster.
- Verify the deployment and its pods are running.

**💡 Note**: Infrastructure details, if needed, are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to the Jump Host

```bash
ssh thor@jump_host
```

**Purpose**: Connect to the jump host where `kubectl` is configured to manage the Kubernetes cluster.

---

## 🔹 Step 2: Create the Deployment

```bash
kubectl create deployment nginx --image=nginx:latest
```

**Purpose**: Create a deployment named `nginx` using the `nginx:latest` image.

**Expected Output**:
```
deployment.apps/nginx created
```

**Alternative YAML Approach** (for reference or manual application):
```bash
vi nginx-deployment.yaml
```

**Deployment YAML**:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:latest
```

**Save and Exit**: Press `Esc`, type `:wq`, press `Enter`.

**Apply YAML (if using)**:
```bash
kubectl apply -f nginx-deployment.yaml
```

**Key Configurations**:
- **apiVersion: apps/v1**: Specifies the API version for deployments.
- **kind: Deployment**: Defines the resource type as a deployment.
- **metadata.name: nginx**: Sets the deployment name to `nginx`.
- **spec.selector.matchLabels**: Matches pods with the label `app: nginx`.
- **spec.template.metadata.labels**: Applies the label `app: nginx` to pods.
- **spec.template.spec.containers**: Defines a single container:
  - `name: nginx`: Sets the container name (auto-generated as `nginx` by `kubectl create`).
  - `image: nginx:latest`: Uses the `nginx:latest` image from Docker Hub.

---

## 🔹 Step 3: Verify Deployment YAML (if using)

```bash
cat nginx-deployment.yaml
```

**Purpose**: Confirm the YAML file contains the correct configuration (if created manually).

**Validation Checklist**:
- ✅ `apiVersion: apps/v1` and `kind: Deployment`
- ✅ Deployment name: `nginx`
- ✅ Label: `app: nginx` in both `selector` and `template`
- ✅ Container name: `nginx`
- ✅ Image: `nginx:latest`
- ✅ Correct YAML indentation (2 spaces)

---

## 🔹 Step 4: Verify Deployment Creation

```bash
kubectl get deployments
```

**Purpose**: Confirm the `nginx` deployment is created and ready.

**Expected Output**:
```
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   1/1     1            1           10s
```

**Success Indicators**:
- ✅ Deployment name: `nginx`
- ✅ Ready: `1/1`
- ✅ Up-to-date: `1`
- ✅ Available: `1`

---

## 🔹 Step 5: Verify Pods

```bash
kubectl get pods -l app=nginx
```

**Purpose**: Check that the pods created by the deployment are running.

**Expected Output**:
```
NAME                     READY   STATUS    RESTARTS   AGE
nginx-abcdef123456-789   1/1     Running   0          10s
```

**Success Indicators**:
- ✅ Pod name starts with `nginx-`
- ✅ Status: `Running`
- ✅ Ready: `1/1`
- ✅ No restarts

---

## 🔹 Step 6: Verify Deployment Details

```bash
kubectl describe deployment nginx
```

**Purpose**: Check detailed information about the deployment, including labels and container status.

**Expected Output (Excerpt)**:
```
Name:                   nginx
Namespace:              default
Selector:               app=nginx
Labels:                 app=nginx
Replicas:               1 desired | 1 updated | 1 total
Pod Template:
  Labels:               app=nginx
  Containers:
   nginx:
    Image:              nginx:latest
    State:              Running
```

---

## 🔹 Step 7: Test Pod Accessibility (Optional)

```bash
kubectl port-forward deployment/nginx 8080:80
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

**Purpose**: Verify the Nginx container started correctly.

**Expected Logs**:
```
[notice] 1#1: start worker processes
[notice] 1#1: start worker process 32
```

---

## 🔹 Step 9: Clean Up (Optional)

```bash
kubectl delete deployment nginx
```

**Purpose**: Remove the deployment if testing is complete.

**Expected Output**:
```
deployment.apps "nginx" deleted
```

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **Jump Host**:

```bash
# Connect to jump host
ssh thor@jump_host

# Create deployment
kubectl create deployment nginx --image=nginx:latest

# Verify
kubectl get deployments
kubectl get pods -l app=nginx
kubectl describe deployment nginx
```

**Alternative YAML-based commands**:
```bash
# Create deployment YAML
cat > nginx-deployment.yaml << 'EOF'
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:latest
EOF

# Deploy and verify
kubectl apply -f nginx-deployment.yaml
kubectl get deployments
kubectl get pods -l app=nginx
```

---

## 💡 Common Kubernetes Deployment Issues & Fixes

### **Issue 1: Deployment Not Creating Pods**
**Problem**: `kubectl get pods -l app=nginx` shows no pods.
**Fix**: Check deployment events and selector labels.
```bash
kubectl describe deployment nginx
```

### **Issue 2: Image Pull Failure**
**Problem**: Pods in `ErrImagePull` or `ImagePullBackOff`.
**Fix**: Verify image name and tag.
```bash
docker pull nginx:latest
kubectl delete pod -l app=nginx
kubectl rollout restart deployment nginx
```

### **Issue 3: Pods in CrashLoopBackOff**
**Problem**: Pods restart repeatedly; `STATUS: CrashLoopBackOff`.
**Fix**: Check logs for errors.
```bash
kubectl logs -l app=nginx
kubectl describe pod -l app=nginx
```

### **Issue 4: Incorrect Selector Labels**
**Problem**: Deployment doesn’t manage pods due to label mismatch.
**Fix**: Verify selector and pod template labels.
```bash
kubectl get pods -l app=nginx
kubectl edit deployment nginx
```

---

## 🔧 Troubleshooting Deployment Failures

### **Error: Deployment Not Ready**
**Symptoms**: `kubectl get deployments` shows `0/1` ready.
**Solution**: Check pod status and events.
```bash
kubectl get pods -l app=nginx
kubectl get events --field-selector involvedObject.name=nginx
```

### **Error: Image Not Found**
**Symptoms**: "Failed to pull image nginx:latest".
**Solution**: Ensure image exists and cluster has registry access.
```bash
kubectl describe pod -l app=nginx
```

### **Error: YAML Syntax Error (if using YAML)**
**Symptoms**: `kubectl apply` fails with parsing error.
**Solution**: Validate YAML syntax.
```bash
kubectl apply -f nginx-deployment.yaml --dry-run=client
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was creating a Kubernetes deployment with precise specifications for the name and image, ensuring it deploys pods successfully in the cluster.

**💡 Solution Approach**:
1. **Deployment Creation**: Used `kubectl create deployment` for simplicity or defined `nginx-deployment.yaml` for manual control.
2. **Image Specification**: Used `nginx:latest` as required.
3. **Label Management**: Ensured `app: nginx` labels were applied automatically.
4. **Deployment**: Applied the configuration using `kubectl`.
5. **Verification**: Checked deployment and pod status with `kubectl get` and `kubectl describe`.

**🎯 Key Success Factors**:
- **Correct Command**: Used `kubectl create deployment nginx --image=nginx:latest` for quick setup.
- **Accurate Image**: Specified `nginx:latest` explicitly.
- **Label Consistency**: Ensured `app: nginx` for pod selection.
- **Verification**: Confirmed deployment and pod status.
- **Testing**: Optionally tested accessibility with `kubectl port-forward`.

**⚠️ Critical Deployment Fixes**:
- Use `apiVersion: apps/v1` for deployments (if using YAML).
- Ensure exact deployment name (`nginx`).
- Validate `nginx:latest` image tag.
- Check label selector (`app: nginx`) for pod association.

**🔒 Kubernetes Best Practices Applied**:
- **Simple Configuration**: Used minimal deployment settings for the task.
- **Label Usage**: Applied consistent labels for management.
- **Dry Run Testing**: Validated YAML with `--dry-run=client` (if using YAML).
- **Log Verification**: Checked container logs for startup confirmation.

**⚠️ Important Troubleshooting Concepts**:
- **Events**: Use `kubectl get events` to diagnose deployment issues.
- **Logs**: Check container logs for runtime errors.
- **Describe**: Use `kubectl describe` for detailed status.
- **Selector Validation**: Ensure deployment selector matches pod labels.

---

## ⚠️ Important Production Notes

🔧 **Deployment Debugging**: Use `kubectl describe` and `kubectl logs` for systematic issue resolution.
🔐 **Image Security**: Pin specific `nginx` versions (e.g., `nginx:1.25`) in production to avoid unexpected updates.
📊 **Resource Limits**: Add `resources` (e.g., CPU/memory limits) to the deployment for production stability.
🛡️ **Networking**: Expose the deployment via a service (e.g., ClusterIP or NodePort) for production access.

*Last Updated: October 25, 2025*