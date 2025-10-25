# 🌟 Task 64 - Troubleshoot and Fix Python Flask App Deployment in Kubernetes

**📌 Task Description**

The **Nautilus DevOps team** attempted to deploy a Python Flask application on a Kubernetes cluster, but due to misconfigurations, the application is not accessible. The task involves troubleshooting and fixing the issues in the existing `python-deployment-devops` deployment and its associated service to ensure the application is accessible via the specified NodePort (32345).

**👉 Task Requirements**:
- Fix the `python-deployment-devops` deployment:
  - Correct the image from `poroko/flask-app-demo` to `poroko/flask-demo-app`.
- Fix the service:
  - Update `port` and `targetPort` from `8080` to `5000` (Flask’s default port).
  - Retain `nodePort: 32345`.
- Verify the application is accessible via the **App** button in the KodeKloud lab interface, displaying the content: `Hello World Pyvo 1!`.
- Use `kubectl` on the jump host, which is pre-configured to interact with the Kubernetes cluster.

**💡 Note**: Infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to the Jump Host

```bash
ssh thor@jump_host
```

**Purpose**: Connect to the jump host where `kubectl` is configured to manage the Kubernetes cluster.

---

## 🔹 Step 2: Investigate Deployment and Service Issues

```bash
kubectl get deployments
kubectl get pods
kubectl get svc
```

**Purpose**: Check the status of the `python-deployment-devops` deployment, its pods, and the associated service.

**Expected Deployment Output**:
```
NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
python-deployment-devops 0/1     0            0           1h
```

**Expected Pod Output**:
```
NAME                                    READY   STATUS             RESTARTS   AGE
python-deployment-devops-xyz123456-abc789 0/1     ImagePullBackOff   0          1h
```

**Expected Service Output**:
```
NAME                    TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
python-service-devops   NodePort   10.96.123.456   <none>        8080:32345/TCP   1h
```

**Observations**:
- Pods are not running (e.g., `ImagePullBackOff` due to incorrect image `poroko/flask-app-demo`).
- Service has no endpoints because `port: 8080` and `targetPort: 8080` do not match the Flask app’s port (5000).

---

## 🔹 Step 3: Inspect Deployment Details

```bash
kubectl describe deployment python-deployment-devops
```

**Purpose**: Identify the image issue in the deployment.

**Expected Output (Excerpt)**:
```
Pod Template:
  Containers:
   python-container:
    Image:        poroko/flask-app-demo
    Port:         5000/TCP
Events:
  Type     Reason             Age   From                   Message
  ----     ------             ----  ----                   -------
  Warning  Failed             1m    deployment-controller  Failed to pull image "poroko/flask-app-demo": image not found
```

**Findings**:
- **Image Issue**: The deployment uses `poroko/flask-app-demo`, which is incorrect. It should be `poroko/flask-demo-app`.

---

## 🔹 Step 4: Inspect Service Details

```bash
kubectl describe svc python-service-devops
```

**Purpose**: Identify the port mismatch in the service.

**Expected Output (Excerpt)**:
```
Name:                     python-service-devops
Type:                     NodePort
Selector:                 app=python-app
Port:                     <unset>  8080/TCP
TargetPort:               8080/TCP
NodePort:                 <unset>  32345/TCP
Endpoints:                <none>
```

**Findings**:
- **Port Issue**: The service uses `port: 8080` and `targetPort: 8080`, but the Flask app listens on port `5000` (as indicated by `containerPort: 5000` in the deployment).

---

## 🔹 Step 5: Fix the Deployment

```bash
kubectl edit deployment python-deployment-devops
```

**Purpose**: Correct the image name in the deployment.

**Changes to Make**:
- Locate: `image: poroko/flask-app-demo`
- Change to: `image: poroko/flask-demo-app`

**Reference Deployment YAML (for clarity)**:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: python-deployment-devops
spec:
  replicas: 1
  selector:
    matchLabels:
      app: python-app
  template:
    metadata:
      labels:
        app: python-app
    spec:
      containers:
      - name: python-container
        image: poroko/flask-demo-app
        ports:
        - containerPort: 5000
```

**Save and Exit**: Press `Esc`, type `:wq`, press `Enter`.

**Note**: The actual YAML may include additional fields (e.g., resource limits, labels). Ensure only the image name is updated.

---

## 🔹 Step 6: Fix the Service

```bash
kubectl edit svc python-service-devops
```

**Purpose**: Correct the port and targetPort in the service.

**Changes to Make**:
- Locate:
  ```yaml
  ports:
  - port: 8080
    targetPort: 8080
    nodePort: 32345
  ```
- Change to:
  ```yaml
  ports:
  - port: 5000
    targetPort: 5000
    nodePort: 32345
  ```

**Reference Service YAML (for clarity)**:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: python-service-devops
spec:
  selector:
    app: python-app
  ports:
  - protocol: TCP
    port: 5000
    targetPort: 5000
    nodePort: 32345
  type: NodePort
```

**Save and Exit**: Press `Esc`, type `:wq`, press `Enter`.

---

## 🔹 Step 7: Verify Deployment and Pod Status

```bash
kubectl get deployments
kubectl get pods
```

**Purpose**: Confirm the deployment is updated and pods are running.

**Expected Deployment Output**:
```
NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
python-deployment-devops 1/1     1            1           1h
```

**Expected Pod Output**:
```
NAME                                    READY   STATUS    RESTARTS   AGE
python-deployment-devops-xyz123456-abc789 1/1     Running   0          30s
```

**Success Indicators**:
- ✅ Deployment: `1/1` ready
- ✅ Pod status: `Running`
- ✅ Ready: `1/1`
- ✅ No restarts

---

## 🔹 Step 8: Verify Service

```bash
kubectl get svc
kubectl describe svc python-service-devops
```

**Purpose**: Confirm the service is updated and has endpoints.

**Expected `get svc` Output**:
```
NAME                    TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
python-service-devops   NodePort   10.96.123.456   <none>        5000:32345/TCP   1h
```

**Expected `describe svc` Output (Excerpt)**:
```
Name:                     python-service-devops
Type:                     NodePort
Selector:                 app=python-app
Port:                     <unset>  5000/TCP
TargetPort:               5000/TCP
NodePort:                 <unset>  32345/TCP
Endpoints:                10.0.0.5:5000
```

**Success Indicators**:
- ✅ Service name: `python-service-devops`
- ✅ Type: `NodePort`
- ✅ Port mapping: `5000:32345/TCP`
- ✅ Endpoints: Populated (e.g., `10.0.0.5:5000`)

---

## 🔹 Step 9: Access Flask Application

**Action**: In the KodeKloud lab interface, click the **App** button to access the Flask application.

**Purpose**: Verify the application is accessible via the NodePort (32345).

**Expected Output** (via browser):
```
Hello World Pyvo 1!
```

**Alternative CLI Test** (if no App button is available):
```bash
kubectl get nodes -o wide
```

**Note the node’s external IP** (e.g., `192.168.1.100`), then:
```bash
curl http://<node-ip>:32345
```

**Expected Output**:
```
Hello World Pyvo 1!
```

---

## 🔹 Step 10: Verify Pod Details (Optional)

```bash
kubectl describe pod -l app=python-app
```

**Purpose**: Confirm the pod’s configuration, including the corrected image.

**Expected Output (Excerpt)**:
```
Name:         python-deployment-devops-xyz123456-abc789
Namespace:    default
Containers:
  python-container:
    Image:        poroko/flask-demo-app
    Port:         5000/TCP
    State:        Running
```

---

## 🔹 Step 11: Check Flask Logs (Optional)

```bash
kubectl logs -l app=python-app
```

**Purpose**: Verify the Flask container started correctly.

**Expected Logs** (example):
```
 * Serving Flask app 'app'
 * Running on http://0.0.0.0:5000
```

---

## 🔹 Step 12: Clean Up (Optional)

```bash
kubectl delete deployment python-deployment-devops
kubectl delete svc python-service-devops
```

**Purpose**: Remove the deployment and service if testing is complete.

**Expected Output**:
```
deployment.apps "python-deployment-devops" deleted
service "python-service-devops" deleted
```

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **Jump Host**:

```bash
# Connect to jump host
ssh thor@jump_host

# Investigate issues
kubectl get deployments
kubectl get pods
kubectl get svc
kubectl describe deployment python-deployment-devops
kubectl describe svc python-service-devops

# Fix deployment and service
kubectl edit deployment python-deployment-devops  # Change image to poroko/flask-demo-app
kubectl edit svc python-service-devops          # Change port and targetPort to 5000

# Verify
kubectl get deployments
kubectl get pods
kubectl get svc
kubectl describe svc python-service-devops
```

---

## 💡 Common Kubernetes Deployment/Service Issues & Fixes

### **Issue 1: Image Pull Failure**
**Problem**: Pods in `ErrImagePull` or `ImagePullBackOff` due to `poroko/flask-app-demo`.
**Fix**: Correct the image name to `poroko/flask-demo-app`.
```bash
kubectl edit deployment python-deployment-devops
docker pull poroko/flask-demo-app
```

### **Issue 2: Service No Endpoints**
**Problem**: `kubectl describe svc` shows `<none>` for endpoints.
**Fix**: Correct `port` and `targetPort` to `5000`.
```bash
kubectl edit svc python-service-devops
kubectl get endpoints python-service-devops
```

### **Issue 3: Pods Not Running**
**Problem**: Pods in `CrashLoopBackOff` or `Error`.
**Fix**: Check logs and events.
```bash
kubectl logs -l app=python-app
kubectl describe pod -l app=python-app
```

### **Issue 4: Service Not Accessible**
**Problem**: Accessing `<node-ip>:32345` fails.
**Fix**: Verify service configuration and pod selector.
```bash
kubectl describe svc python-service-devops
kubectl get pods -l app=python-app
```

---

## 🔧 Troubleshooting Deployment/Service Failures

### **Error: Pod Not Running**
**Symptoms**: `kubectl get pods` shows `Error` or `CrashLoopBackOff`.
**Solution**: Check logs and events.
```bash
kubectl logs -l app=python-app
kubectl get events --field-selector involvedObject.name=python-deployment-devops
```

### **Error: Service Not Routing**
**Symptoms**: Accessing `<node-ip>:32345` returns no response.
**Solution**: Check service endpoints and selector.
```bash
kubectl get endpoints python-service-devops
kubectl describe svc python-service-devops
```

### **Error: Image Not Found**
**Symptoms**: "Failed to pull image poroko/flask-app-demo".
**Solution**: Ensure correct image name.
```bash
kubectl describe pod -l app=python-app
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was identifying and fixing two misconfigurations: an incorrect image name (`poroko/flask-app-demo`) and incorrect service ports (`8080` instead of `5000`), which prevented the Flask application from running and being accessible.

**💡 Solution Approach**:
1. **Issue Identification**: Used `kubectl describe` to confirm the image and port issues.
2. **Deployment Correction**: Updated the image to `poroko/flask-demo-app`.
3. **Service Correction**: Changed `port` and `targetPort` to `5000`, retaining `nodePort: 32345`.
4. **Verification**: Confirmed pod status, service endpoints, and application accessibility via the App button.

**🎯 Key Success Factors**:
- **Accurate Diagnosis**: Identified incorrect image and port settings.
- **Correct Fixes**: Updated image to `poroko/flask-demo-app` and ports to `5000`.
- **Verification**: Ensured pods are `Running` and service has endpoints.
- **Testing**: Confirmed `Hello World Pyvo 1!` via the App button.

**⚠️ Critical Fixes**:
- Correct image name from `poroko/flask-app-demo` to `poroko/flask-demo-app`.
- Update service `port` and `targetPort` to `5000`.
- Retain `nodePort: 32345`.
- Verify selector labels match between deployment and service.

**🔒 Kubernetes Best Practices Applied**:
- **Troubleshooting**: Used `kubectl describe` and `kubectl logs` for systematic diagnosis.
- **Minimal Changes**: Modified only the necessary fields (image, ports).
- **Verification**: Confirmed service endpoints and pod status.
- **Restart Strategy**: Leveraged `kubectl edit` for quick fixes.

**⚠️ Important Troubleshooting Concepts**:
- **Service Endpoints**: Check `kubectl get endpoints` to ensure service routing.
- **Pod Logs**: Use `kubectl logs` to diagnose application issues.
- **Events**: Use `kubectl get events` to identify deployment or service issues.
- **NodePort Access**: Verify node IP and port accessibility.

---

## ⚠️ Important Production Notes

🔧 **Deployment Debugging**: Use `kubectl describe`, `kubectl logs`, and `kubectl get endpoints` for issue resolution.
🔐 **Image Security**: Pin specific image versions (e.g., `poroko/flask-demo-app:1.0`) in production to avoid unexpected updates.
📊 **Resource Limits**: Add `resources` (e.g., CPU/memory limits) to prevent overuse.
🛡️ **Service Exposure**: Use Ingress or LoadBalancer for production instead of NodePort.

*Last Updated: October 25, 2025*