# 🌟 Task 62 - Deploy Pod with Secret in Kubernetes

**📌 Task Description**

The **Nautilus DevOps team** is deploying tools on a Kubernetes cluster, some of which require securely storing license information using Kubernetes secrets. This task involves creating a generic secret from a file and deploying a pod that consumes the secret via a volume mount, allowing verification of the secret's contents.

**👉 Task Requirements**:
- Create a generic secret named `official`:
  - Source: File `/opt/official.txt` on the jump host (contains the password/license number, e.g., `5ecur3`).
- Create a pod named `secret-devops`:
  - Container name: `secret-container-devops`
  - Image: `debian:latest` (specify the tag explicitly)
  - Command: Use `sleep 3600` to keep the container running
  - Mount the secret as a volume at `/opt/cluster`
- Verify by exec-ing into the container to check the secret file at `/opt/cluster/official.txt`.
- Ensure the pod is in the `Running` state before validation.
- Use `kubectl` on the jump host, which is pre-configured to interact with the Kubernetes cluster.

**💡 Note**: Infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to the Jump Host

```bash
ssh thor@jump_host
```

**Purpose**: Connect to the jump host where `kubectl` is configured to manage the Kubernetes cluster.

---

## 🔹 Step 2: Verify Secret File Existence

```bash
cat /opt/official.txt
```

**Purpose**: Confirm the `/opt/official.txt` file exists and contains the secret (e.g., `5ecur3`).

**Expected Output**:
```
5ecur3
```

---

## 🔹 Step 3: Create the Secret

```bash
kubectl create secret generic official --from-file=/opt/official.txt
```

**Purpose**: Create a generic secret named `official` using the contents of `/opt/official.txt`.

**Expected Output**:
```
secret/official created
```

---

## 🔹 Step 4: Verify the Secret

```bash
kubectl get secrets
kubectl describe secret official
```

**Purpose**: Confirm the secret was created and contains the expected data.

**Expected `get secrets` Output**:
```
NAME       TYPE     DATA   AGE
official   Opaque   1      10s
```

**Expected `describe secret` Output (Excerpt)**:
```
Name:         official
Namespace:    default
Type:         Opaque
Data
====
official.txt:  7 bytes
```

**Success Indicators**:
- ✅ Secret name: `official`
- ✅ Type: `Opaque`
- ✅ Data: Contains `official.txt`

---

## 🔹 Step 5: Create the Pod YAML File

```bash
vi secret-pod.yaml
```

**Purpose**: Create a Kubernetes YAML file to define the `secret-devops` pod with the secret volume mount.

---

## 🔹 Step 6: Add Pod YAML Content

**Pod YAML**:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: secret-devops
spec:
  containers:
  - name: secret-container-devops
    image: debian:latest
    command: ["sleep", "3600"]
    volumeMounts:
    - name: secret-volume
      mountPath: /opt/cluster
  volumes:
  - name: secret-volume
    secret:
      secretName: official
```

**Key Configurations**:
- **apiVersion: v1**: Specifies the API version for pods.
- **kind: Pod**: Defines the resource type as a pod.
- **metadata.name: secret-devops**: Sets the pod name.
- **spec.containers**:
  - `name: secret-container-devops`: Sets the container name.
  - `image: debian:latest`: Uses the `debian:latest` image.
  - `command: ["sleep", "3600"]`: Keeps the container running for 1 hour.
  - `volumeMounts`: Mounts `secret-volume` at `/opt/cluster`.
- **spec.volumes**:
  - `name: secret-volume`: Names the volume.
  - `secret.secretName: official`: Links to the `official` secret.

**Save and Exit**: Press `Esc`, type `:wq`, press `Enter`.

---

## 🔹 Step 7: Verify YAML Syntax

```bash
cat secret-pod.yaml
```

**Purpose**: Confirm the YAML file contains the correct configuration.

**Validation Checklist**:
- ✅ `apiVersion: v1` and `kind: Pod`
- ✅ Pod name: `secret-devops`
- ✅ Container name: `secret-container-devops`
- ✅ Image: `debian:latest`
- ✅ Command: `["sleep", "3600"]`
- ✅ Volume mount: `name: secret-volume`, `mountPath: /opt/cluster`
- ✅ Volume: `name: secret-volume`, `secretName: official`
- ✅ Correct YAML indentation (2 spaces)

---

## 🔹 Step 8: Apply the Pod Configuration

```bash
kubectl apply -f secret-pod.yaml
```

**Purpose**: Deploy the `secret-devops` pod to the Kubernetes cluster.

**Expected Output**:
```
pod/secret-devops created
```

---

## 🔹 Step 9: Verify Pod Status

```bash
kubectl get pods
```

**Purpose**: Confirm the `secret-devops` pod is running.

**Expected Output**:
```
NAME            READY   STATUS    RESTARTS   AGE
secret-devops   1/1     Running   0          30s
```

**Success Indicators**:
- ✅ Pod name: `secret-devops`
- ✅ Status: `Running`
- ✅ Ready: `1/1`
- ✅ No restarts

---

## 🔹 Step 10: Verify Secret in Container

```bash
kubectl exec -it secret-devops -- /bin/bash
```

**Inside the container**:
```bash
ls /opt/cluster
cat /opt/cluster/official.txt
exit
```

**Expected Output**:
```
# ls /opt/cluster
official.txt
# cat /opt/cluster/official.txt
5ecur3
```

**Purpose**: Confirm the secret is mounted at `/opt/cluster/official.txt` and contains the expected content.

---

## 🔹 Step 11: Verify Pod Details (Optional)

```bash
kubectl describe pod secret-devops
```

**Purpose**: Check detailed information about the pod, including secret volume mount and container status.

**Expected Output (Excerpt)**:
```
Name:         secret-devops
Namespace:    default
Containers:
  secret-container-devops:
    Image:        debian:latest
    Command:
      sleep
      3600
    Volume Mounts:
      /opt/cluster from secret-volume (rw)
Volumes:
  secret-volume:
    Type:       Secret
    SecretName: official
```

**Success Indicators**:
- ✅ Container name: `secret-container-devops`
- ✅ Image: `debian:latest`
- ✅ Volume mount: `/opt/cluster` from `secret-volume`
- ✅ Volume: `Secret` with `SecretName: official`

---

## 🔹 Step 12: Clean Up (Optional)

```bash
kubectl delete -f secret-pod.yaml
kubectl delete secret official
```

**Purpose**: Remove the pod and secret if testing is complete.

**Expected Output**:
```
pod "secret-devops" deleted
secret "official" deleted
```

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **Jump Host**:

```bash
# Connect to jump host
ssh thor@jump_host

# Verify secret file
cat /opt/official.txt

# Create secret
kubectl create secret generic official --from-file=/opt/official.txt
kubectl get secrets
kubectl describe secret official

# Create pod YAML
cat > secret-pod.yaml << 'EOF'
apiVersion: v1
kind: Pod
metadata:
  name: secret-devops
spec:
  containers:
  - name: secret-container-devops
    image: debian:latest
    command: ["sleep", "3600"]
    volumeMounts:
    - name: secret-volume
      mountPath: /opt/cluster
  volumes:
  - name: secret-volume
    secret:
      secretName: official
EOF

# Deploy and verify
kubectl apply -f secret-pod.yaml
kubectl get pods
kubectl exec -it secret-devops -- /bin/bash -c 'ls /opt/cluster && cat /opt/cluster/official.txt'
```

---

## 💡 Common Kubernetes Secret/Pod Issues & Fixes

### **Issue 1: Secret Not Created**
**Problem**: `kubectl get secrets` does not show `official`.
**Fix**: Verify the file path and re-create the secret.
```bash
ls -l /opt/official.txt
kubectl create secret generic official --from-file=/opt/official.txt
```

### **Issue 2: Pod Not Running**
**Problem**: `kubectl get pods` shows `Error` or `CrashLoopBackOff`.
**Fix**: Check logs and pod events.
```bash
kubectl logs secret-devops
kubectl describe pod secret-devops
```

### **Issue 3: Secret Not Mounted**
**Problem**: `cat /opt/cluster/official.txt` shows no file or incorrect content.
**Fix**: Verify secret volume mount and secret name.
```bash
kubectl describe pod secret-devops
kubectl get secret official -o yaml
```

### **Issue 4: Image Pull Failure**
**Problem**: Pod in `ErrImagePull` or `ImagePullBackOff`.
**Fix**: Verify image name and tag.
```bash
docker pull debian:latest
kubectl delete pod secret-devops
kubectl apply -f secret-pod.yaml
```

---

## 🔧 Troubleshooting Pod/Secret Failures

### **Error: Pod Not Running**
**Symptoms**: `kubectl get pods` shows `Error` or `CrashLoopBackOff`.
**Solution**: Check logs and events.
```bash
kubectl logs secret-devops
kubectl get events --field-selector involvedObject.name=secret-devops
```

### **Error: Secret Mount Failure**
**Symptoms**: Secret file not found at `/opt/cluster`.
**Solution**: Verify secret and volume configuration.
```bash
kubectl describe secret official
kubectl describe pod secret-devops
```

### **Error: YAML Syntax Error**
**Symptoms**: `kubectl apply` fails with parsing error.
**Solution**: Validate YAML syntax.
```bash
kubectl apply -f secret-pod.yaml --dry-run=client
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was creating a Kubernetes secret from a file and mounting it in a pod, ensuring the secret is accessible at the specified path (`/opt/cluster/official.txt`) and the pod remains running for verification.

**💡 Solution Approach**:
1. **Secret Creation**: Used `kubectl create secret generic` to create the `official` secret from `/opt/official.txt`.
2. **Pod Configuration**: Defined `secret-devops` with `debian:latest`, mounting the secret at `/opt/cluster`.
3. **Deployment**: Applied the pod YAML using `kubectl apply`.
4. **Verification**: Confirmed pod status and secret content via `kubectl exec`.

**🎯 Key Success Factors**:
- **Correct Secret Creation**: Successfully created the `official` secret from the file.
- **Volume Mount**: Mounted the secret correctly at `/opt/cluster`.
- **Pod Stability**: Used `sleep 3600` to keep the container running.
- **Verification**: Confirmed secret content (`5ecur3`) in the container.
- **Testing**: Validated pod status and secret accessibility.

**⚠️ Critical Fixes**:
- Ensure `/opt/official.txt` exists and contains the correct data.
- Use `debian:latest` with explicit tag.
- Mount secret volume at `/opt/cluster`.
- Verify secret name (`official`) in the pod YAML.

**🔒 Kubernetes Best Practices Applied**:
- **Secure Storage**: Used Kubernetes secrets for sensitive data.
- **Minimal Configuration**: Kept YAML simple with required fields.
- **Verification**: Used `kubectl describe` and `kubectl exec` for validation.
- **Dry Run Testing**: Validated YAML with `--dry-run=client`.

**⚠️ Important Troubleshooting Concepts**:
- **Secret Verification**: Check `kubectl describe secret` for data integrity.
- **Pod Logs**: Use `kubectl logs` to diagnose container issues.
- **Events**: Use `kubectl get events` to identify pod or secret issues.
- **Exec Debugging**: Use `kubectl exec` to inspect mounted secrets.

---

## ⚠️ Important Production Notes

🔧 **Pod Debugging**: Use `kubectl describe`, `kubectl logs`, and `kubectl exec` for thorough issue resolution.
🔐 **Secret Security**: Restrict access to secrets with RBAC and namespace isolation in production.
📊 **Resource Limits**: Add `resources` (e.g., CPU/memory limits) to prevent overuse.
🛡️ **Secret Management**: Use external secret management tools (e.g., Vault) for production environments.

*Last Updated: October 25, 2025*