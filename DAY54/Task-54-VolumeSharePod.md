# 🌟 Task 54 - Create Pod with Shared Volume in Kubernetes

**📌 Task Description**

The **Nautilus DevOps team** is developing an application that requires multiple containers within a pod to share a volume for temporary data storage. The team needs to create a pod template to replicate this scenario on the Kubernetes cluster accessible from the jump host. The task involves creating a pod with two containers sharing an `emptyDir` volume, testing the shared volume by creating a file in one container, and verifying its presence in the other.

**👉 Task Requirements**:
- Create a pod named `volume-share-nautilus`.
- **First Container**:
  - Name: `volume-container-nautilus-1`
  - Image: `ubuntu:latest` (specify the tag explicitly)
  - Command: Run `sleep 3600` to keep the container running
  - Mount volume `volume-share` at `/tmp/blog`
- **Second Container**:
  - Name: `volume-container-nautilus-2`
  - Image: `ubuntu:latest` (specify the tag explicitly)
  - Command: Run `sleep 3600` to keep the container running
  - Mount volume `volume-share` at `/tmp/games`
- **Volume**:
  - Name: `volume-share`
  - Type: `emptyDir`
- Exec into `volume-container-nautilus-1`, create a file `blog.txt` with content under `/tmp/blog`.
- Verify the `blog.txt` file is accessible under `/tmp/games` in `volume-container-nautilus-2`.
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
vi volume-share-nautilus.yaml
```

**Purpose**: Create a Kubernetes YAML file to define the `volume-share-nautilus` pod with a shared volume.

---

## 🔹 Step 3: Add Pod YAML Content

**Pod YAML**:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: volume-share-nautilus
spec:
  containers:
    - name: volume-container-nautilus-1
      image: ubuntu:latest
      command: ["sleep", "3600"]
      volumeMounts:
        - name: volume-share
          mountPath: /tmp/blog
    - name: volume-container-nautilus-2
      image: ubuntu:latest
      command: ["sleep", "3600"]
      volumeMounts:
        - name: volume-share
          mountPath: /tmp/games
  volumes:
    - name: volume-share
      emptyDir: {}
```

**Key Configurations**:
- **apiVersion: v1**: Specifies the Kubernetes API version for pods.
- **kind: Pod**: Defines the resource type as a pod.
- **metadata.name: volume-share-nautilus**: Sets the pod name.
- **spec.containers**: Defines two containers:
  - **First Container**:
    - `name: volume-container-nautilus-1`: Sets the container name.
    - `image: ubuntu:latest`: Uses the `ubuntu:latest` image.
    - `command: ["sleep", "3600"]`: Keeps the container running for 1 hour.
    - `volumeMounts`: Mounts `volume-share` at `/tmp/blog`.
  - **Second Container**:
    - `name: volume-container-nautilus-2`: Sets the container name.
    - `image: ubuntu:latest`: Uses the `ubuntu:latest` image.
    - `command: ["sleep", "3600"]`: Keeps the container running for 1 hour.
    - `volumeMounts`: Mounts `volume-share` at `/tmp/games`.
- **spec.volumes**: Defines a shared volume:
  - `name: volume-share`: Names the volume.
  - `emptyDir: {}`: Uses an `emptyDir` volume for temporary storage.

**Save and Exit**: Press `Esc`, type `:wq`, press `Enter`.

---

## 🔹 Step 4: Verify Pod YAML Syntax

```bash
cat volume-share-nautilus.yaml
```

**Purpose**: Confirm the YAML file contains the correct configuration.

**Validation Checklist**:
- ✅ `apiVersion: v1` and `kind: Pod`
- ✅ Pod name: `volume-share-nautilus`
- ✅ Container names: `volume-container-nautilus-1` and `volume-container-nautilus-2`
- ✅ Image: `ubuntu:latest` for both containers
- ✅ Command: `["sleep", "3600"]` for both containers
- ✅ Volume mounts: `/tmp/blog` and `/tmp/games` for `volume-share`
- ✅ Volume: `volume-share` with `emptyDir: {}`
- ✅ Correct YAML indentation (2 spaces)

---

## 🔹 Step 5: Apply the Pod Configuration

```bash
kubectl apply -f volume-share-nautilus.yaml
```

**Purpose**: Deploy the pod to the Kubernetes cluster.

**Expected Output**:
```
pod/volume-share-nautilus created
```

---

## 🔹 Step 6: Verify Pod Creation

```bash
kubectl get pods
```

**Purpose**: Confirm the `volume-share-nautilus` pod is running with both containers.

**Expected Output**:
```
NAME                    READY   STATUS    RESTARTS   AGE
volume-share-nautilus   2/2     Running   0          30s
```

**Success Indicators**:
- ✅ Pod name: `volume-share-nautilus`
- ✅ Status: `Running`
- ✅ Ready: `2/2` (both containers)
- ✅ No restarts

---

## 🔹 Step 7: Verify Pod Details

```bash
kubectl describe pod volume-share-nautilus
```

**Purpose**: Check detailed information about the pod, including volume mounts and container status.

**Expected Output (Excerpt)**:
```
Name:         volume-share-nautilus
Namespace:    default
Containers:
  volume-container-nautilus-1:
    Image:        ubuntu:latest
    Command:
      sleep
      3600
    Volume Mounts:
      /tmp/blog from volume-share (rw)
    State:        Running
  volume-container-nautilus-2:
    Image:        ubuntu:latest
    Command:
      sleep
      3600
    Volume Mounts:
      /tmp/games from volume-share (rw)
    State:        Running
Volumes:
  volume-share:
    Type:       EmptyDir
```

**Success Indicators**:
- ✅ Correct container names and images
- ✅ Volume mounts at `/tmp/blog` and `/tmp/games`
- ✅ `volume-share` as `EmptyDir`
- ✅ Both containers in `Running` state

---

## 🔹 Step 8: Exec into First Container and Create File

```bash
kubectl exec -it volume-share-nautilus -c volume-container-nautilus-1 -- bash
```

**Inside the container**:
```bash
cd /tmp/blog
echo "Hello from container 1" > blog.txt
exit
```

**Purpose**: Create a `blog.txt` file in the shared volume at `/tmp/blog` in the first container.

---

## 🔹 Step 9: Verify File in Second Container

```bash
kubectl exec -it volume-share-nautilus -c volume-container-nautilus-2 -- bash
```

**Inside the container**:
```bash
cat /tmp/games/blog.txt
```

**Expected Output**:
```
Hello from container 1
```

**Purpose**: Confirm the `blog.txt` file is accessible in the second container at `/tmp/games`, verifying the shared volume.

**Exit the container**:
```bash
exit
```

---

## 🔹 Step 10: Check Container Logs (Optional)

```bash
kubectl logs volume-share-nautilus -c volume-container-nautilus-1
kubectl logs volume-share-nautilus -c volume-container-nautilus-2
```

**Purpose**: Verify both containers are running without errors.

**Expected Output**: Likely empty, as `sleep 3600` produces no logs unless interrupted.

---

## 🔹 Step 11: Clean Up (Optional)

```bash
kubectl delete -f volume-share-nautilus.yaml
```

**Purpose**: Remove the pod if testing is complete.

**Expected Output**:
```
pod "volume-share-nautilus" deleted
```

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **Jump Host**:

```bash
# Connect to jump host
ssh thor@jump_host

# Create pod YAML
cat > volume-share-nautilus.yaml << 'EOF'
apiVersion: v1
kind: Pod
metadata:
  name: volume-share-nautilus
spec:
  containers:
    - name: volume-container-nautilus-1
      image: ubuntu:latest
      command: ["sleep", "3600"]
      volumeMounts:
        - name: volume-share
          mountPath: /tmp/blog
    - name: volume-container-nautilus-2
      image: ubuntu:latest
      command: ["sleep", "3600"]
      volumeMounts:
        - name: volume-share
          mountPath: /tmp/games
  volumes:
    - name: volume-share
      emptyDir: {}
EOF

# Deploy and verify
kubectl apply -f volume-share-nautilus.yaml
kubectl get pods
kubectl describe pod volume-share-nautilus

# Create and verify file in shared volume
kubectl exec -it volume-share-nautilus -c volume-container-nautilus-1 -- bash -c 'cd /tmp/blog && echo "Hello from container 1" > blog.txt'
kubectl exec -it volume-share-nautilus -c volume-container-nautilus-2 -- cat /tmp/games/blog.txt
```

---

## 💡 Common Kubernetes Pod Issues & Fixes

### **Issue 1: Pod Stuck in Pending**
**Problem**: `kubectl get pods` shows `Pending` status.
**Fix**: Check events for scheduling issues.
```bash
kubectl describe pod volume-share-nautilus
```
**Common Causes**: Insufficient cluster resources or node issues.

### **Issue 2: Image Pull Failure**
**Problem**: Pods in `ErrImagePull` or `ImagePullBackOff`.
**Fix**: Verify image name and tag.
```bash
docker pull ubuntu:latest
kubectl delete pod volume-share-nautilus
kubectl apply -f volume-share-nautilus.yaml
```

### **Issue 3: File Not Shared**
**Problem**: `blog.txt` not visible in `volume-container-nautilus-2`.
**Fix**: Verify volume mount paths and `emptyDir` configuration.
```bash
kubectl describe pod volume-share-nautilus
kubectl exec -it volume-share-nautilus -c volume-container-nautilus-1 -- ls -l /tmp/blog
```

### **Issue 4: Exec Command Fails**
**Problem**: `kubectl exec` fails with "container not found" or "no shell".
**Fix**: Ensure container is running and has `bash`.
```bash
kubectl get pods
kubectl exec -it volume-share-nautilus -c volume-container-nautilus-1 -- bash
```

---

## 🔧 Troubleshooting Pod Failures

### **Error: Pod Not Running**
**Symptoms**: `kubectl get pods` shows `Error` or `CrashLoopBackOff`.
**Solution**: Check logs and events for both containers.
```bash
kubectl logs volume-share-nautilus -c volume-container-nautilus-1
kubectl logs volume-share-nautilus -c volume-container-nautilus-2
kubectl get events --field-selector involvedObject.name=volume-share-nautilus
```

### **Error: Volume Not Shared**
**Symptoms**: `blog.txt` not visible in `/tmp/games`.
**Solution**: Verify `volume-share` is mounted correctly in both containers.
```bash
kubectl describe pod volume-share-nautilus
```

### **Error: YAML Syntax Error**
**Symptoms**: `kubectl apply` fails with parsing error.
**Solution**: Validate YAML syntax.
```bash
kubectl apply -f volume-share-nautilus.yaml --dry-run=client
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was creating a pod with two containers sharing an `emptyDir` volume, ensuring the volume is mounted at different paths (`/tmp/blog` and `/tmp/games`), and verifying that a file created in one container is accessible in the other.

**💡 Solution Approach**:
1. **YAML Creation**: Defined `volume-share-nautilus.yaml` with two containers and a shared `emptyDir` volume.
2. **Volume Configuration**: Mounted `volume-share` at `/tmp/blog` and `/tmp/games` for the respective containers.
3. **Pod Deployment**: Applied the pod using `kubectl apply`.
4. **File Creation**: Created `blog.txt` in `volume-container-nautilus-1` at `/tmp/blog`.
5. **Verification**: Confirmed `blog.txt` is accessible in `volume-container-nautilus-2` at `/tmp/games`.

**🎯 Key Success Factors**:
- **Correct YAML Structure**: Ensured proper `apiVersion: v1`, container, and volume specifications.
- **Shared Volume**: Configured `emptyDir` volume correctly with consistent naming.
- **Mount Paths**: Used distinct mount paths (`/tmp/blog`, `/tmp/games`) for the shared volume.
- **Testing**: Validated file sharing by creating and reading `blog.txt`.
- **Verification**: Confirmed pod status and volume mounts with `kubectl describe`.

**⚠️ Critical Fixes**:
- Ensure `volume-share` is defined as `emptyDir` and mounted correctly.
- Use `["sleep", "3600"]` to keep containers running.
- Validate `ubuntu:latest` image availability.
- Confirm file creation and access in both containers.

**🔒 Kubernetes Best Practices Applied**:
- **Shared Volumes**: Used `emptyDir` for temporary data sharing between containers.
- **Minimal Configuration**: Kept YAML simple with only required fields.
- **Dry Run Testing**: Validated YAML with `--dry-run=client`.
- **Exec Verification**: Used `kubectl exec` to test volume sharing interactively.

**⚠️ Important Troubleshooting Concepts**:
- **Volume Mounts**: Verify mount paths align with volume configuration.
- **Logs**: Check container logs for runtime issues.
- **Events**: Use `kubectl get events` to diagnose pod issues.
- **Exec Debugging**: Use `kubectl exec` to inspect container filesystems.

---

## ⚠️ Important Production Notes

🔧 **Pod Debugging**: Use `kubectl describe`, `kubectl logs`, and `kubectl exec` for thorough issue resolution.
🔐 **Image Security**: Pin specific `ubuntu` versions (e.g., `ubuntu:20.04`) in production to avoid unexpected updates.
📊 **Resource Limits**: Add `resources` (e.g., CPU/memory limits) to prevent overuse in production.
🛡️ **Volume Management**: Use persistent volumes (e.g., PVCs) instead of `emptyDir` for production data.

*Last Updated: October 25, 2025*