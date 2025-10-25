# 🌟 Task 53 - Troubleshoot and Fix Nginx-PHP-FPM Pod Issue in Kubernetes

**📌 Task Description**

The **Nautilus DevOps team** encountered an issue this morning with an Nginx and PHP-FPM setup running in a Kubernetes pod, causing the application to stop functioning. The team needs to investigate and fix the issue with the pod named `nginx-phpfpm`, which uses a ConfigMap named `nginx-config`. After resolving the issue, a file (`index.php`) must be copied from the jump host to the Nginx container’s document root, and the application should be accessible via the website button in the KodeKloud lab interface.

**👉 Task Requirements**:
- Identify and fix the issue with the `nginx-phpfpm` pod, using the `nginx-config` ConfigMap.
- Copy `/home/thor/index.php` from the jump host to the `nginx-container`’s document root (`/var/www/html`).
- Verify the application is accessible via the **Website** button, displaying the PHP page.
- Use `kubectl` on the jump host, which is pre-configured to interact with the Kubernetes cluster.

**💡 Note**: Infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to the Jump Host

```bash
ssh thor@jump_host
```

**Purpose**: Connect to the jump host where `kubectl` is configured to manage the Kubernetes cluster.

---

## 🔹 Step 2: Investigate the Pod Issue

```bash
kubectl describe pod nginx-phpfpm
```

**Purpose**: Examine the pod’s configuration to identify the issue.

**Key Findings**:
- **ConfigMap (`nginx-config`)**: Specifies `root /var/www/html;` for Nginx’s document root.
- **Volume Mounts**:
  - `php-fpm-container`: Mounts the shared volume at `/var/www/html` (✅ correct).
  - `nginx-container`: Mounts the shared volume at `/usr/share/nginx/html` (❌ incorrect).
- **Issue**: Nginx serves content from `/usr/share/nginx/html`, but PHP-FPM reads/writes to `/var/www/html`, causing Nginx to fail to find PHP files (e.g., `index.php`) for processing.

**Problem Summary**: The misaligned volume mount paths prevent Nginx from accessing PHP files processed by PHP-FPM, breaking the application.

---

## 🔹 Step 3: Edit the Pod Configuration

```bash
kubectl edit pod nginx-phpfpm
```

**Purpose**: Modify the pod’s YAML to correct the `nginx-container` volume mount path.

**Change Required**:
- Update the `nginx-container`’s volume mount from:
  ```yaml
  - mountPath: /usr/share/nginx/html
    name: shared-files
  ```
  to:
  ```yaml
  - mountPath: /var/www/html
    name: shared-files
  ```

**Save and Exit**: Press `Esc`, type `:wq`, press `Enter`.

**Note**: Editing the pod directly creates a temporary YAML file (e.g., `/tmp/kubectl-edit-2234947867.yaml`) with the updated configuration.

---

## 🔹 Step 4: Delete and Recreate the Pod

```bash
kubectl delete pod nginx-phpfpm
kubectl apply -f /tmp/kubectl-edit-2234947867.yaml
```

**Purpose**: Delete the existing pod and apply the corrected YAML to recreate the pod with the proper volume mount.

**Expected Output (Delete)**:
```
pod "nginx-phpfpm" deleted
```

**Expected Output (Apply)**:
```
pod/nginx-phpfpm created
```

**Reference Pod YAML (for clarity)**:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-phpfpm
spec:
  containers:
    - name: nginx-container
      image: nginx:latest
      volumeMounts:
        - mountPath: /var/www/html
          name: shared-files
    - name: php-fpm-container
      image: php:7.4-fpm
      volumeMounts:
        - mountPath: /var/www/html
          name: shared-files
  volumes:
    - name: shared-files
      emptyDir: {}
```

**Note**: The actual YAML may include additional configurations (e.g., ConfigMap mounts for `nginx-config`). Ensure the `nginx-container` mount path is corrected to `/var/www/html`.

---

## 🔹 Step 5: Verify Pod Status

```bash
kubectl get pods
```

**Purpose**: Confirm the `nginx-phpfpm` pod is running with both containers.

**Expected Output**:
```
NAME           READY   STATUS    RESTARTS   AGE
nginx-phpfpm   2/2     Running   0          30s
```

**Success Indicators**:
- ✅ Pod name: `nginx-phpfpm`
- ✅ Status: `Running`
- ✅ Ready: `2/2` (both `nginx-container` and `php-fpm-container`)
- ✅ No restarts

---

## 🔹 Step 6: Verify Pod Configuration

```bash
kubectl describe pod nginx-phpfpm
```

**Purpose**: Confirm the corrected volume mount path for `nginx-container`.

**Expected Output (Excerpt)**:
```
Containers:
  nginx-container:
    Image:          nginx:latest
    Volume Mounts:
      /var/www/html from shared-files (rw)
  php-fpm-container:
    Image:          php:7.4-fpm
    Volume Mounts:
      /var/www/html from shared-files (rw)
Volumes:
  shared-files:
    Type:       EmptyDir
```

**Success Indicators**:
- ✅ `nginx-container` mounts `shared-files` at `/var/www/html`
- ✅ `php-fpm-container` mounts `shared-files` at `/var/www/html`
- ✅ Both containers share the same volume path

---

## 🔹 Step 7: Copy index.php to the Nginx Container

```bash
kubectl cp /home/thor/index.php nginx-phpfpm:/var/www/html/index.php -c nginx-container
```

**Purpose**: Copy the `index.php` file from the jump host to the `nginx-container`’s document root (`/var/www/html`).

**Expected Output**: No output on success (silent operation).

**Verify File Copy**:
```bash
kubectl exec nginx-phpfpm -c nginx-container -- ls -l /var/www/html/index.php
```

**Expected Output**:
```
-rw-r--r-- 1 root root 123 Oct 25 15:50 /var/www/html/index.php
```

---

## 🔹 Step 8: Test Application Accessibility

**Action**: In the KodeKloud lab interface, click the **Website** button to access the Nginx-PHP-FPM application.

**Purpose**: Verify the application is functional and displays the PHP page.

**Expected Output** (via browser):
- A PHP page rendered correctly (content depends on `index.php`).

**Alternative CLI Test** (if no website button is available):
```bash
kubectl port-forward pod/nginx-phpfpm 8080:80
```

**In a new terminal**:
```bash
curl http://localhost:8080/index.php
```

**Expected Output**: PHP page content (depends on `index.php`).

---

## 🔹 Step 9: Check Container Logs

```bash
kubectl logs nginx-phpfpm -c nginx-container
kubectl logs nginx-phpfpm -c php-fpm-container
```

**Purpose**: Verify both containers started correctly and are processing requests.

**Expected Logs (nginx-container)**:
```
[notice] 1#1: start worker processes
[notice] 1#1: start worker process 32
```

**Expected Logs (php-fpm-container)**:
```
[25-Oct-2025 15:50:00] NOTICE: fpm is running, pid 1
[25-Oct-2025 15:50:00] NOTICE: ready to handle connections
```

---

## 🔹 Step 10: Clean Up (Optional)

```bash
kubectl delete pod nginx-phpfpm
```

**Purpose**: Remove the pod if testing is complete.

**Expected Output**:
```
pod "nginx-phpfpm" deleted
```

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **Jump Host**:

```bash
# Connect to jump host
ssh thor@jump_host

# Investigate and fix pod
kubectl describe pod nginx-phpfpm
kubectl edit pod nginx-phpfpm  # Change nginx-container mountPath to /var/www/html
kubectl delete pod nginx-phpfpm
kubectl apply -f /tmp/kubectl-edit-2234947867.yaml

# Copy file and verify
kubectl cp /home/thor/index.php nginx-phpfpm:/var/www/html/index.php -c nginx-container
kubectl exec nginx-phpfpm -c nginx-container -- ls -l /var/www/html/index.php
kubectl get pods
kubectl describe pod nginx-phpfpm
```

---

## 💡 Common Kubernetes Pod Issues & Fixes

### **Issue 1: Pod Stuck in Pending**
**Problem**: `kubectl get pods` shows `Pending` status.
**Fix**: Check events for scheduling issues.
```bash
kubectl describe pod nginx-phpfpm
```
**Common Causes**: Insufficient resources or node issues.

### **Issue 2: Nginx Cannot Find PHP Files**
**Problem**: Nginx returns 404 errors for PHP files.
**Fix**: Verify volume mount paths align for both containers.
```bash
kubectl describe pod nginx-phpfpm
```

### **Issue 3: File Copy Failure**
**Problem**: `kubectl cp` fails with "no such file or directory".
**Fix**: Verify source and destination paths.
```bash
ls -l /home/thor/index.php
kubectl exec nginx-phpfpm -c nginx-container -- ls -l /var/www/html
```

### **Issue 4: PHP-FPM Not Processing Requests**
**Problem**: PHP pages fail to render.
**Fix**: Check PHP-FPM logs and Nginx configuration.
```bash
kubectl logs nginx-phpfpm -c php-fpm-container
kubectl exec nginx-phpfpm -c nginx-container -- cat /etc/nginx/conf.d/default.conf
```

---

## 🔧 Troubleshooting Pod Failures

### **Error: Pod Not Running**
**Symptoms**: `kubectl get pods` shows `Error` or `CrashLoopBackOff`.
**Solution**: Check logs and events for both containers.
```bash
kubectl logs nginx-phpfpm -c nginx-container
kubectl logs nginx-phpfpm -c php-fpm-container
kubectl get events --field-selector involvedObject.name=nginx-phpfpm
```

### **Error: Volume Mount Misconfiguration**
**Symptoms**: Nginx cannot access PHP files.
**Solution**: Ensure both containers mount the shared volume at `/var/www/html`.
```bash
kubectl describe pod nginx-phpfpm
```

### **Error: kubectl cp Fails**
**Symptoms**: "error: tar: unexpected EOF" or similar.
**Solution**: Verify file exists and container is running.
```bash
kubectl get pods
ls -l /home/thor/index.php
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was identifying and fixing the misaligned volume mount paths in the `nginx-phpfpm` pod, where `nginx-container` used `/usr/share/nginx/html` while `php-fpm-container` used `/var/www/html`, causing Nginx to fail to find PHP files. Additionally, copying `index.php` and verifying application functionality was required.

**💡 Solution Approach**:
1. **Issue Identification**: Used `kubectl describe` to find the mismatched volume mount paths.
2. **Pod Correction**: Edited the pod to align `nginx-container`’s mount path to `/var/www/html`.
3. **Pod Recreation**: Deleted and reapplied the corrected pod configuration.
4. **File Copy**: Copied `index.php` to the Nginx document root using `kubectl cp`.
5. **Verification**: Confirmed pod status and application accessibility via the website button.

**🎯 Key Success Factors**:
- **Accurate Diagnosis**: Identified the volume mount mismatch using `kubectl describe`.
- **Correct Mount Path**: Aligned `nginx-container` to `/var/www/html`.
- **File Copy**: Successfully copied `index.php` to the correct location.
- **Testing**: Validated PHP page rendering via the website button.
- **Verification**: Confirmed pod and container status post-fix.

**⚠️ Critical Fixes**:
- Correct `nginx-container` volume mount to `/var/www/html`.
- Ensure `shared-files` volume is used consistently across containers.
- Verify `index.php` exists on the jump host before copying.
- Validate Nginx configuration in `nginx-config` ConfigMap.

**🔒 Kubernetes Best Practices Applied**:
- **Shared Volumes**: Ensured consistent mount paths for multi-container pods.
- **Troubleshooting**: Used `kubectl describe` and `kubectl logs` for systematic diagnosis.
- **File Management**: Verified file paths before `kubectl cp`.
- **Validation**: Confirmed functionality with direct testing.

**⚠️ Important Troubleshooting Concepts**:
- **Volume Mounts**: Verify mount paths align with application configuration.
- **Logs**: Check both containers’ logs for errors.
- **Events**: Use `kubectl get events` to diagnose pod issues.
- **ConfigMap**: Ensure `nginx-config` aligns with volume mounts.

---

## ⚠️ Important Production Notes

🔧 **Pod Debugging**: Use `kubectl describe`, `kubectl logs`, and `kubectl exec` for thorough issue resolution.
🔐 **Security**: Restrict access to the PHP application with proper Nginx configuration and network policies.
📊 **Monitoring**: Use `kubectl top` to monitor resource usage for Nginx and PHP-FPM containers.
🛡️ **Service Exposure**: Configure a Kubernetes service (e.g., ClusterIP or NodePort) for production access.

*Last Updated: October 25, 2025*
