# 🌟 Task 55 - Create Pod with Nginx and Sidecar for Log Aggregation in Kubernetes

**📌 Task Description**

The **Nautilus DevOps team** is managing a web server running the `nginx` image, with access and error logs that are not critical enough for persistent storage but need to be accessible for the last 24 hours for debugging purposes. To follow the separation of concerns principle, the team will implement the Sidecar pattern by deploying a second container to ship Nginx logs to a log-aggregation service. Both containers will share an `emptyDir` volume to access logs, with Nginx serving web pages and the sidecar handling log shipping.

**👉 Task Requirements**:
- Create a pod named `webserver`.
- Define an `emptyDir` volume named `shared-logs`.
- Create two containers:
  - **First Container**:
    - Name: `nginx-container`
    - Image: `nginx:latest` (specify the tag explicitly)
    - Mount volume `shared-logs` at `/var/log/nginx`
  - **Second Container (Sidecar)**:
    - Name: `sidecar-container`
    - Image: `ubuntu:latest` (specify the tag explicitly)
    - Command: `["sh", "-c", "while true; do cat /var/log/nginx/access.log /var/log/nginx/error.log; sleep 30; done"]`
    - Mount volume `shared-logs` at `/var/log/nginx`
- Ensure both containers are running.
- Use `kubectl` on the jump host, which is pre-configured to interact with the Kubernetes cluster.

**💡 Note**: Infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to the Jump Host

```bash
ssh thor@jump_host
```

**Purpose**: Connect to the jump host where `kubectl` is configured to manage the Kubernetes cluster.

---

## 🔹 Step 2: Create the Pod YAML File

```bash
vi webserver.yaml
```

**Purpose**: Create a Kubernetes YAML file to define the `webserver` pod with a shared `emptyDir` volume and two containers.

---

## 🔹 Step 3: Add Pod YAML Content

**Pod YAML**:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: webserver
spec:
  containers:
    - name: nginx-container
      image: nginx:latest
      volumeMounts:
        - name: shared-logs
          mountPath: /var/log/nginx
    - name: sidecar-container
      image: ubuntu:latest
      command: ["sh", "-c", "while true; do cat /var/log/nginx/access.log /var/log/nginx/error.log; sleep 30; done"]
      volumeMounts:
        - name: shared-logs
          mountPath: /var/log/nginx
  volumes:
    - name: shared-logs
      emptyDir: {}
```

**Key Configurations**:
- **apiVersion: v1**: Specifies the Kubernetes API version for pods.
- **kind: Pod**: Defines the resource type as a pod.
- **metadata.name: webserver**: Sets the pod name.
- **spec.containers**: Defines two containers:
  - **First Container (Nginx)**:
    - `name: nginx-container`: Sets the container name.
    - `image: nginx:latest`: Uses the `nginx:latest` image.
    - `volumeMounts`: Mounts `shared-logs` at `/var/log/nginx`.
  - **Second Container (Sidecar)**:
    - `name: sidecar-container`: Sets the container name.
    - `image: ubuntu:latest`: Uses the `ubuntu:latest` image.
    - `command: ["sh", "-c", "while true; do cat /var/log/nginx/access.log /var/log/nginx/error.log; sleep 30; done"]`: Continuously reads Nginx logs every 30 seconds.
    - `volumeMounts`: Mounts `shared-logs` at `/var/log/nginx`.
- **spec.volumes**: Defines a shared volume:
  - `name: shared-logs`: Names the volume.
  - `emptyDir: {}`: Uses an `emptyDir` volume for temporary log storage.

**Save and Exit**: Press `Esc`, type `:wq`, press `Enter`.

---

## 🔹 Step 4: Verify Pod YAML Syntax

```bash
cat webserver.yaml
```

**Purpose**: Confirm the YAML file contains the correct configuration.

**Validation Checklist**:
- ✅ `apiVersion: v1` and `kind: Pod`
- ✅ Pod name: `webserver`
- ✅ Container names: `nginx-container` and `sidecar-container`
- ✅ Images: `nginx:latest` and `ubuntu:latest`
- ✅ Command for `sidecar-container`: `["sh", "-c", "while true; do cat /var/log/nginx/access.log /var/log/nginx/error.log; sleep 30; done"]`
- ✅ Volume mounts: `/var/log/nginx` for both containers
- ✅ Volume: `shared-logs` with `emptyDir: {}`
- ✅ Correct YAML indentation (2 spaces)

---

## 🔹 Step 5: Apply the Pod Configuration

```bash
kubectl apply -f webserver.yaml
```

**Purpose**: Deploy the pod to the Kubernetes cluster.

**Expected Output**:
```
pod/webserver created
```

---

## 🔹 Step 6: Verify Pod Creation

```bash
kubectl get pods
```

**Purpose**: Confirm the `webserver` pod is running with both containers.

**Expected Output**:
```
NAME        READY   STATUS    RESTARTS   AGE
webserver   2/2     Running   0          30s
```

**Success Indicators**:
- ✅ Pod name: `webserver`
- ✅ Status: `Running`
- ✅ Ready: `2/2` (both `nginx-container` and `sidecar-container`)
- ✅ No restarts

---

## 🔹 Step 7: Verify Pod Details

```bash
kubectl describe pod webserver
```

**Purpose**: Check detailed information about the pod, including volume mounts and container status.

**Expected Output (Excerpt)**:
```
Name:         webserver
Namespace:    default
Containers:
  nginx-container:
    Image:        nginx:latest
    Volume Mounts:
      /var/log/nginx from shared-logs (rw)
    State:        Running
  sidecar-container:
    Image:        ubuntu:latest
    Command:
      sh
      -c
      while true; do cat /var/log/nginx/access.log /var/log/nginx/error.log; sleep 30; done
    Volume Mounts:
      /var/log/nginx from shared-logs (rw)
    State:        Running
Volumes:
  shared-logs:
    Type:       EmptyDir
```

**Success Indicators**:
- ✅ Correct container names and images
- ✅ Volume mounts at `/var/log/nginx` for both containers
- ✅ `shared-logs` as `EmptyDir`
- ✅ Both containers in `Running` state

---

## 🔹 Step 8: Test Nginx Accessibility (Optional)

```bash
kubectl port-forward pod/webserver 8080:80
```

**Purpose**: Forward the Nginx container’s port 80 to localhost:8080 to test the web server.

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

## 🔹 Step 9: Verify Sidecar Log Shipping

```bash
kubectl logs webserver -c sidecar-container
```

**Purpose**: Confirm the sidecar container is reading Nginx logs from the shared volume.

**Expected Output** (example):
```
172.17.0.1 - - [25/Oct/2025:15:55:00 +0000] "GET / HTTP/1.1" 200 612 "-" "curl/7.68.0"
[25-Oct-2025 15:55:00] [error] 6#6: *1 open() "/usr/share/nginx/html/favicon.ico" failed
```

**Success Indicators**:
- ✅ Logs from `access.log` and `error.log` are displayed
- ✅ Output updates every 30 seconds (due to `sleep 30`)

---

## 🔹 Step 10: Check Nginx Logs (Optional)

```bash
kubectl exec -it webserver -c nginx-container -- cat /var/log/nginx/access.log
kubectl exec -it webserver -c nginx-container -- cat /var/log/nginx/error.log
```

**Purpose**: Verify Nginx is writing logs to the shared volume.

**Expected Output** (example):
```
# access.log
172.17.0.1 - - [25/Oct/2025:15:55:00 +0000] "GET / HTTP/1.1" 200 612 "-" "curl/7.68.0"
# error.log
[25-Oct-2025 15:55:00] [error] 6#6: *1 open() "/usr/share/nginx/html/favicon.ico" failed
```

---

## 🔹 Step 11: Clean Up (Optional)

```bash
kubectl delete -f webserver.yaml
```

**Purpose**: Remove the pod if testing is complete.

**Expected Output**:
```
pod "webserver" deleted
```

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **Jump Host**:

```bash
# Connect to jump host
ssh thor@jump_host

# Create pod YAML
cat > webserver.yaml << 'EOF'
apiVersion: v1
kind: Pod
metadata:
  name: webserver
spec:
  containers:
    - name: nginx-container
      image: nginx:latest
      volumeMounts:
        - name: shared-logs
          mountPath: /var/log/nginx
    - name: sidecar-container
      image: ubuntu:latest
      command: ["sh", "-c", "while true; do cat /var/log/nginx/access.log /var/log/nginx/error.log; sleep 30; done"]
      volumeMounts:
        - name: shared-logs
          mountPath: /var/log/nginx
  volumes:
    - name: shared-logs
      emptyDir: {}
EOF

# Deploy and verify
kubectl apply -f webserver.yaml
kubectl get pods
kubectl describe pod webserver
kubectl logs webserver -c sidecar-container
```

---

## 💡 Common Kubernetes Pod Issues & Fixes

### **Issue 1: Pod Stuck in Pending**
**Problem**: `kubectl get pods` shows `Pending` status.
**Fix**: Check events for scheduling issues.
```bash
kubectl describe pod webserver
```
**Common Causes**: Insufficient resources or node issues.

### **Issue 2: Image Pull Failure**
**Problem**: Pods in `ErrImagePull` or `ImagePullBackOff`.
**Fix**: Verify image names and tags.
```bash
docker pull nginx:latest
docker pull ubuntu:latest
kubectl delete pod webserver
kubectl apply -f webserver.yaml
```

### **Issue 3: Sidecar Container Fails**
**Problem**: `sidecar-container` in `CrashLoopBackOff` or `Error`.
**Fix**: Check logs for errors; ensure log files exist.
```bash
kubectl logs webserver -c sidecar-container
kubectl exec -it webserver -c nginx-container -- ls -l /var/log/nginx
```

### **Issue 4: Logs Not Accessible**
**Problem**: Sidecar cannot read `access.log` or `error.log`.
**Fix**: Verify volume mount paths and permissions.
```bash
kubectl describe pod webserver
kubectl exec -it webserver -c nginx-container -- ls -l /var/log/nginx
```

---

## 🔧 Troubleshooting Pod Failures

### **Error: Pod Not Running**
**Symptoms**: `kubectl get pods` shows `Error` or `CrashLoopBackOff`.
**Solution**: Check logs and events for both containers.
```bash
kubectl logs webserver -c nginx-container
kubectl logs webserver -c sidecar-container
kubectl get events --field-selector involvedObject.name=webserver
```

### **Error: Volume Not Shared**
**Symptoms**: Sidecar cannot access Nginx logs.
**Solution**: Verify `shared-logs` is mounted correctly in both containers.
```bash
kubectl describe pod webserver
```

### **Error: Sidecar Command Fails**
**Symptoms**: Sidecar container fails due to missing `sh` or log files.
**Solution**: Ensure `ubuntu:latest` has `sh` and logs are generated.
```bash
kubectl exec -it webserver -c sidecar-container -- sh -c 'which sh'
kubectl exec -it webserver -c nginx-container -- touch /var/log/nginx/access.log
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was implementing the Sidecar pattern to create a pod with an Nginx container and a sidecar container for log aggregation, using a shared `emptyDir` volume to access Nginx logs, and ensuring both containers run correctly.

**💡 Solution Approach**:
1. **YAML Creation**: Defined `webserver.yaml` with two containers and a shared `emptyDir` volume.
2. **Volume Configuration**: Mounted `shared-logs` at `/var/log/nginx` for both containers.
3. **Sidecar Command**: Configured the sidecar to continuously read logs with a 30-second interval.
4. **Pod Deployment**: Applied the pod using `kubectl apply`.
5. **Verification**: Confirmed pod status, volume mounts, and log access with `kubectl describe` and `kubectl logs`.

**🎯 Key Success Factors**:
- **Correct YAML Structure**: Ensured proper `apiVersion: v1`, container, and volume specifications.
- **Shared Volume**: Configured `emptyDir` volume correctly with consistent mount paths.
- **Sidecar Command**: Used correct command syntax for log reading.
- **Verification**: Confirmed both containers running and logs accessible.
- **Testing**: Validated Nginx accessibility and sidecar log output.

**⚠️ Critical Fixes**:
- Ensure `shared-logs` is defined as `emptyDir` and mounted at `/var/log/nginx`.
- Use exact command `["sh", "-c", "while true; do cat /var/log/nginx/access.log /var/log/nginx/error.log; sleep 30; done"]` for the sidecar.
- Validate `nginx:latest` and `ubuntu:latest` image availability.
- Confirm logs are generated and accessible in the shared volume.

**🔒 Kubernetes Best Practices Applied**:
- **Sidecar Pattern**: Separated concerns with Nginx for web serving and sidecar for log shipping.
- **Shared Volumes**: Used `emptyDir` for temporary log storage.
- **Minimal Configuration**: Kept YAML simple with only required fields.
- **Log Verification**: Checked sidecar logs to confirm functionality.

**⚠️ Important Troubleshooting Concepts**:
- **Volume Mounts**: Verify mount paths align with Nginx log directory.
- **Logs**: Check both containers’ logs for errors.
- **Events**: Use `kubectl get events` to diagnose pod issues.
- **Exec Debugging**: Use `kubectl exec` to inspect container filesystems.

---

## ⚠️ Important Production Notes

🔧 **Pod Debugging**: Use `kubectl describe`, `kubectl logs`, and `kubectl exec` for thorough issue resolution.
🔐 **Image Security**: Pin specific image versions (e.g., `nginx:1.25`, `ubuntu:20.04`) in production to avoid unexpected updates.
📊 **Resource Limits**: Add `resources` (e.g., CPU/memory limits) to prevent overuse in production.
🛡️ **Log Aggregation**: Integrate with a production log-aggregation service (e.g., Fluentd, ELK) instead of `cat` for log shipping.

*Last Updated: October 25, 2025*