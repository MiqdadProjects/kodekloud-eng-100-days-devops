# 🌟 Task 63 - Deploy Iron Gallery and Database with Services in Kubernetes

**📌 Task Description**

The **Nautilus DevOps team** is deploying a customized Iron Gallery application on a Kubernetes cluster, along with its database backend. The task involves creating a namespace, two deployments (for the gallery and database), and two services to expose them, with specific configurations for volumes, environment variables, and resource limits. The goal is to ensure the Iron Gallery installation page is accessible without configuring the database connection.

**👉 Task Requirements**:
- Create a namespace named `iron-namespace-datacenter`.
- Create a deployment named `iron-gallery-deployment-datacenter` in the namespace:
  - Labels: `run: iron-gallery`
  - Replicas: `1`
  - Selector: `matchLabels: run: iron-gallery`
  - Template labels: `run: iron-gallery`
  - Container:
    - Name: `iron-gallery-container-datacenter`
    - Image: `kodekloud/irongallery:2.0` (exact image and tag)
    - Resources: Memory limit `100Mi`, CPU limit `50m`
    - Volume mounts:
      - Name: `config`, mount path: `/usr/share/nginx/html/data`
      - Name: `images`, mount path: `/usr/share/nginx/html/uploads`
    - Volumes:
      - Name: `config`, type: `emptyDir`
      - Name: `images`, type: `emptyDir`
- Create a deployment named `iron-db-deployment-datacenter` in the namespace:
  - Labels: `db: mariadb`
  - Replicas: `1`
  - Selector: `matchLabels: db: mariadb`
  - Template labels: `db: mariadb`
  - Container:
    - Name: `iron-db-container-datacenter`
    - Image: `kodekloud/irondb:2.0` (exact image and tag)
    - Environment variables:
      - `MYSQL_DATABASE: database_web`
      - `MYSQL_ROOT_PASSWORD`: Complex password (e.g., `Root@12345`)
      - `MYSQL_PASSWORD`: Complex password (e.g., `User@12345`)
      - `MYSQL_USER`: Custom user (e.g., `dbadmin`, not `root`)
    - Volume mount: Name `db`, mount path `/var/lib/mysql`
    - Volume: Name `db`, type `emptyDir`
- Create a ClusterIP service named `iron-db-service-datacenter` in the namespace:
  - Selector: `db: mariadb`
  - Protocol: `TCP`
  - Port: `3306`, TargetPort: `3306`
  - Type: `ClusterIP`
- Create a NodePort service named `iron-gallery-service-datacenter` in the namespace:
  - Selector: `run: iron-gallery`
  - Protocol: `TCP`
  - Port: `80`, TargetPort: `80`, NodePort: `32678`
  - Type: `NodePort`
- Verify the Iron Gallery installation page via the **App** button in the KodeKloud lab interface.
- Use `kubectl` on the jump host, which is pre-configured to interact with the Kubernetes cluster.

**💡 Note**: Infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface. Database connection configuration is not required for this task.

---

## 🔹 Step 1: Connect to the Jump Host

```bash
ssh thor@jump_host
```

**Purpose**: Connect to the jump host where `kubectl` is configured to manage the Kubernetes cluster.

---

## 🔹 Step 2: Create the Namespace

```bash
kubectl create namespace iron-namespace-datacenter
```

**Purpose**: Create the `iron-namespace-datacenter` namespace to isolate the resources.

**Expected Output**:
```
namespace/iron-namespace-datacenter created
```

---

## 🔹 Step 3: Create the YAML File

```bash
vi iron-gallery-setup.yaml
```

**Purpose**: Create a single Kubernetes YAML file to define the namespace, deployments, and services.

---

## 🔹 Step 4: Add YAML Content

**YAML Content**:

```yaml
---
apiVersion: v1
kind: Namespace
metadata:
  name: iron-namespace-datacenter
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: iron-gallery-deployment-datacenter
  namespace: iron-namespace-datacenter
  labels:
    run: iron-gallery
spec:
  replicas: 1
  selector:
    matchLabels:
      run: iron-gallery
  template:
    metadata:
      labels:
        run: iron-gallery
    spec:
      containers:
      - name: iron-gallery-container-datacenter
        image: kodekloud/irongallery:2.0
        resources:
          limits:
            memory: "100Mi"
            cpu: "50m"
        volumeMounts:
        - name: config
          mountPath: /usr/share/nginx/html/data
        - name: images
          mountPath: /usr/share/nginx/html/uploads
      volumes:
      - name: config
        emptyDir: {}
      - name: images
        emptyDir: {}
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: iron-db-deployment-datacenter
  namespace: iron-namespace-datacenter
  labels:
    db: mariadb
spec:
  replicas: 1
  selector:
    matchLabels:
      db: mariadb
  template:
    metadata:
      labels:
        db: mariadb
    spec:
      containers:
      - name: iron-db-container-datacenter
        image: kodekloud/irondb:2.0
        env:
        - name: MYSQL_DATABASE
          value: "database_web"
        - name: MYSQL_ROOT_PASSWORD
          value: "Root@12345"
        - name: MYSQL_PASSWORD
          value: "User@12345"
        - name: MYSQL_USER
          value: "dbadmin"
        volumeMounts:
        - name: db
          mountPath: /var/lib/mysql
      volumes:
      - name: db
        emptyDir: {}
---
apiVersion: v1
kind: Service
metadata:
  name: iron-db-service-datacenter
  namespace: iron-namespace-datacenter
spec:
  selector:
    db: mariadb
  ports:
  - protocol: TCP
    port: 3306
    targetPort: 3306
  type: ClusterIP
---
apiVersion: v1
kind: Service
metadata:
  name: iron-gallery-service-datacenter
  namespace: iron-namespace-datacenter
spec:
  selector:
    run: iron-gallery
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
    nodePort: 32678
  type: NodePort
```

**Key Configurations**:
- **Namespace (`iron-namespace-datacenter`)**:
  - `apiVersion: v1`, `kind: Namespace`: Defines the namespace.
  - `metadata.name: iron-namespace-datacenter`: Sets the namespace name.
- **Deployment (`iron-gallery-deployment-datacenter`)**:
  - `apiVersion: apps/v1`, `kind: Deployment`: Defines a deployment.
  - `metadata.name: iron-gallery-deployment-datacenter`: Sets the deployment name in the namespace.
  - `metadata.labels.run: iron-gallery`: Labels the deployment.
  - `spec.replicas: 1`: Runs one pod.
  - `spec.selector.matchLabels.run: iron-gallery`: Matches pods with the label.
  - `spec.template.metadata.labels.run: iron-gallery`: Labels the pods.
  - `spec.template.spec.containers`:
    - `name: iron-gallery-container-datacenter`: Sets the container name.
    - `image: kodekloud/irongallery:2.0`: Uses the specified image.
    - `resources.limits`: Sets memory to `100Mi`, CPU to `50m`.
    - `volumeMounts`: Mounts `config` at `/usr/share/nginx/html/data` and `images` at `/usr/share/nginx/html/uploads`.
  - `spec.template.spec.volumes`: Defines `config` and `images` as `emptyDir`.
- **Deployment (`iron-db-deployment-datacenter`)**:
  - `metadata.name: iron-db-deployment-datacenter`: Sets the deployment name in the namespace.
  - `metadata.labels.db: mariadb`: Labels the deployment.
  - `spec.replicas: 1`: Runs one pod.
  - `spec.selector.matchLabels.db: mariadb`: Matches pods with the label.
  - `spec.template.metadata.labels.db: mariadb`: Labels the pods.
  - `spec.template.spec.containers`:
    - `name: iron-db-container-datacenter`: Sets the container name.
    - `image: kodekloud/irondb:2.0`: Uses the specified image.
    - `env`: Sets `MYSQL_DATABASE: database_web`, `MYSQL_ROOT_PASSWORD: Root@12345`, `MYSQL_PASSWORD: User@12345`, `MYSQL_USER: dbadmin`.
    - `volumeMounts`: Mounts `db` at `/var/lib/mysql`.
  - `spec.template.spec.volumes`: Defines `db` as `emptyDir`.
- **Service (`iron-db-service-datacenter`)**:
  - `metadata.name: iron-db-service-datacenter`: Sets the service name in the namespace.
  - `spec.selector.db: mariadb`: Targets pods with the label.
  - `spec.ports`: Maps port `3306` to `targetPort: 3306`, protocol `TCP`.
  - `spec.type: ClusterIP`: Internal service type.
- **Service (`iron-gallery-service-datacenter`)**:
  - `metadata.name: iron-gallery-service-datacenter`: Sets the service name in the namespace.
  - `spec.selector.run: iron-gallery`: Targets pods with the label.
  - `spec.ports`: Maps port `80` to `targetPort: 80`, `nodePort: 32678`, protocol `TCP`.
  - `spec.type: NodePort`: Exposes the service externally.

**Save and Exit**: Press `Esc`, type `:wq`, press `Enter`.

---

## 🔹 Step 5: Verify YAML Syntax

```bash
cat iron-gallery-setup.yaml
```

**Purpose**: Confirm the YAML file contains the correct configurations.

**Validation Checklist**:
- **Namespace**:
  - ✅ `name: iron-namespace-datacenter`
- **Gallery Deployment**:
  - ✅ `name: iron-gallery-deployment-datacenter`, `namespace: iron-namespace-datacenter`
  - ✅ Labels: `run: iron-gallery` in `metadata`, `selector`, and `template.metadata`
  - ✅ Replicas: `1`
  - ✅ Container: `name: iron-gallery-container-datacenter`, `image: kodekloud/irongallery:2.0`
  - ✅ Resources: `memory: 100Mi`, `cpu: 50m`
  - ✅ Volume mounts: `config` at `/usr/share/nginx/html/data`, `images` at `/usr/share/nginx/html/uploads`
  - ✅ Volumes: `config` and `images` as `emptyDir`
- **Database Deployment**:
  - ✅ `name: iron-db-deployment-datacenter`, `namespace: iron-namespace-datacenter`
  - ✅ Labels: `db: mariadb` in `metadata`, `selector`, and `template.metadata`
  - ✅ Replicas: `1`
  - ✅ Container: `name: iron-db-container-datacenter`, `image: kodekloud/irondb:2.0`
  - ✅ Environment: `MYSQL_DATABASE`, `MYSQL_ROOT_PASSWORD`, `MYSQL_PASSWORD`, `MYSQL_USER`
  - ✅ Volume mount: `db` at `/var/lib/mysql`
  - ✅ Volume: `db` as `emptyDir`
- **Database Service**:
  - ✅ `name: iron-db-service-datacenter`, `namespace: iron-namespace-datacenter`
  - ✅ Selector: `db: mariadb`
  - ✅ Ports: `port: 3306`, `targetPort: 3306`, `protocol: TCP`
  - ✅ Type: `ClusterIP`
- **Gallery Service**:
  - ✅ `name: iron-gallery-service-datacenter`, `namespace: iron-namespace-datacenter`
  - ✅ Selector: `run: iron-gallery`
  - ✅ Ports: `port: 80`, `targetPort: 80`, `nodePort: 32678`, `protocol: TCP`
  - ✅ Type: `NodePort`
- ✅ Correct YAML indentation (2 spaces) and valid separators (`---`)

---

## 🔹 Step 6: Apply the Configurations

```bash
kubectl apply -f iron-gallery-setup.yaml
```

**Purpose**: Deploy the namespace, deployments, and services to the Kubernetes cluster.

**Expected Output**:
```
namespace/iron-namespace-datacenter unchanged
deployment.apps/iron-gallery-deployment-datacenter created
deployment.apps/iron-db-deployment-datacenter created
service/iron-db-service-datacenter created
service/iron-gallery-service-datacenter created
```

---

## 🔹 Step 7: Verify Namespace and Resources

```bash
kubectl get all -n iron-namespace-datacenter
```

**Purpose**: Confirm the namespace, deployments, pods, and services are created and running.

**Expected Output**:
```
NAME                                              READY   STATUS    RESTARTS   AGE
pod/iron-gallery-deployment-datacenter-xyz123456   1/1     Running   0          30s
pod/iron-db-deployment-datacenter-abc789123       1/1     Running   0          30s

NAME                                  TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
service/iron-db-service-datacenter     ClusterIP   10.96.123.456   <none>        3306/TCP         30s
service/iron-gallery-service-datacenter NodePort   10.96.123.789   <none>        80:32678/TCP     30s

NAME                                          READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/iron-gallery-deployment-datacenter   1/1     1            1           30s
deployment.apps/iron-db-deployment-datacenter        1/1     1            1           30s
```

**Success Indicators**:
- ✅ Namespace: `iron-namespace-datacenter`
- ✅ Pods: `Running`, `1/1` ready, no restarts
- ✅ Services: `iron-db-service-datacenter` (ClusterIP, 3306), `iron-gallery-service-datacenter` (NodePort, 32678)
- ✅ Deployments: `1/1` ready

---

## 🔹 Step 8: Access Iron Gallery Installation Page

**Action**: In the KodeKloud lab interface, click the **App** button to access the Iron Gallery installation page.

**Purpose**: Verify the Iron Gallery application is accessible via the NodePort (32678).

**Expected Output**: ![screenshot](<Lychee -.png>)

**Alternative CLI Test** (if no App button is available):
```bash
kubectl get nodes -o wide -n iron-namespace-datacenter
```

**Note the node’s external IP** (e.g., `192.168.1.100`), then:
```bash
curl http://<node-ip>:32678
```

**Expected Output**:
- HTML content of the Iron Gallery installation page.

---

## 🔹 Step 9: Verify Deployment Details (Optional)

```bash
kubectl describe deployment -n iron-namespace-datacenter iron-gallery-deployment-datacenter
kubectl describe deployment -n iron-namespace-datacenter iron-db-deployment-datacenter
```

**Purpose**: Confirm the deployments’ configurations, including images, labels, and volumes.

**Expected Output (Excerpt for Gallery)**:
```
Name:                   iron-gallery-deployment-datacenter
Namespace:              iron-namespace-datacenter
Pod Template:
  Labels:               run=iron-gallery
  Containers:
   iron-gallery-container-datacenter:
    Image:        kodekloud/irongallery:2.0
    Resources:
      Limits:
        cpu:        50m
        memory:     100Mi
    Volume Mounts:
      /usr/share/nginx/html/data from config (rw)
      /usr/share/nginx/html/uploads from images (rw)
  Volumes:
   config:
     Type:       EmptyDir
   images:
     Type:       EmptyDir
```

**Expected Output (Excerpt for Database)**:
```
Name:                   iron-db-deployment-datacenter
Namespace:              iron-namespace-datacenter
Pod Template:
  Labels:               db=mariadb
  Containers:
   iron-db-container-datacenter:
    Image:        kodekloud/irondb:2.0
    Environment:
      MYSQL_DATABASE:      database_web
      MYSQL_ROOT_PASSWORD: Root@12345
      MYSQL_PASSWORD:      User@12345
      MYSQL_USER:          dbadmin
    Volume Mounts:
      /var/lib/mysql from db (rw)
  Volumes:
   db:
     Type:       EmptyDir
```

---

## 🔹 Step 10: Check Pod Logs (Optional)

```bash
kubectl logs -l run=iron-gallery -n iron-namespace-datacenter
kubectl logs -l db=mariadb -n iron-namespace-datacenter
```

**Purpose**: Verify the containers started correctly.

**Expected Gallery Logs** (example):
```
[Sat Oct 25 16:00:00.000000 2025] [mpm_event:notice] AH00489: Apache/2.4.62 (Unix) configured
```

**Expected Database Logs** (example):
```
2025-10-25 16:00:00+00:00 [Note] [Entrypoint]: MariaDB starting
2025-10-25 16:00:00+00:00 [Note] [Entrypoint]: Database initialized
```

---

## 🔹 Step 11: Test Database Accessibility (Optional)

```bash
kubectl exec -it $(kubectl get pods -l db=mariadb -n iron-namespace-datacenter -o name | head -n 1) -n iron-namespace-datacenter -- mysql -u dbadmin -pUser@12345 -e "SHOW DATABASES;"
```

**Expected Output**:
```
+--------------------+
| Database           |
+--------------------+
| database_web       |
| information_schema |
| mysql              |
| performance_schema |
+--------------------+
```

---

## 🔹 Step 12: Clean Up (Optional)

```bash
kubectl delete -f iron-gallery-setup.yaml
```

**Purpose**: Remove the namespace, deployments, and services if testing is complete.

**Expected Output**:
```
namespace "iron-namespace-datacenter" deleted
deployment.apps "iron-gallery-deployment-datacenter" deleted
deployment.apps "iron-db-deployment-datacenter" deleted
service "iron-db-service-datacenter" deleted
service "iron-gallery-service-datacenter" deleted
```

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **Jump Host**:

```bash
# Connect to jump host
ssh thor@jump_host

# Create namespace
kubectl create namespace iron-namespace-datacenter

# Create YAML file
cat > iron-gallery-setup.yaml << 'EOF'
---
apiVersion: v1
kind: Namespace
metadata:
  name: iron-namespace-datacenter
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: iron-gallery-deployment-datacenter
  namespace: iron-namespace-datacenter
  labels:
    run: iron-gallery
spec:
  replicas: 1
  selector:
    matchLabels:
      run: iron-gallery
  template:
    metadata:
      labels:
        run: iron-gallery
    spec:
      containers:
      - name: iron-gallery-container-datacenter
        image: kodekloud/irongallery:2.0
        resources:
          limits:
            memory: "100Mi"
            cpu: "50m"
        volumeMounts:
        - name: config
          mountPath: /usr/share/nginx/html/data
        - name: images
          mountPath: /usr/share/nginx/html/uploads
      volumes:
      - name: config
        emptyDir: {}
      - name: images
        emptyDir: {}
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: iron-db-deployment-datacenter
  namespace: iron-namespace-datacenter
  labels:
    db: mariadb
spec:
  replicas: 1
  selector:
    matchLabels:
      db: mariadb
  template:
    metadata:
      labels:
        db: mariadb
    spec:
      containers:
      - name: iron-db-container-datacenter
        image: kodekloud/irondb:2.0
        env:
        - name: MYSQL_DATABASE
          value: "database_web"
        - name: MYSQL_ROOT_PASSWORD
          value: "Root@12345"
        - name: MYSQL_PASSWORD
          value: "User@12345"
        - name: MYSQL_USER
          value: "dbadmin"
        volumeMounts:
        - name: db
          mountPath: /var/lib/mysql
      volumes:
      - name: db
        emptyDir: {}
---
apiVersion: v1
kind: Service
metadata:
  name: iron-db-service-datacenter
  namespace: iron-namespace-datacenter
spec:
  selector:
    db: mariadb
  ports:
  - protocol: TCP
    port: 3306
    targetPort: 3306
  type: ClusterIP
---
apiVersion: v1
kind: Service
metadata:
  name: iron-gallery-service-datacenter
  namespace: iron-namespace-datacenter
spec:
  selector:
    run: iron-gallery
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
    nodePort: 32678
  type: NodePort
EOF

# Deploy and verify
kubectl apply -f iron-gallery-setup.yaml
kubectl get all -n iron-namespace-datacenter
```

---

## 💡 Common Kubernetes Deployment/Service Issues & Fixes

### **Issue 1: Pods Not Running**
**Problem**: `kubectl get pods` shows `Error` or `CrashLoopBackOff`.
**Fix**: Check logs and events.
```bash
kubectl logs -l run=iron-gallery -n iron-namespace-datacenter
kubectl logs -l db=mariadb -n iron-namespace-datacenter
kubectl describe pod -n iron-namespace-datacenter
```

### **Issue 2: Image Pull Failure**
**Problem**: Pods in `ErrImagePull` or `ImagePullBackOff`.
**Fix**: Verify image names and tags.
```bash
docker pull kodekloud/irongallery:2.0
docker pull kodekloud/irondb:2.0
kubectl rollout restart deployment -n iron-namespace-datacenter iron-gallery-deployment-datacenter
kubectl rollout restart deployment -n iron-namespace-datacenter iron-db-deployment-datacenter
```

### **Issue 3: Service Not Accessible**
**Problem**: Iron Gallery installation page not accessible via nodePort 32678.
**Fix**: Verify service configuration and pod selector.
```bash
kubectl describe svc iron-gallery-service-datacenter -n iron-namespace-datacenter
kubectl get endpoints iron-gallery-service-datacenter -n iron-namespace-datacenter
```

### **Issue 4: Incorrect Selector Labels**
**Problem**: Service does not route to pods.
**Fix**: Ensure deployment and service selectors match.
```bash
kubectl get deployment iron-gallery-deployment-datacenter -n iron-namespace-datacenter -o yaml | grep selector
kubectl get svc iron-gallery-service-datacenter -n iron-namespace-datacenter -o yaml | grep selector
```

---

## 🔧 Troubleshooting Deployment/Service Failures

### **Error: Pod Not Running**
**Symptoms**: `kubectl get pods` shows `Error` or `CrashLoopBackOff`.
**Solution**: Check logs and events.
```bash
kubectl logs -l run=iron-gallery -n iron-namespace-datacenter
kubectl logs -l db=mariadb -n iron-namespace-datacenter
kubectl get events -n iron-namespace-datacenter
```

### **Error: Service Not Routing**
**Symptoms**: Accessing `<node-ip>:32678` fails.
**Solution**: Verify service endpoints and pod labels.
```bash
kubectl get endpoints iron-gallery-service-datacenter -n iron-namespace-datacenter
kubectl describe svc iron-gallery-service-datacenter -n iron-namespace-datacenter
```

### **Error: YAML Syntax Error**
**Symptoms**: `kubectl apply` fails with parsing error.
**Solution**: Validate YAML syntax.
```bash
kubectl apply -f iron-gallery-setup.yaml --dry-run=client
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was creating a namespace, two deployments (gallery and database), and two services with specific configurations (labels, volumes, environment variables, resource limits, and NodePort), ensuring the Iron Gallery installation page is accessible.

**💡 Solution Approach**:
1. **Namespace Creation**: Created `iron-namespace-datacenter` for resource isolation.
2. **Gallery Deployment**: Configured `iron-gallery-deployment-datacenter` with `kodekloud/irongallery:2.0`, volumes, and resource limits.
3. **Database Deployment**: Configured `iron-db-deployment-datacenter` with `kodekloud/irondb:2.0` and environment variables.
4. **Services**: Created `ClusterIP` for the database and `NodePort` (32678) for the gallery.
5. **Verification**: Confirmed resource creation and gallery accessibility via the App button.

**🎯 Key Success Factors**:
- **Correct Configurations**: Ensured precise labels, images, and volume mounts.
- **Namespace Isolation**: Applied all resources in `iron-namespace-datacenter`.
- **Service Accessibility**: Verified NodePort service for gallery access.
- **Verification**: Used `kubectl get all` to check all resources.
- **Testing**: Confirmed installation page via the App button.

**⚠️ Critical Fixes**:
- Ensure exact image names: `kodekloud/irongallery:2.0`, `kodekloud/irondb:2.0`.
- Match selector labels: `run: iron-gallery` and `db: mariadb`.
- Use `nodePort: 32678` within valid range (30000-32767).
- Verify environment variables for the database.

**🔒 Kubernetes Best Practices Applied**:
- **Namespace Isolation**: Used a dedicated namespace for organization.
- **Label Management**: Ensured consistent labels for deployment-service matching.
- **Resource Limits**: Applied CPU and memory limits for stability.
- **Minimal Configuration**: Kept YAMLs simple with required fields.

**⚠️ Important Troubleshooting Concepts**:
- **Service Endpoints**: Check `kubectl get endpoints` for service routing.
- **Pod Logs**: Use `kubectl logs` to diagnose startup issues.
- **Events**: Use `kubectl get events` to identify resource issues.
- **NodePort Access**: Verify node IP and port accessibility.

---

## ⚠️ Important Production Notes

🔧 **Deployment Debugging**: Use `kubectl describe`, `kubectl logs`, and `kubectl exec` for issue resolution.
🔐 **Security**: Secure database credentials with Kubernetes secrets in production.
📊 **Resource Limits**: Monitor and adjust resource limits based on application needs.
🛡️ **Service Exposure**: Use Ingress or LoadBalancer for production instead of NodePort.
🗄️ **Persistent Storage**: Replace `emptyDir` with persistent volumes for production data.

*Last Updated: October 25, 2025*