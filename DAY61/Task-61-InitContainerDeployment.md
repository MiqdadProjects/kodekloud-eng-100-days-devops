# 🌟 Task 61 - Deploy Application with Init Container in Kubernetes

**📌 Task Description**

The **Nautilus DevOps team** is preparing to deploy applications on a Kubernetes cluster, requiring pre-configuration tasks that cannot be performed inside the container images. To address this, they are testing the use of init containers to perform setup tasks before the main container starts. This task involves creating a deployment with an init container and a main container sharing a volume to exchange configuration data.

**👉 Task Requirements**:
- Create a deployment named `ic-deploy-datacenter`:
  - Replicas: `1`
  - Labels: `app: ic-datacenter` (in both `spec.selector.matchLabels` and `spec.template.metadata.labels`)
- Configure an init container:
  - Name: `ic-msg-datacenter`
  - Image: `debian:latest`
  - Command: `["/bin/bash", "-c", "echo Init Done - Welcome to xFusionCorp Industries > /ic/beta"]`
  - Volume mount: Name `ic-volume-datacenter`, mount path `/ic`
- Configure the main container:
  - Name: `ic-main-datacenter`
  - Image: `debian:latest`
  - Command: `["/bin/bash", "-c", "while true; do cat /ic/beta; sleep 5; done"]`
  - Volume mount: Name `ic-volume-datacenter`, mount path `/ic`
- Create a volume:
  - Name: `ic-volume-datacenter`
  - Type: `emptyDir`
- Verify the deployment and check logs using `kubectl logs -f` to confirm the output: `Init Done - Welcome to xFusionCorp Industries`.
- Use `kubectl` on the jump host, which is pre-configured to interact with the Kubernetes cluster.

**💡 Note**: Infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to the Jump Host

```bash
ssh thor@jump_host
```

**Purpose**: Connect to the jump host where `kubectl` is configured to manage the Kubernetes cluster.

---

## 🔹 Step 2: Create the Deployment YAML File

```bash
vi ic-deploy-datacenter.yaml
```

**Purpose**: Create a Kubernetes YAML file to define the `ic-deploy-datacenter` deployment with an init container, main container, and shared volume.

---

## 🔹 Step 3: Add Deployment YAML Content

**Deployment YAML**:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ic-deploy-datacenter
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ic-datacenter
  template:
    metadata:
      labels:
        app: ic-datacenter
    spec:
      initContainers:
      - name: ic-msg-datacenter
        image: debian:latest
        command: ["/bin/bash", "-c", "echo Init Done - Welcome to xFusionCorp Industries > /ic/beta"]
        volumeMounts:
        - name: ic-volume-datacenter
          mountPath: /ic
      containers:
      - name: ic-main-datacenter
        image: debian:latest
        command: ["/bin/bash", "-c", "while true; do cat /ic/beta; sleep 5; done"]
        volumeMounts:
        - name: ic-volume-datacenter
          mountPath: /ic
      volumes:
      - name: ic-volume-datacenter
        emptyDir: {}
```

**Key Configurations**:
- **apiVersion: apps/v1**: Specifies the API version for deployments.
- **kind: Deployment**: Defines the resource type as a deployment.
- **metadata.name: ic-deploy-datacenter**: Sets the deployment name.
- **spec.replicas: 1**: Runs one pod instance.
- **spec.selector.matchLabels: app: ic-datacenter**: Matches pods with the label `app: ic-datacenter`.
- **spec.template.metadata.labels: app: ic-datacenter**: Applies the label to pods.
- **spec.template.spec.initContainers**:
  - `name: ic-msg-datacenter`: Sets the init container name.
  - `image: debian:latest`: Uses the `debian:latest` image.
  - `command: ["/bin/bash", "-c", "echo Init Done - Welcome to xFusionCorp Industries > /ic/beta"]`: Writes a message to `/ic/beta`.
  - `volumeMounts`: Mounts `ic-volume-datacenter` at `/ic`.
- **spec.template.spec.containers**:
  - `name: ic-main-datacenter`: Sets the main container name.
  - `image: debian:latest`: Uses the `debian:latest` image.
  - `command: ["/bin/bash", "-c", "while true; do cat /ic/beta; sleep 5; done"]`: Continuously reads `/ic/beta` every 5 seconds.
  - `volumeMounts`: Mounts `ic-volume-datacenter` at `/ic`.
- **spec.template.spec.volumes**:
  - `name: ic-volume-datacenter`: Names the volume.
  - `emptyDir: {}`: Uses an `emptyDir` volume for temporary storage.

**Save and Exit**: Press `Esc`, type `:wq`, press `Enter`.

---

## 🔹 Step 4: Verify YAML Syntax

```bash
cat ic-deploy-datacenter.yaml
```

**Purpose**: Confirm the YAML file contains the correct configuration.

**Validation Checklist**:
- ✅ `apiVersion: apps/v1` and `kind: Deployment`
- ✅ Deployment name: `ic-deploy-datacenter`
- ✅ Replicas: `1`
- ✅ Labels: `app: ic-datacenter` in `selector` and `template.metadata`
- ✅ Init container: `name: ic-msg-datacenter`, `image: debian:latest`, correct command, volume mount `/ic`
- ✅ Main container: `name: ic-main-datacenter`, `image: debian:latest`, correct command, volume mount `/ic`
- ✅ Volume: `name: ic-volume-datacenter`, `emptyDir: {}`
- ✅ Correct YAML indentation (2 spaces)

---

## 🔹 Step 5: Apply the Deployment Configuration

```bash
kubectl apply -f ic-deploy-datacenter.yaml
```

**Purpose**: Deploy the `ic-deploy-datacenter` deployment to the Kubernetes cluster.

**Expected Output**:
```
deployment.apps/ic-deploy-datacenter created
```

---

## 🔹 Step 6: Verify Deployment and Pods

```bash
kubectl get deployments
kubectl get pods
```

**Purpose**: Confirm the deployment is running and pods are operational.

**Expected Deployment Output**:
```
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
ic-deploy-datacenter   1/1     1            1           30s
```

**Expected Pod Output**:
```
NAME                                  READY   STATUS    RESTARTS   AGE
ic-deploy-datacenter-xyz123456-abc789 1/1     Running   0          30s
```

**Success Indicators**:
- ✅ Deployment name: `ic-deploy-datacenter`
- ✅ Pod status: `Running`
- ✅ Ready: `1/1` for both deployment and pod
- ✅ No restarts

---

## 🔹 Step 7: Verify Pod Logs

```bash
kubectl logs -f -l app=ic-datacenter
```

**Purpose**: Check the main container’s logs to confirm it reads the init container’s message.

**Expected Output**:
```
Init Done - Welcome to xFusionCorp Industries
Init Done - Welcome to xFusionCorp Industries
Init Done - Welcome to xFusionCorp Industries
...
```

**Success Indicators**:
- ✅ Output matches: `Init Done - Welcome to xFusionCorp Industries`
- ✅ Output repeats every 5 seconds (due to `sleep 5`)

---

## 🔹 Step 8: Verify Pod Details

```bash
kubectl describe pod -l app=ic-datacenter
```

**Purpose**: Confirm the pod’s configuration, including init and main containers, volume mounts, and status.

**Expected Output (Excerpt)**:
```
Name:         ic-deploy-datacenter-xyz123456-abc789
Namespace:    default
Init Containers:
  ic-msg-datacenter:
    Image:        debian:latest
    Command:
      /bin/bash
      -c
      echo Init Done - Welcome to xFusionCorp Industries > /ic/beta
    State:        Terminated
      Reason:     Completed
    Volume Mounts:
      /ic from ic-volume-datacenter (rw)
Containers:
  ic-main-datacenter:
    Image:        debian:latest
    Command:
      /bin/bash
      -c
      while true; do cat /ic/beta; sleep 5; done
    State:        Running
    Volume Mounts:
      /ic from ic-volume-datacenter (rw)
Volumes:
  ic-volume-datacenter:
    Type:       EmptyDir
```

**Success Indicators**:
- ✅ Init container: `ic-msg-datacenter`, `debian:latest`, correct command, `State: Terminated (Completed)`
- ✅ Main container: `ic-main-datacenter`, `debian:latest`, correct command, `State: Running`
- ✅ Volume mounts: `/ic` for both containers
- ✅ Volume: `ic-volume-datacenter` as `EmptyDir`

---

## 🔹 Step 9: Verify File in Main Container (Optional)

```bash
kubectl exec -it $(kubectl get pods -l app=ic-datacenter -o name | head -n 1) -- cat /ic/beta
```

**Purpose**: Confirm the init container created the file `/ic/beta` in the shared volume.

**Expected Output**:
```
Init Done - Welcome to xFusionCorp Industries
```

---

## 🔹 Step 10: Clean Up (Optional)

```bash
kubectl delete -f ic-deploy-datacenter.yaml
```

**Purpose**: Remove the deployment if testing is complete.

**Expected Output**:
```
deployment.apps "ic-deploy-datacenter" deleted
```

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **Jump Host**:

```bash
# Connect to jump host
ssh thor@jump_host

# Create deployment YAML
cat > ic-deploy-datacenter.yaml << 'EOF'
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ic-deploy-datacenter
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ic-datacenter
  template:
    metadata:
      labels:
        app: ic-datacenter
    spec:
      initContainers:
      - name: ic-msg-datacenter
        image: debian:latest
        command: ["/bin/bash", "-c", "echo Init Done - Welcome to xFusionCorp Industries > /ic/beta"]
        volumeMounts:
        - name: ic-volume-datacenter
          mountPath: /ic
      containers:
      - name: ic-main-datacenter
        image: debian:latest
        command: ["/bin/bash", "-c", "while true; do cat /ic/beta; sleep 5; done"]
        volumeMounts:
        - name: ic-volume-datacenter
          mountPath: /ic
      volumes:
      - name: ic-volume-datacenter
        emptyDir: {}
EOF

# Deploy and verify
kubectl apply -f ic-deploy-datacenter.yaml
kubectl get deployments
kubectl get pods
kubectl logs -f -l app=ic-datacenter
kubectl describe pod -l app=ic-datacenter
```

---

## 💡 Common Kubernetes Deployment Issues & Fixes

### **Issue 1: Init Container Failure**
**Problem**: Init container fails, preventing main container from starting.
**Fix**: Check init container logs and command syntax.
```bash
kubectl logs -l app=ic-datacenter --container=ic-msg-datacenter
kubectl describe pod -l app=ic-datacenter
```

### **Issue 2: Image Pull Failure**
**Problem**: Pods in `ErrImagePull` or `ImagePullBackOff`.
**Fix**: Verify image name and tag.
```bash
docker pull debian:latest
kubectl rollout restart deployment ic-deploy-datacenter
```

### **Issue 3: Main Container Cannot Read File**
**Problem**: Main container logs show errors or no output for `/ic/beta`.
**Fix**: Verify volume mount and init container output.
```bash
kubectl exec -it $(kubectl get pods -l app=ic-datacenter -o name | head -n 1) -- ls -l /ic
kubectl describe pod -l app=ic-datacenter
```

### **Issue 4: Deployment Not Running**
**Problem**: `kubectl get deployments` shows `0/1` ready.
**Fix**: Check pod status and events.
```bash
kubectl get pods
kubectl get events --field-selector involvedObject.name=ic-deploy-datacenter
```

---

## 🔧 Troubleshooting Deployment Failures

### **Error: Init Container Not Completing**
**Symptoms**: Pod stuck in `Init:Error` or `Init:CrashLoopBackOff`.
**Solution**: Check init container logs and command.
```bash
kubectl logs -l app=ic-datacenter --container=ic-msg-datacenter
kubectl describe pod -l app=ic-datacenter
```

### **Error: Volume Not Shared**
**Symptoms**: Main container cannot access `/ic/beta`.
**Solution**: Verify volume mounts and `emptyDir` configuration.
```bash
kubectl describe pod -l app=ic-datacenter
```

### **Error: YAML Syntax Error**
**Symptoms**: `kubectl apply` fails with parsing error.
**Solution**: Validate YAML syntax.
```bash
kubectl apply -f ic-deploy-datacenter.yaml --dry-run=client
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was creating a deployment with an init container to write a configuration file to a shared `emptyDir` volume and a main container to read it continuously, ensuring proper volume mounts and command execution.

**💡 Solution Approach**:
1. **Deployment Creation**: Defined `ic-deploy-datacenter` with one replica and correct labels.
2. **Init Container**: Configured `ic-msg-datacenter` to write a message to `/ic/beta`.
3. **Main Container**: Configured `ic-main-datacenter` to read `/ic/beta` every 5 seconds.
4. **Volume Configuration**: Used `emptyDir` for `ic-volume-datacenter`, mounted at `/ic` in both containers.
5. **Verification**: Checked logs to confirm the expected output.

**🎯 Key Success Factors**:
- **Correct YAML**: Ensured proper labels, volume, and container configurations.
- **Init Container**: Successfully wrote the message to the shared volume.
- **Main Container**: Continuously read the message with correct output.
- **Verification**: Confirmed logs show `Init Done - Welcome to xFusionCorp Industries`.
- **Testing**: Validated pod status and volume mounts.

**⚠️ Critical Fixes**:
- Ensure exact command syntax for init and main containers.
- Use `debian:latest` for both containers.
- Mount `ic-volume-datacenter` at `/ic` in both containers.
- Verify `emptyDir` volume configuration.

**🔒 Kubernetes Best Practices Applied**:
- **Init Containers**: Used for pre-configuration tasks.
- **Shared Volumes**: Leveraged `emptyDir` for temporary data sharing.
- **Minimal Configuration**: Kept YAML simple with required fields.
- **Log Verification**: Used `kubectl logs -f` to confirm functionality.

**⚠️ Important Troubleshooting Concepts**:
- **Init Container Logs**: Check logs for init container failures.
- **Volume Mounts**: Verify mount paths align with volume configuration.
- **Events**: Use `kubectl get events` to diagnose deployment issues.
- **Exec Debugging**: Use `kubectl exec` to inspect files in the volume.

---

## ⚠️ Important Production Notes

🔧 **Deployment Debugging**: Use `kubectl describe`, `kubectl logs`, and `kubectl exec` for thorough issue resolution.
🔐 **Image Security**: Pin specific `debian` versions (e.g., `debian:11`) in production to avoid unexpected updates.
📊 **Resource Limits**: Add `resources` (e.g., CPU/memory limits) to prevent overuse.
🛡️ **Volume Management**: Use persistent volumes (e.g., PVCs) instead of `emptyDir` for production data.

*Last Updated: October 25, 2025*