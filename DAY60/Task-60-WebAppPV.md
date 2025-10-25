# 🌟 Task 60 - Deploy Web Application with Persistent Volume in Kubernetes

**📌 Task Description**

The **Nautilus DevOps team** is developing a Kubernetes template to deploy a web application with persistent storage for the application code. The task involves creating a PersistentVolume (PV), a PersistentVolumeClaim (PVC), a pod running an Apache web server, and a NodePort service to expose the application, ensuring the web server’s document root uses the persistent volume.

**👉 Task Requirements**:
- Create a PersistentVolume named `pv-nautilus`:
  - Storage class: `manual`
  - Capacity: `3Gi`
  - Access mode: `ReadWriteOnce`
  - Volume type: `hostPath`
  - Path: `/mnt/devops` (pre-created, no direct access needed)
- Create a PersistentVolumeClaim named `pvc-nautilus`:
  - Storage class: `manual`
  - Request: `1Gi`
  - Access mode: `ReadWriteOnce`
- Create a pod named `pod-nautilus`:
  - Container name: `container-nautilus`
  - Image: `httpd:latest` (specify the tag explicitly)
  - Mount the PVC `pvc-nautilus` at the web server’s document root (`/usr/local/apache2/htdocs`)
- Create a NodePort service named `web-nautilus`:
  - NodePort: `30008`
  - Selector: Match the pod’s labels
- Verify the web server is accessible via the **Website** button in the KodeKloud lab interface, displaying content (e.g., `Index of /`).
- Use `kubectl` on the jump host, which is pre-configured to interact with the Kubernetes cluster.

**💡 Note**: Infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to the Jump Host

```bash
ssh thor@jump_host
```

**Purpose**: Connect to the jump host where `kubectl` is configured to manage the Kubernetes cluster.

---

## 🔹 Step 2: Create the YAML File

```bash
vi nautilus-pv-pvc-pod-service.yaml
```

**Purpose**: Create a single Kubernetes YAML file to define the PersistentVolume, PersistentVolumeClaim, pod, and NodePort service.

---

## 🔹 Step 3: Add YAML Content

**YAML Content**:

```yaml
# Persistent Volume
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nautilus
spec:
  capacity:
    storage: 3Gi
  accessModes:
    - ReadWriteOnce
  storageClassName: manual
  hostPath:
    path: /mnt/devops

---
# Persistent Volume Claim
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-nautilus
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi

---
# Pod using PVC
apiVersion: v1
kind: Pod
metadata:
  name: pod-nautilus
  labels:
    app: pod-nautilus
spec:
  containers:
  - name: container-nautilus
    image: httpd:latest
    volumeMounts:
    - mountPath: /usr/local/apache2/htdocs
      name: nautilus-storage
  volumes:
  - name: nautilus-storage
    persistentVolumeClaim:
      claimName: pvc-nautilus

---
# NodePort Service
apiVersion: v1
kind: Service
metadata:
  name: web-nautilus
spec:
  type: NodePort
  selector:
    app: pod-nautilus
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
    nodePort: 30008
```

**Key Configurations**:
- **PersistentVolume (`pv-nautilus`)**:
  - `apiVersion: v1`, `kind: PersistentVolume`: Defines a PV.
  - `metadata.name: pv-nautilus`: Sets the PV name.
  - `spec.capacity.storage: 3Gi`: Allocates 3Gi storage.
  - `spec.accessModes: ReadWriteOnce`: Allows one node to read/write.
  - `spec.storageClassName: manual`: Uses manual storage class.
  - `spec.hostPath.path: /mnt/devops`: Uses the pre-created host path.
- **PersistentVolumeClaim (`pvc-nautilus`)**:
  - `apiVersion: v1`, `kind: PersistentVolumeClaim`: Defines a PVC.
  - `metadata.name: pvc-nautilus`: Sets the PVC name.
  - `spec.storageClassName: manual`: Matches PV’s storage class.
  - `spec.accessModes: ReadWriteOnce`: Matches PV’s access mode.
  - `spec.resources.requests.storage: 1Gi`: Requests 1Gi from the PV.
- **Pod (`pod-nautilus`)**:
  - `apiVersion: v1`, `kind: Pod`: Defines a pod.
  - `metadata.name: pod-nautilus`: Sets the pod name.
  - `metadata.labels.app: pod-nautilus`: Labels the pod for service selector.
  - `spec.containers`:
    - `name: container-nautilus`: Sets the container name.
    - `image: httpd:latest`: Uses Apache web server image.
    - `volumeMounts`: Mounts `nautilus-storage` at `/usr/local/apache2/htdocs` (Apache document root).
  - `spec.volumes`: Links the PVC `pvc-nautilus` to the volume `nautilus-storage`.
- **Service (`web-nautilus`)**:
  - `apiVersion: v1`, `kind: Service`: Defines a service.
  - `metadata.name: web-nautilus`: Sets the service name.
  - `spec.type: NodePort`: Exposes the service on a node port.
  - `spec.selector.app: pod-nautilus`: Targets the pod’s label.
  - `spec.ports`: Maps port `80` to `targetPort: 80` and `nodePort: 30008`.

**Save and Exit**: Press `Esc`, type `:wq`, press `Enter`.

---

## 🔹 Step 4: Verify YAML Syntax

```bash
cat nautilus-pv-pvc-pod-service.yaml
```

**Purpose**: Confirm the YAML file contains the correct configurations.

**Validation Checklist**:
- **PersistentVolume**:
  - ✅ `name: pv-nautilus`
  - ✅ `storage: 3Gi`, `accessModes: ReadWriteOnce`, `storageClassName: manual`, `hostPath: /mnt/devops`
- **PersistentVolumeClaim**:
  - ✅ `name: pvc-nautilus`
  - ✅ `storage: 1Gi`, `accessModes: ReadWriteOnce`, `storageClassName: manual`
- **Pod**:
  - ✅ `name: pod-nautilus`, `labels.app: pod-nautilus`
  - ✅ Container: `name: container-nautilus`, `image: httpd:latest`
  - ✅ Volume mount: `/usr/local/apache2/htdocs`, `name: nautilus-storage`
  - ✅ Volume: `persistentVolumeClaim.claimName: pvc-nautilus`
- **Service**:
  - ✅ `name: web-nautilus`, `type: NodePort`
  - ✅ `selector.app: pod-nautilus`
  - ✅ Ports: `port: 80`, `targetPort: 80`, `nodePort: 30008`
- ✅ Correct YAML indentation (2 spaces) and valid separators (`---`)

---

## 🔹 Step 5: Apply the Configurations

```bash
kubectl apply -f nautilus-pv-pvc-pod-service.yaml
```

**Purpose**: Deploy the PersistentVolume, PersistentVolumeClaim, pod, and NodePort service to the Kubernetes cluster.

**Expected Output**:
```
persistentvolume/pv-nautilus created
persistentvolumeclaim/pvc-nautilus created
pod/pod-nautilus created
service/web-nautilus created
```

---

## 🔹 Step 6: Verify PersistentVolume

```bash
kubectl get pv
```

**Purpose**: Confirm the `pv-nautilus` is created and bound.

**Expected Output**:
```
NAME          CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                 STORAGECLASS   AGE
pv-nautilus   3Gi        RWO            Retain           Bound    default/pvc-nautilus  manual         30s
```

**Success Indicators**:
- ✅ Name: `pv-nautilus`
- ✅ Status: `Bound`
- ✅ Claim: `default/pvc-nautilus`
- ✅ StorageClass: `manual`

---

## 🔹 Step 7: Verify PersistentVolumeClaim

```bash
kubectl get pvc
```

**Purpose**: Confirm the `pvc-nautilus` is bound to the PV.

**Expected Output**:
```
NAME           STATUS   VOLUME        CAPACITY   ACCESS MODES   STORAGECLASS   AGE
pvc-nautilus   Bound    pv-nautilus   3Gi        RWO            manual         30s
```

**Success Indicators**:
- ✅ Name: `pvc-nautilus`
- ✅ Status: `Bound`
- ✅ Volume: `pv-nautilus`

---

## 🔹 Step 8: Verify Pod Status

```bash
kubectl get pod -o wide
```

**Purpose**: Confirm the `pod-nautilus` is running.

**Expected Output**:
```
NAME           READY   STATUS    RESTARTS   AGE   IP           NODE
pod-nautilus   1/1     Running   0          30s   10.0.0.5     node01
```

**Success Indicators**:
- ✅ Name: `pod-nautilus`
- ✅ Status: `Running`
- ✅ Ready: `1/1`
- ✅ No restarts

---

## 🔹 Step 9: Verify Service

```bash
kubectl get svc
```

**Purpose**: Confirm the `web-nautilus` service is created with the correct NodePort.

**Expected Output**:
```
NAME           TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
web-nautilus   NodePort   10.96.123.456   <none>        80:30008/TCP   30s
```

**Success Indicators**:
- ✅ Name: `web-nautilus`
- ✅ Type: `NodePort`
- ✅ Port mapping: `80:30008/TCP`

---

## 🔹 Step 10: Access Web Server

**Action**: In the KodeKloud lab interface, click the **Website** button to access the Apache web server.

**Purpose**: Verify the web server is accessible via the NodePort (30008).

**Expected Output** (via browser):
```
Index of /
```

**Note**: The default Apache directory listing (`Index of /`) appears because no `index.html` or other files are present in `/mnt/devops`.

**Alternative CLI Test** (if no Website button is available):
```bash
kubectl get nodes -o wide
```

**Note the node’s external IP** (e.g., `192.168.1.100`), then:
```bash
curl http://<node-ip>:30008
```

**Expected Output**:
```
<html><body><h1>Index of /</h1>...</body></html>
```

---

## 🔹 Step 11: Verify Pod Details (Optional)

```bash
kubectl describe pod pod-nautilus
```

**Purpose**: Confirm the pod’s configuration, including PVC mount and container status.

**Expected Output (Excerpt)**:
```
Name:         pod-nautilus
Namespace:    default
Containers:
  container-nautilus:
    Image:        httpd:latest
    Volume Mounts:
      /usr/local/apache2/htdocs from nautilus-storage (rw)
Volumes:
  nautilus-storage:
    Type:       PersistentVolumeClaim
    ClaimName:  pvc-nautilus
```

**Success Indicators**:
- ✅ Image: `httpd:latest`
- ✅ Volume mount: `/usr/local/apache2/htdocs`
- ✅ PVC: `pvc-nautilus`

---

## 🔹 Step 12: Check Apache Logs (Optional)

```bash
kubectl logs pod-nautilus
```

**Purpose**: Verify the Apache container started correctly.

**Expected Logs** (example):
```
[Sat Oct 25 16:00:00.000000 2025] [mpm_event:notice] [pid 1] AH00489: Apache/2.4.62 (Unix) configured
```

---

## 🔹 Step 13: Clean Up (Optional)

```bash
kubectl delete -f nautilus-pv-pvc-pod-service.yaml
```

**Purpose**: Remove the PV, PVC, pod, and service if testing is complete.

**Expected Output**:
```
persistentvolume "pv-nautilus" deleted
persistentvolumeclaim "pvc-nautilus" deleted
pod "pod-nautilus" deleted
service "web-nautilus" deleted
```

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **Jump Host**:

```bash
# Connect to jump host
ssh thor@jump_host

# Create YAML file
cat > nautilus-pv-pvc-pod-service.yaml << 'EOF'
# Persistent Volume
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nautilus
spec:
  capacity:
    storage: 3Gi
  accessModes:
    - ReadWriteOnce
  storageClassName: manual
  hostPath:
    path: /mnt/devops
---
# Persistent Volume Claim
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-nautilus
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
---
# Pod using PVC
apiVersion: v1
kind: Pod
metadata:
  name: pod-nautilus
  labels:
    app: pod-nautilus
spec:
  containers:
  - name: container-nautilus
    image: httpd:latest
    volumeMounts:
    - mountPath: /usr/local/apache2/htdocs
      name: nautilus-storage
  volumes:
  - name: nautilus-storage
    persistentVolumeClaim:
      claimName: pvc-nautilus
---
# NodePort Service
apiVersion: v1
kind: Service
metadata:
  name: web-nautilus
spec:
  type: NodePort
  selector:
    app: pod-nautilus
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
    nodePort: 30008
EOF

# Deploy and verify
kubectl apply -f nautilus-pv-pvc-pod-service.yaml
kubectl get pv
kubectl get pvc
kubectl get pod -o wide
kubectl get svc
```

---

## 💡 Common Kubernetes PV/PVC/Pod/Service Issues & Fixes

### **Issue 1: PV Not Bound**
**Problem**: `kubectl get pv` shows `pv-nautilus` as `Available`, not `Bound`.
**Fix**: Verify PVC configuration matches PV.
```bash
kubectl describe pv pv-nautilus
kubectl describe pvc pvc-nautilus
```

### **Issue 2: PVC Not Bound**
**Problem**: `kubectl get pvc` shows `pvc-nautilus` as `Pending`.
**Fix**: Ensure PV exists and matches storage class and access mode.
```bash
kubectl get pv
kubectl edit pvc pvc-nautilus
```

### **Issue 3: Pod Not Running**
**Problem**: `kubectl get pod` shows `Error` or `CrashLoopBackOff`.
**Fix**: Check logs and events.
```bash
kubectl logs pod-nautilus
kubectl describe pod pod-nautilus
```

### **Issue 4: Service Not Accessible**
**Problem**: Accessing `<node-ip>:30008` fails.
**Fix**: Verify service selector and pod labels.
```bash
kubectl describe svc web-nautilus
kubectl get pod -l app=pod-nautilus
```

---

## 🔧 Troubleshooting Failures

### **Error: Pod Not Running**
**Symptoms**: `kubectl get pod` shows `Error` or `CrashLoopBackOff`.
**Solution**: Check logs and events.
```bash
kubectl logs pod-nautilus
kubectl get events --field-selector involvedObject.name=pod-nautilus
```

### **Error: Volume Mount Failure**
**Symptoms**: Pod fails with volume mount error.
**Solution**: Verify PVC and PV configurations.
```bash
kubectl describe pod pod-nautilus
kubectl describe pvc pvc-nautilus
```

### **Error: Service Not Routing**
**Symptoms**: Accessing `<node-ip>:30008` returns no response.
**Solution**: Check service endpoints and pod selector.
```bash
kubectl get endpoints web-nautilus
kubectl describe svc web-nautilus
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was creating a PersistentVolume, PersistentVolumeClaim, pod, and NodePort service, ensuring the PV is correctly bound to the PVC, the pod mounts the volume at Apache’s document root, and the service exposes the web server on port 30008.

**💡 Solution Approach**:
1. **PV and PVC Creation**: Defined `pv-nautilus` (3Gi, `hostPath`) and `pvc-nautilus` (1Gi, `manual`).
2. **Pod Configuration**: Created `pod-nautilus` with `httpd:latest`, mounting `pvc-nautilus` at `/usr/local/apache2/htdocs`.
3. **Service Configuration**: Exposed the pod via `web-nautilus` with NodePort `30008`.
4. **Deployment**: Applied all resources using a single YAML file.
5. **Verification**: Confirmed PV/PVC binding, pod status, service creation, and web server accessibility.

**🎯 Key Success Factors**:
- **Correct PV/PVC**: Matched `storageClassName: manual` and `accessModes: ReadWriteOnce`.
- **Pod Mount**: Mounted PVC at Apache’s document root (`/usr/local/apache2/htdocs`).
- **Service Selector**: Used `app: pod-nautilus` to link service to pod.
- **Verification**: Confirmed `Index of /` via the Website button.
- **Testing**: Validated all components (PV, PVC, pod, service) with `kubectl get`.

**⚠️ Critical Fixes**:
- Ensure `hostPath: /mnt/devops` is correct (pre-created).
- Match `app: pod-nautilus` in pod labels and service selector.
- Use `nodePort: 30008` within valid range (30000-32767).
- Verify PVC binds to PV before pod creation.

**🔒 Kubernetes Best Practices Applied**:
- **Persistent Storage**: Used PV/PVC for reliable storage.
- **Label Management**: Ensured consistent labels for service-pod matching.
- **Minimal Configuration**: Kept YAMLs simple with required fields.
- **Verification**: Used `kubectl get` and `kubectl describe` for validation.

**⚠️ Important Troubleshooting Concepts**:
- **PV/PVC Binding**: Check `kubectl describe pv` and `kubectl describe pvc` for binding issues.
- **Pod Logs**: Use `kubectl logs` to diagnose Apache errors.
- **Service Endpoints**: Verify `kubectl get endpoints` for service routing.
- **NodePort Access**: Confirm node IP and port accessibility.

---

## ⚠️ Important Production Notes

🔧 **Debugging**: Use `kubectl describe`, `kubectl logs`, and `kubectl get endpoints` for issue resolution.
🔐 **Security**: Restrict `hostPath` usage in production; prefer managed storage (e.g., NFS, cloud storage).
📊 **Resource Limits**: Add `resources` (e.g., CPU/memory limits) to the pod for stability.
🛡️ **Service Exposure**: Use Ingress or LoadBalancer for production instead of NodePort.

*Last Updated: October 25, 2025*