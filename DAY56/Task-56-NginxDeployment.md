# 🌟 Task 56 - Deploy Nginx Website with NodePort Service in Kubernetes

**📌 Task Description**

The **Nautilus DevOps team** is supporting developers who have created a static website that needs to be deployed on a Kubernetes cluster. To ensure high availability and scalability, the team has decided to create a deployment with multiple replicas and expose it via a NodePort service. The task involves setting up the deployment and service with specific configurations and verifying the website's accessibility.

**👉 Task Requirements**:
- Create a deployment named `nginx-deployment`:
  - Image: `nginx:latest` (must specify the `latest` tag)
  - Container name: `nginx-container`
  - Replicas: `3`
  - Port: `80` (Nginx default)
  - Labels: `app: nginx` (for selector and template metadata)
- Create a service named `nginx-service`:
  - Type: `NodePort`
  - Selector: `app: nginx`
  - Port: `80`
  - TargetPort: `80`
  - NodePort: `30011`
- Verify the website is accessible via the **App** button in the KodeKloud lab interface, displaying the default Nginx welcome page.
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
vi nginx-deployment.yaml
```

**Purpose**: Create a YAML file to define the `nginx-deployment` with three replicas and the specified container configuration.

---

## 🔹 Step 3: Add Deployment YAML Content

**Deployment YAML**:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx-container
        image: nginx:latest
        ports:
        - containerPort: 80
```

**Key Configurations**:
- **apiVersion: apps/v1**, **kind: Deployment**: Defines a deployment resource.
- **metadata.name: nginx-deployment**: Sets the deployment name.
- **spec.replicas: 3**: Runs three pods for high availability.
- **spec.selector.matchLabels.app: nginx**: Matches pods with the label.
- **spec.template.metadata.labels.app: nginx**: Labels the pods.
- **spec.template.spec.containers**:
  - **name: nginx-container**: Sets the container name.
  - **image: nginx:latest**: Uses the `nginx` image with the `latest` tag.
  - **ports.containerPort: 80**: Exposes Nginx’s default HTTP port.

**Save and Exit**: Press `Esc`, type `:wq`, press `Enter`.

---

## 🔹 Step 4: Create the Service YAML File

```bash
vi nginx-service.yaml
```

**Purpose**: Create a YAML file to define the `nginx-service` as a NodePort service to expose the Nginx deployment.

---

## 🔹 Step 5: Add Service YAML Content

**Service YAML**:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  type: NodePort
  ports:
  - port: 80
    targetPort: 80
    nodePort: 30011
    protocol: TCP
```

**Key Configurations**:
- **apiVersion: v1**, **kind: Service**: Defines a service resource.
- **metadata.name: nginx-service**: Sets the service name.
- **spec.selector.app: nginx**: Targets pods with the label `app: nginx`.
- **spec.type: NodePort**: Exposes the service externally via a node port.
- **spec.ports**:
  - **port: 80**: The service port.
  - **targetPort: 80**: The container port to route traffic to.
  - **nodePort: 30011**: The external port for accessing the service.
  - **protocol: TCP**: Specifies the protocol (default for HTTP).

**Save and Exit**: Press `Esc`, type `:wq`, press `Enter`.

---

## 🔹 Step 6: Verify YAML Syntax

```bash
cat nginx-deployment.yaml
cat nginx-service.yaml
```

**Purpose**: Confirm both YAML files contain the correct configurations.

**Validation Checklist**:
- **Deployment**:
  - ✅ `name: nginx-deployment`
  - ✅ Replicas: `3`
  - ✅ Labels: `app: nginx` in `selector` and `template.metadata`
  - ✅ Container: `name: nginx-container`, `image: nginx:latest`, `port: 80`
- **Service**:
  - ✅ `name: nginx-service`
  - ✅ Type: `NodePort`
  - ✅ Selector: `app: nginx`
  - ✅ Ports: `port: 80`, `targetPort: 80`, `nodePort: 30011`, `protocol: TCP`
- ✅ Correct YAML indentation (2 spaces)

---

## 🔹 Step 7: Apply the Configurations

```bash
kubectl apply -f nginx-deployment.yaml
kubectl apply -f nginx-service.yaml
```

**Purpose**: Deploy the Nginx deployment and service to the Kubernetes cluster.

**Expected Output**:
```
deployment.apps/nginx-deployment created
service/nginx-service created
```

---

## 🔹 Step 8: Verify Deployment and Pods

```bash
kubectl get deploy,pod
```

**Purpose**: Confirm the deployment is running and all three pods are operational.

**Expected Output**:
```
NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx-deployment 3/3     3            3           30s

NAME                                    READY   STATUS    RESTARTS   AGE
pod/nginx-deployment-xyz123-abc789      1/1     Running   0          30s
pod/nginx-deployment-xyz456-def123      1/1     Running   0          30s
pod/nginx-deployment-xyz789-ghi456      1/1     Running   0          30s
```

**Success Indicators**:
- ✅ Deployment: `3/3` ready
- ✅ Pod status: `Running`
- ✅ Ready: `1/1` for each pod
- ✅ No restarts

---

## 🔹 Step 9: Verify Service

```bash
kubectl get svc nginx-service
kubectl describe svc nginx-service
```

**Purpose**: Confirm the service is created and correctly configured.

**Expected `get svc` Output**:
```
NAME            TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
nginx-service   NodePort   10.96.123.456   <none>        80:30011/TCP   30s
```

**Expected `describe svc` Output (Excerpt)**:
```
Name:                     nginx-service
Type:                     NodePort
Selector:                 app=nginx
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  30011/TCP
Endpoints:                10.0.0.5:80,10.0.0.6:80,10.0.0.7:80
```

**Success Indicators**:
- ✅ Service name: `nginx-service`
- ✅ Type: `NodePort`
- ✅ Port mapping: `80:30011/TCP`
- ✅ Endpoints: Populated with three pod IPs (e.g., `10.0.0.5:80`, etc.)

---

## 🔹 Step 10: Access Nginx Website

**Action**: In the KodeKloud lab interface, click the **App** button to access the Nginx website.

**Purpose**: Verify the website is accessible via the NodePort (30011).

**Expected Output** (via browser):
- The default Nginx welcome page appears, typically displaying:
  ```
  Welcome to nginx!
  If you see this page, the nginx web server is successfully installed and working...
  ```

**Alternative CLI Test** (if no App button is available):
```bash
kubectl get nodes -o wide
```

**Note the node’s external IP** (e.g., `192.168.1.100`), then:
```bash
curl http://<node-ip>:30011
```

**Expected Output**:
- HTML content of the Nginx welcome page.

---

## 🔹 Step 11: Verify Deployment Details (Optional)

```bash
kubectl describe deployment nginx-deployment
```

**Purpose**: Confirm the deployment’s configuration, including image and replicas.

**Expected Output (Excerpt)**:
```
Name:                   nginx-deployment
Pod Template:
  Labels:               app=nginx
  Containers:
   nginx-container:
    Image:        nginx:latest
    Port:         80/TCP
Replicas:
  Desired:      3
  Current:      3
  Ready:        3
```

**Success Indicators**:
- ✅ Image: `nginx:latest`
- ✅ Port: `80`
- ✅ Replicas: `3`

---

## 🔹 Step 12: Check Nginx Logs (Optional)

```bash
kubectl logs -l app=nginx
```

**Purpose**: Verify the Nginx container started correctly.

**Expected Logs** (example):
```
[25/Oct/2025:16:00:00 +0000] nginx: started
```

---

## 🔹 Step 13: Clean Up (Optional)

```bash
kubectl delete -f nginx-deployment.yaml
kubectl delete -f nginx-service.yaml
```

**Purpose**: Remove the deployment and service if testing is complete.

**Expected Output**:
```
deployment.apps "nginx-deployment" deleted
service "nginx-service" deleted
```

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **Jump Host**:

```bash
# Connect to jump host
ssh thor@jump_host

# Create deployment YAML
cat > nginx-deployment.yaml << 'EOF'
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx-container
        image: nginx:latest
        ports:
        - containerPort: 80
EOF

# Create service YAML
cat > nginx-service.yaml << 'EOF'
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  type: NodePort
  ports:
  - port: 80
    targetPort: 80
    nodePort: 30011
    protocol: TCP
EOF

# Deploy and verify
kubectl apply -f nginx-deployment.yaml
kubectl apply -f nginx-service.yaml
kubectl get deploy,pod
kubectl get svc nginx-service
```

---

## 💡 Common Kubernetes Deployment/Service Issues & Fixes

### **Issue 1: Pods Not Running**
**Problem**: `kubectl get pods` shows `Error` or `CrashLoopBackOff`.
**Fix**: Check logs and events.
```bash
kubectl logs -l app=nginx
kubectl describe pod -l app=nginx
```

### **Issue 2: Image Pull Failure**
**Problem**: Pods in `ErrImagePull` or `ImagePullBackOff`.
**Fix**: Verify the image name and tag.
```bash
docker pull nginx:latest
kubectl rollout restart deployment nginx-deployment
```

### **Issue 3: Service Not Accessible**
**Problem**: Accessing `<node-ip>:30011` fails.
**Fix**: Verify service selector and endpoints.
```bash
kubectl describe svc nginx-service
kubectl get endpoints nginx-service
```

### **Issue 4: NodePort Not Working**
**Problem**: NodePort `30011` is not accessible.
**Fix**: Ensure `nodePort` is within the valid range (30000-32767) and check node firewall settings.
```bash
kubectl describe svc nginx-service
kubectl get nodes -o wide
```

---

## 🔧 Troubleshooting Deployment/Service Failures

### **Error: Pod Not Running**
**Symptoms**: `kubectl get pods` shows `Error` or `CrashLoopBackOff`.
**Solution**: Check logs and events for specific errors.
```bash
kubectl logs -l app=nginx
kubectl get events --field-selector involvedObject.name=nginx-deployment
```

### **Error: Service Not Routing**
**Symptoms**: Accessing `<node-ip>:30011` returns no response.
**Solution**: Verify service selector and pod labels.
```bash
kubectl describe svc nginx-service
kubectl get pods -l app=nginx
```

### **Error: YAML Syntax Error**
**Symptoms**: `kubectl apply` fails with parsing error.
**Solution**: Validate YAML syntax.
```bash
kubectl apply -f nginx-deployment.yaml --dry-run=client
kubectl apply -f nginx-service.yaml --dry-run=client
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was creating a highly available Nginx deployment with three replicas and exposing it via a NodePort service, ensuring the correct image tag and port configurations.

**💡 Solution Approach**:
1. **Deployment Creation**: Defined `nginx-deployment` with `nginx:latest`, three replicas, and port `80`.
2. **Service Creation**: Configured `nginx-service` as NodePort with `nodePort: 30011`.
3. **Verification**: Confirmed deployment readiness, pod status, service endpoints, and website accessibility via the App button.
4. **Testing**: Validated the Nginx welcome page display.

**🎯 Key Success Factors**:
- **Correct Image**: Used `nginx:latest` as specified.
- **High Availability**: Set replicas to `3`.
- **Service Exposure**: Configured NodePort `30011` correctly.
- **Verification**: Ensured pods are `Running` and service has endpoints.
- **Testing**: Confirmed the Nginx welcome page via the App button.

**⚠️ Critical Fixes**:
- Ensure `image: nginx:latest` includes the `latest` tag.
- Match `app: nginx` labels across deployment and service.
- Use `nodePort: 30011` within valid range (30000-32767).
- Verify service selector matches pod labels.

**🔒 Kubernetes Best Practices Applied**:
- **Label Consistency**: Used `app: nginx` for deployment and service.
- **Replicas**: Set to `3` for high availability.
- **Port Configuration**: Explicitly defined `port`, `targetPort`, and `nodePort`.
- **Verification**: Used `kubectl describe` and `kubectl get` for validation.

**⚠️ Important Troubleshooting Concepts**:
- **Service Endpoints**: Check `kubectl get endpoints` for routing issues.
- **Pod Logs**: Use `kubectl logs` to diagnose Nginx issues.
- **Events**: Use `kubectl get events` to identify deployment or service issues.
- **NodePort Access**: Verify node IP and port accessibility.

---

## ⚠️ Important Production Notes

🔧 **Deployment Debugging**: Use `kubectl describe`, `kubectl logs`, and `kubectl exec` for issue resolution.
🔐 **Image Security**: Pin a specific Nginx version (e.g., `nginx:1.25`) in production to avoid unexpected updates.
📊 **Resource Limits**: Add CPU/memory limits to the deployment for resource control.
🛡️ **Service Exposure**: Use Ingress or LoadBalancer for production instead of NodePort.
🗄️ **Persistent Storage**: Add PersistentVolume for static website files in production.

*Last Updated: October 25, 2025*