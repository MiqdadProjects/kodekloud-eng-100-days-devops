# 🌟 Task 66 - Deploy MySQL with Persistent Volume and Secrets in Kubernetes

**📌 Task Description**

The **Nautilus DevOps team** needs to deploy a MySQL server on a Kubernetes cluster to support an application. The task involves creating a PersistentVolume (PV), PersistentVolumeClaim (PVC), multiple secrets for sensitive data, a MySQL deployment, and a NodePort service to expose the database, with environment variables sourced from the secrets.

**👉 Task Requirements**:
- Create a PersistentVolume named `mysql-pv`:
  - Capacity: `250Mi`
  - Other parameters: Set as per preference (e.g., `ReadWriteOnce`, `manual` storage class, `hostPath`).
- Create a PersistentVolumeClaim named `mysql-pv-claim`:
  - Request: `250Mi`
  - Other parameters: Match PV (e.g., `ReadWriteOnce`, `manual` storage class).
- Create three secrets:
  - `mysql-root-pass`: Key `password`, value `YUIidhb667`.
  - `mysql-user-pass`: Keys `username` (`kodekloud_gem`) and `password` (`B4zNgHA7Ya`).
  - `mysql-db-url`: Key `database`, value `kodekloud_db1`.
- Create a deployment named `mysql-deployment`:
  - Replicas: `1`
  - Container:
    - Name: `mysql-container`
    - Image: `mysql:5.7` (preferred choice for compatibility).
    - Mount: PVC at `/var/lib/mysql`.
    - Environment variables:
      - `MYSQL_ROOT_PASSWORD`: From `mysql-root-pass` secret, key `password`.
      - `MYSQL_DATABASE`: From `mysql-db-url` secret, key `database`.
      - `MYSQL_USER`: From `mysql-user-pass` secret, key `username`.
      - `MYSQL_PASSWORD`: From `mysql-user-pass` secret, key `password`.
    - Port: `3306`.
  - Labels: `app: mysql` (for selector and template metadata).
- Create a NodePort service named `mysql`:
  - Selector: `app: mysql`
  - Port: `3306`
  - TargetPort: `3306`
  - NodePort: `30007`
- Ensure the deployment is in an up and running state.
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
vi mysql-setup.yaml
```

**Purpose**: Create a single Kubernetes YAML file to define the PersistentVolume, PersistentVolumeClaim, secrets, deployment, and service.

---

## 🔹 Step 3: Add YAML Content

**YAML Content**:

```yaml
# ----------------------------
# 1. Persistent Volume
# ----------------------------
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mysql-pv
spec:
  capacity:
    storage: 250Mi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: manual
  hostPath:
    path: /mnt/mysql-data
---
# ----------------------------
# 2. Persistent Volume Claim
# ----------------------------
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: manual
  resources:
    requests:
      storage: 250Mi
---
# ----------------------------
# 3. Secrets
# ----------------------------
apiVersion: v1
kind: Secret
metadata:
  name: mysql-root-pass
type: Opaque
stringData:
  password: YUIidhb667
---
apiVersion: v1
kind: Secret
metadata:
  name: mysql-user-pass
type: Opaque
stringData:
  username: kodekloud_gem
  password: B4zNgHA7Ya
---
apiVersion: v1
kind: Secret
metadata:
  name: mysql-db-url
type: Opaque
stringData:
  database: kodekloud_db1
---
# ----------------------------
# 4. Deployment
# ----------------------------
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - name: mysql-container
        image: mysql:5.7
        ports:
        - containerPort: 3306
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-root-pass
              key: password
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: mysql-db-url
              key: database
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: mysql-user-pass
              key: username
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-user-pass
              key: password
        volumeMounts:
        - name: mysql-storage
          mountPath: /var/lib/mysql
      volumes:
      - name: mysql-storage
        persistentVolumeClaim:
          claimName: mysql-pv-claim
---
# ----------------------------
# 5. Service (NodePort)
# ----------------------------
apiVersion: v1
kind: Service
metadata:
  name: mysql
spec:
  type: NodePort
  selector:
    app: mysql
  ports:
  - protocol: TCP
    port: 3306
    targetPort: 3306
    nodePort: 30007
```

**Key Configurations**:
- **PersistentVolume (`mysql-pv`)**:
  - `apiVersion: v1`, `kind: PersistentVolume`: Defines a PV.
  - `metadata.name: mysql-pv`: Sets the PV name.
  - `spec.capacity.storage: 250Mi`: Allocates 250Mi storage.
  - `spec.accessModes: ReadWriteOnce`: Allows one node to read/write.
  - `spec.persistentVolumeReclaimPolicy: Retain`: Retains data after PVC deletion.
  - `spec.storageClassName: manual`: Uses manual storage class.
  - `spec.hostPath.path: /mnt/mysql-data`: Uses a host path (assumed pre-created).
- **PersistentVolumeClaim (`mysql-pv-claim`)**:
  - `metadata.name: mysql-pv-claim`: Sets the PVC name.
  - `spec.accessModes: ReadWriteOnce`: Matches PV’s access mode.
  - `spec.storageClassName: manual`: Matches PV’s storage class.
  - `spec.resources.requests.storage: 250Mi`: Requests 250Mi.
- **Secrets**:
  - `mysql-root-pass`: `password: YUIidhb667`, type `Opaque`.
  - `mysql-user-pass`: `username: kodekloud_gem`, `password: B4zNgHA7Ya`, type `Opaque`.
  - `mysql-db-url`: `database: kodekloud_db1`, type `Opaque`.
- **Deployment (`mysql-deployment`)**:
  - `metadata.name: mysql-deployment`: Sets the deployment name.
  - `spec.replicas: 1`: Runs one pod.
  - `spec.selector.matchLabels.app: mysql`: Matches pods with the label.
  - `spec.template.metadata.labels.app: mysql`: Labels the pods.
  - `spec.template.spec.containers`:
    - `name: mysql-container`: Sets the container name.
    - `image: mysql:5.7`: Uses MySQL 5.7 for compatibility.
    - `ports.containerPort: 3306`: Exposes MySQL’s default port.
    - `env`: Sources environment variables from secrets.
    - `volumeMounts`: Mounts `mysql-storage` at `/var/lib/mysql`.
  - `spec.template.spec.volumes`: Links to `mysql-pv-claim`.
- **Service (`mysql`)**:
  - `metadata.name: mysql`: Sets the service name.
  - `spec.type: NodePort`: Exposes the service externally.
  - `spec.selector.app: mysql`: Targets pods with the label.
  - `spec.ports`: Maps `port: 3306` to `targetPort: 3306`, `nodePort: 30007`.

**Save and Exit**: Press `Esc`, type `:wq`, press `Enter`.

---

## 🔹 Step 4: Verify YAML Syntax

```bash
cat mysql-setup.yaml
```

**Purpose**: Confirm the YAML file contains the correct configurations.

**Validation Checklist**:
- **PersistentVolume**:
  - ✅ `name: mysql-pv`
  - ✅ `storage: 250Mi`, `accessModes: ReadWriteOnce`, `storageClassName: manual`, `hostPath: /mnt/mysql-data`
- **PersistentVolumeClaim**:
  - ✅ `name: mysql-pv-claim`
  - ✅ `storage: 250Mi`, `accessModes: ReadWriteOnce`, `storageClassName: manual`
- **Secrets**:
  - ✅ `mysql-root-pass`: `password: YUIidhb667`
  - ✅ `mysql-user-pass`: `username: kodekloud_gem`, `password: B4zNgHA7Ya`
  - ✅ `mysql-db-url`: `database: kodekloud_db1`
- **Deployment**:
  - ✅ `name: mysql-deployment`, `replicas: 1`
  - ✅ Labels: `app: mysql` in `selector` and `template.metadata`
  - ✅ Container: `name: mysql-container`, `image: mysql:5.7`, `port: 3306`
  - ✅ Environment: Correct `secretKeyRef` for all variables
  - ✅ Volume mount: `mysql-storage` at `/var/lib/mysql`
  - ✅ Volume: `persistentVolumeClaim.claimName: mysql-pv-claim`
- **Service**:
  - ✅ `name: mysql`, `type: NodePort`
  - ✅ Selector: `app: mysql`
  - ✅ Ports: `port: 3306`, `targetPort: 3306`, `nodePort: 30007`
- ✅ Correct YAML indentation (2 spaces) and valid separators (`---`)

---

## 🔹 Step 5: Apply the Configurations

```bash
kubectl apply -f mysql-setup.yaml
```

**Purpose**: Deploy the PersistentVolume, PersistentVolumeClaim, secrets, deployment, and service to the Kubernetes cluster.

**Expected Output**:
```
persistentvolume/mysql-pv created
persistentvolumeclaim/mysql-pv-claim created
secret/mysql-root-pass created
secret/mysql-user-pass created
secret/mysql-db-url created
deployment.apps/mysql-deployment created
service/mysql created
```

---

## 🔹 Step 6: Verify PersistentVolume and PersistentVolumeClaim

```bash
kubectl get pv,pvc
```

**Purpose**: Confirm the PV is created and the PVC is bound.

**Expected Output**:
```
NAME                           CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                    STORAGECLASS   AGE
persistentvolume/mysql-pv      250Mi      RWO            Retain           Bound    default/mysql-pv-claim   manual         30s

NAME                                STATUS   VOLUME     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
persistentvolumeclaim/mysql-pv-claim Bound    mysql-pv   250Mi      RWO            manual         30s
```

**Success Indicators**:
- ✅ PV name: `mysql-pv`, status: `Bound`, claim: `default/mysql-pv-claim`
- ✅ PVC name: `mysql-pv-claim`, status: `Bound`, volume: `mysql-pv`

---

## 🔹 Step 7: Verify Secrets

```bash
kubectl get secrets
kubectl describe secret mysql-root-pass
kubectl describe secret mysql-user-pass
kubectl describe secret mysql-db-url
```

**Purpose**: Confirm all secrets were created with the correct data.

**Expected `get secrets` Output**:
```
NAME                TYPE     DATA   AGE
mysql-root-pass     Opaque   1      30s
mysql-user-pass     Opaque   2      30s
mysql-db-url        Opaque   1      30s
```

**Expected `describe secret` Outputs (Excerpts)**:
- `mysql-root-pass`:
  ```
  Name:         mysql-root-pass
  Type:         Opaque
  Data
  ====
  password:     10 bytes
  ```
- `mysql-user-pass`:
  ```
  Name:         mysql-user-pass
  Type:         Opaque
  Data
  ====
  username:     12 bytes
  password:     10 bytes
  ```
- `mysql-db-url`:
  ```
  Name:         mysql-db-url
  Type:         Opaque
  Data
  ====
  database:     11 bytes
  ```

**Success Indicators**:
- ✅ Secrets created: `mysql-root-pass`, `mysql-user-pass`, `mysql-db-url`
- ✅ Correct data counts: 1, 2, and 1 respectively

---

## 🔹 Step 8: Verify Deployment and Pod Status

```bash
kubectl get deploy,pod
```

**Purpose**: Confirm the deployment is running and pods are operational.

**Expected Output**:
```
NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/mysql-deployment 1/1     1            1           30s

NAME                                    READY   STATUS    RESTARTS   AGE
pod/mysql-deployment-xyz123456-abc789   1/1     Running   0          30s
```

**Success Indicators**:
- ✅ Deployment: `1/1` ready
- ✅ Pod status: `Running`
- ✅ Ready: `1/1`
- ✅ No restarts

---

## 🔹 Step 9: Verify Service

```bash
kubectl get svc mysql
kubectl describe svc mysql
```

**Purpose**: Confirm the service is created and correctly configured.

**Expected `get svc` Output**:
```
NAME    TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
mysql   NodePort   10.96.123.456   <none>        3306:30007/TCP   30s
```

**Expected `describe svc` Output (Excerpt)**:
```
Name:                     mysql
Type:                     NodePort
Selector:                 app=mysql
Port:                     <unset>  3306/TCP
TargetPort:               3306/TCP
NodePort:                 <unset>  30007/TCP
Endpoints:                10.0.0.5:3306
```

**Success Indicators**:
- ✅ Service name: `mysql`
- ✅ Type: `NodePort`
- ✅ Port mapping: `3306:30007/TCP`
- ✅ Endpoints: Populated (e.g., `10.0.0.5:3306`)

---

## 🔹 Step 10: Verify MySQL Functionality (Optional)

```bash
kubectl exec -it $(kubectl get pods -l app=mysql -o name | head -n 1) -- mysql -u kodekloud_gem -pB4zNgHA7Ya -e "SHOW DATABASES;"
```

**Purpose**: Confirm the MySQL database is initialized with the specified credentials and database.

**Expected Output**:
```
+--------------------+
| Database           |
+--------------------+
| kodekloud_db1      |
| information_schema |
| mysql              |
| performance_schema |
+--------------------+
```

---

## 🔹 Step 11: Check MySQL Logs (Optional)

```bash
kubectl logs -l app=mysql
```

**Purpose**: Verify the MySQL container started correctly.

**Expected Logs** (example):
```
2025-10-25T16:00:00.000000Z 0 [Note] mysqld: ready for connections.
Version: '5.7.44'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server (GPL)
```

---

## 🔹 Step 12: Clean Up (Optional)

```bash
kubectl delete -f mysql-setup.yaml
```

**Purpose**: Remove the PV, PVC, secrets, deployment, and service if testing is complete.

**Expected Output**:
```
persistentvolume "mysql-pv" deleted
persistentvolumeclaim "mysql-pv-claim" deleted
secret "mysql-root-pass" deleted
secret "mysql-user-pass" deleted
secret "mysql-db-url" deleted
deployment.apps "mysql-deployment" deleted
service "mysql" deleted
```

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **Jump Host**:

```bash
# Connect to jump host
ssh thor@jump_host

# Create YAML file
cat > mysql-setup.yaml << 'EOF'
# Persistent Volume
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mysql-pv
spec:
  capacity:
    storage: 250Mi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: manual
  hostPath:
    path: /mnt/mysql-data
---
# Persistent Volume Claim
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: manual
  resources:
    requests:
      storage: 250Mi
---
# Secrets
apiVersion: v1
kind: Secret
metadata:
  name: mysql-root-pass
type: Opaque
stringData:
  password: YUIidhb667
---
apiVersion: v1
kind: Secret
metadata:
  name: mysql-user-pass
type: Opaque
stringData:
  username: kodekloud_gem
  password: B4zNgHA7Ya
---
apiVersion: v1
kind: Secret
metadata:
  name: mysql-db-url
type: Opaque
stringData:
  database: kodekloud_db1
---
# Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - name: mysql-container
        image: mysql:5.7
        ports:
        - containerPort: 3306
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-root-pass
              key: password
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: mysql-db-url
              key: database
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: mysql-user-pass
              key: username
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-user-pass
              key: password
        volumeMounts:
        - name: mysql-storage
          mountPath: /var/lib/mysql
      volumes:
      - name: mysql-storage
        persistentVolumeClaim:
          claimName: mysql-pv-claim
---
# Service (NodePort)
apiVersion: v1
kind: Service
metadata:
  name: mysql
spec:
  type: NodePort
  selector:
    app: mysql
  ports:
  - protocol: TCP
    port: 3306
    targetPort: 3306
    nodePort: 30007
EOF

# Deploy and verify
kubectl apply -f mysql-setup.yaml
kubectl get pv,pvc
kubectl get secrets
kubectl get deploy,pod
kubectl get svc mysql
```

---

## 💡 Common Kubernetes PV/PVC/Secret/Deployment/Service Issues & Fixes

### **Issue 1: PV Not Bound**
**Problem**: `kubectl get pv` shows `mysql-pv` as `Available`, not `Bound`.
**Fix**: Verify PVC configuration matches PV.
```bash
kubectl describe pv mysql-pv
kubectl describe pvc mysql-pv-claim
```

### **Issue 2: Secret Not Found**
**Problem**: Pod fails with `secretKeyRef` error.
**Fix**: Verify secrets exist and names match.
```bash
kubectl get secrets
kubectl describe secret mysql-root-pass
```

### **Issue 3: Pod Not Running**
**Problem**: `kubectl get pods` shows `Error` or `CrashLoopBackOff`.
**Fix**: Check logs and events.
```bash
kubectl logs -l app=mysql
kubectl describe pod -l app=mysql
```

### **Issue 4: Service Not Accessible**
**Problem**: Accessing `<node-ip>:30007` fails.
**Fix**: Verify service selector and endpoints.
```bash
kubectl describe svc mysql
kubectl get endpoints mysql
```

---

## 🔧 Troubleshooting Failures

### **Error: Pod Not Running**
**Symptoms**: `kubectl get pods` shows `Error` or `CrashLoopBackOff`.
**Solution**: Check logs and events for secret or volume issues.
```bash
kubectl logs -l app=mysql
kubectl get events --field-selector involvedObject.name=mysql-deployment
```

### **Error: Volume Mount Failure**
**Symptoms**: Pod fails with volume mount error.
**Solution**: Verify PVC and PV configurations.
```bash
kubectl describe pvc mysql-pv-claim
kubectl describe pod -l app=mysql
```

### **Error: Secret Misconfiguration**
**Symptoms**: MySQL fails to start due to incorrect environment variables.
**Solution**: Verify secret data and `secretKeyRef`.
```bash
kubectl describe secret mysql-root-pass
kubectl describe secret mysql-user-pass
kubectl describe secret mysql-db-url
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was creating a PersistentVolume, PersistentVolumeClaim, multiple secrets, a MySQL deployment, and a NodePort service, ensuring environment variables are sourced from secrets and the PV is mounted correctly.

**💡 Solution Approach**:
1. **PV and PVC**: Created `mysql-pv` (250Mi) and `mysql-pv-claim`, ensuring binding.
2. **Secrets**: Defined three secrets with specified key-value pairs.
3. **Deployment**: Configured `mysql-deployment` with `mysql:5.7`, secret-based environment variables, and PVC mount.
4. **Service**: Exposed MySQL via `mysql` service on NodePort `30007`.
5. **Verification**: Confirmed PV/PVC binding, secret creation, deployment status, and service accessibility.

**🎯 Key Success Factors**:
- **Correct PV/PVC**: Matched `storageClassName` and `accessModes`.
- **Secret Configuration**: Used `stringData` for clear secret values.
- **Environment Variables**: Properly referenced secrets in `secretKeyRef`.
- **Verification**: Ensured pod is `Running` and service has endpoints.
- **Testing**: Validated MySQL functionality with `mysql` command.

**⚠️ Critical Fixes**:
- Ensure secrets are created before the deployment.
- Use `mysql:5.7` for compatibility.
- Match `app: mysql` labels across deployment and service.
- Verify `nodePort: 30007` is within valid range (30000-32767).

**🔒 Kubernetes Best Practices Applied**:
- **Persistent Storage**: Used PV/PVC for MySQL data persistence.
- **Secret Management**: Stored sensitive data securely in secrets.
- **Label Consistency**: Ensured matching labels for deployment and service.
- **Minimal Configuration**: Kept YAMLs simple with required fields.

**⚠️ Important Troubleshooting Concepts**:
- **PV/PVC Binding**: Check `kubectl describe pv` and `kubectl describe pvc`.
- **Secret Verification**: Use `kubectl describe secret` to validate data.
- **Pod Logs**: Use `kubectl logs` to diagnose MySQL issues.
- **Service Endpoints**: Check `kubectl get endpoints` for routing.

---

## ⚠️ Important Production Notes

🔧 **Deployment Debugging**: Use `kubectl describe`, `kubectl logs`, and `kubectl exec` for issue resolution.
🔐 **Security**: Restrict secret access with RBAC and use encrypted storage for production secrets.
📊 **Resource Limits**: Add CPU/memory limits to the deployment for stability.
🛡️ **Service Exposure**: Use ClusterIP or Ingress for MySQL in production instead of NodePort.
🗄️ **Persistent Storage**: Replace `hostPath` with managed storage (e.g., cloud volumes) for production.

*Last Updated: October 25, 2025*