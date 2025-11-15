# 🌟 Task 70 - Configure Jenkins User Access with Matrix Authorization Strategy

**📌 Task Description**

The **Nautilus DevOps team** is integrating Jenkins into their CI/CD pipelines and needs to configure user access for the development team. The task involves creating a new Jenkins user, setting up the Project-based Matrix Authorization Strategy, assigning specific permissions to the user, ensuring the admin retains full permissions, removing Anonymous user permissions, and configuring job-specific permissions.

**👉 Task Requirements**:
- **Access Jenkins UI**:
  - Click the **Jenkins** button on the top bar to access the Jenkins UI (typically at `http://<jenkins-server-ip>:8080`).
  - Log in with:
    - Username: `admin`
    - Password: `Adm!n321`
- **Create User**:
  - Username: `ravi`
  - Password: `H85UJjhb`
  - Full Name: `Ravi`
- **Install Plugin**:
  - Install the **Matrix Authorization Strategy** plugin (version 3.2.8 or compatible).
  - Restart Jenkins if required, selecting **Restart Jenkins when installation is complete and no jobs are running**.
- **Configure Authorization**:
  - Use **Project-based Matrix Authorization Strategy**.
  - Assign permissions:
    - `ravi`: Overall **Read** permission only.
    - `admin`: Overall **Administer** permissions (all permissions).
    - **Anonymous**: No permissions.
- **Job Permissions**:
  - For the existing job, enable project-based security and grant `ravi` only **Job Read** permission.
- **Documentation**:
  - Capture screenshots of the Jenkins UI steps (e.g., login, user creation, plugin installation, permission configuration) for review.
  - Optionally, use screen recording software (e.g., loom.com) to document the process.

**💡 Note**: Infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface. The provided solution contains a typo in the password (`ksH85UJjhb`); the correct password is `H85UJjhb` as used in the user creation step.

---

## 🔹 Step 1: Access Jenkins UI

**Action**: In the KodeKloud lab interface, click the **Jenkins** button to open the Jenkins UI (typically at `http://<jenkins-server-ip>:8080`).

**Purpose**: Access the Jenkins web interface to perform user and permission configuration.

**Steps**:
1. Click the **Jenkins** button in the lab interface.
2. On the Jenkins login page, enter:
   - **Username**: `admin`
   - **Password**: `Adm!n321`
3. Click **Log in**.

**Success Indicators**:
- ✅ Jenkins login page loads successfully.
- ✅ Successful login redirects to the Jenkins dashboard.

**Screenshot**: Capture the login page and dashboard after successful login.

---

## 🔹 Step 2: Create User `ravi`

**Steps**:
1. From the Jenkins dashboard, click **Manage Jenkins** (left sidebar or main page).
2. Click **Users** (or **Manage Users** in some versions).
3. Click **Create User**.
4. Enter:
   - **Username**: `ravi`
   - **Password**: `H85UJjhb`
   - **Confirm Password**: `H85UJjhb`
   - **Full Name**: `Ravi`
   - **E-mail address**: (optional, can leave blank as not specified)
5. Click **Create User**.

**Purpose**: Create the `ravi` user with the specified credentials.

**Success Indicators**:
- ✅ User `ravi` appears in the **Users** list.
- ✅ Full name displayed as `Ravi`.

**Screenshot**: Capture the user creation form and the **Users** list after creation.

---

## 🔹 Step 3: Install Matrix Authorization Strategy Plugin

**Steps**:
1. From the Jenkins dashboard, click **Manage Jenkins** > **Plugins**.
2. Click the **Available plugins** tab.
3. In the search bar, type `Matrix Auth`.
4. Select the checkbox for **Matrix Authorization Strategy** (version 3.2.8 or latest compatible).
5. Click **Install without restart** or **Download now and install after restart**.
6. If prompted, check **Restart Jenkins when installation is complete and no jobs are running**.
7. Click **Go** or **Install** to begin installation.
8. If a restart is triggered, wait for the Jenkins login page to reappear (1-2 minutes).
9. Re-login with `admin`/`Adm!n321`.

**Purpose**: Install the Matrix Authorization Strategy plugin to enable fine-grained permission control.

**Success Indicators**:
- ✅ Plugin installation progress shows completion.
- ✅ If restarted, Jenkins UI reappears, and login is successful.
- ✅ Plugin listed in **Manage Jenkins** > **Plugins** > **Installed plugins**.

**Screenshot**: Capture the **Available plugins** tab with the plugin selected, installation progress, and post-restart login page.

---

## 🔹 Step 4: Configure Project-based Matrix Authorization Strategy

**Steps**:
1. From the Jenkins dashboard, click **Manage Jenkins** > **Security** (or **Configure Global Security**).
2. Under **Security Realm**, ensure **Jenkins’ own user database** is selected.
3. Under **Authorization**, select **Project-based Matrix Authorization Strategy**.
4. In the matrix, click **Add user or group** and enter `ravi`.
5. Click **Add user or group** and enter `admin`.
6. If **Anonymous** is listed, ensure it has no permissions.
7. Set permissions:
   - For `ravi`:
     - Check **Overall** > **Read** (only).
   - For `admin`:
     - Check all permissions (e.g., **Overall** > **Administer**, and all others).
   - For **Anonymous**:
     - Uncheck all permissions (or ensure none are checked).
8. Click **Save**.

**Purpose**: Configure global permissions to grant `ravi` read-only access, `admin` full access, and remove all Anonymous permissions.

**Success Indicators**:
- ✅ Authorization strategy set to **Project-based Matrix Authorization Strategy**.
- ✅ `ravi` has only **Overall/Read** permission.
- ✅ `admin` has all permissions (including **Overall/Administer**).
- ✅ **Anonymous** has no permissions.

**Screenshot**: Capture the **Security** page with the matrix showing permissions for `ravi`, `admin`, and **Anonymous**.

---

## 🔹 Step 5: Configure Job Permissions for `ravi`

**Steps**:
1. From the Jenkins dashboard, click the existing job (name not specified, assume a single job exists or check job list).
2. Click **Configure** in the job’s sidebar.
3. Check **Enable project-based security**.
4. In the permission matrix, click **Add user or group** and enter `ravi`.
5. For `ravi`, check **Job** > **Read** (only).
6. Ensure no other permissions (e.g., **Agent**, **SCM**) are checked for `ravi`.
7. Click **Save**.

**Purpose**: Grant `ravi` read-only access to the existing job, ensuring no other permissions are assigned.

**Success Indicators**:
- ✅ Project-based security enabled for the job.
- ✅ `ravi` has only **Job/Read** permission in the job’s matrix.

**Screenshot**: Capture the job’s **Configure** page with the permission matrix showing `ravi`’s **Job/Read** permission.

---

## 🔹 Step 6: Verify User and Permissions

**Steps**:
1. Log out of Jenkins (click **admin** > **Log out**).
2. Log in as `ravi`:
   - **Username**: `ravi`
   - **Password**: `H85UJjhb`
3. Verify `ravi` can:
   - View the Jenkins dashboard (read-only access).
   - View the existing job but not modify it.
4. Log out and re-login as `admin` (`admin`/`Adm!n321`).
5. Navigate to **Manage Jenkins** > **Users** to confirm `ravi`’s details.
6. Navigate to **Manage Jenkins** > **Security** to confirm global permissions.
7. Check the job’s **Configure** page to confirm `ravi`’s job permissions.

**Purpose**: Ensure `ravi` has the correct permissions and can access the dashboard and job in read-only mode.

**Success Indicators**:
- ✅ `ravi` can log in and view the dashboard and job.
- ✅ `ravi` cannot modify the job or access administrative functions.
- ✅ `admin` retains full permissions.
- ✅ **Anonymous** users cannot access the Jenkins UI.

**Screenshot**: Capture `ravi`’s login, dashboard view, job view, and permission matrices (global and job-specific).

---

## 🔹 Step 7: Document the Process

**Steps**:
1. **Screenshots**: Capture screenshots for each step:
   - Jenkins login page (`admin` and `ravi`).
   - User creation form and **Users** list.
   - Plugin installation (Matrix Authorization Strategy).
   - Global security configuration (permission matrix).
   - Job configuration (project-based security matrix).
   - `ravi`’s dashboard and job view.
2. **Screen Recording (Optional)**:
   - Use software like loom.com to record the entire process (login, user creation, plugin installation, permission configuration, verification).
   - Share the recording link for review.
3. Save screenshots or the recording in a shareable format (e.g., PNG for screenshots, URL for Loom).

**Purpose**: Provide visual documentation for review and verification.

**Success Indicator**:
- ✅ Screenshots or recording capture all required steps clearly.

---

## 🔹 Step 8: Verify Jenkins Service (Optional)

**Steps** (on the Jenkins server via SSH):
```bash
ssh root@jenkins
sudo systemctl status jenkins
```

**Purpose**: Confirm the Jenkins service is running after any restarts.

**Expected Output (Excerpt)**:
```
● jenkins.service - Jenkins Continuous Integration Server
   Active: active (running) since Sun 2025-10-26 17:00:00 UTC; 5min ago
```

**Success Indicator**:
- ✅ Service status: `active (running)`

---

## 📋 Quick Reference Guide

**Jenkins UI Steps**:
1. Click **Jenkins** button to access UI.
2. Log in: `admin`/`Adm!n321`.
3. Create user:
   - Navigate: **Manage Jenkins** > **Users** > **Create User**.
   - Username: `ravi`, Password: `H85UJjhb`, Full Name: `Ravi`.
4. Install plugin:
   - Navigate: **Manage Jenkins** > **Plugins** > **Available plugins**.
   - Search: `Matrix Auth`, select **Matrix Authorization Strategy**.
   - Install and restart Jenkins if prompted.
5. Configure global permissions:
   - Navigate: **Manage Jenkins** > **Security**.
   - Select **Project-based Matrix Authorization Strategy**.
   - Set: `ravi` (Overall/Read), `admin` (All), **Anonymous** (None).
   - Save.
6. Configure job permissions:
   - Navigate to job > **Configure**.
   - Enable project-based security.
   - Set: `ravi` (Job/Read only).
   - Save.
7. Verify:
   - Log in as `ravi`/`H85UJjhb`, check read-only access.
   - Re-login as `admin`, confirm permissions.
8. Capture screenshots or record the process (e.g., via loom.com).

**SSH Verification (Optional)**:
```bash
ssh root@jenkins
sudo systemctl status jenkins
```

---

## 💡 Common Jenkins User and Permission Issues & Fixes

### **Issue 1: Login Failure for `ravi`**
**Problem**: Invalid credentials (`ravi`/`H85UJjhb`).
**Fix**: Verify user creation and password.
- Navigate to **Manage Jenkins** > **Users** as `admin`.
- If needed, reset the password by editing `/var/lib/jenkins/users/ravi/config.xml` (requires SSH access).
```bash
ssh root@jenkins
cat /var/lib/jenkins/users/ravi/config.xml
```

### **Issue 2: Matrix Authorization Plugin Not Found**
**Problem**: Plugin not listed in **Available plugins**.
**Fix**: Refresh the plugin list or check network connectivity.
- In Jenkins UI, go to **Manage Jenkins** > **Plugins** > **Advanced** > **Check now**.
- Ensure internet access:
```bash
ssh root@jenkins
curl -I https://updates.jenkins.io/update-center.json
```

### **Issue 3: Permissions Not Applied**
**Problem**: `ravi` has incorrect permissions or cannot access the job.
**Fix**: Verify global and job-specific permission matrices.
- Check **Manage Jenkins** > **Security** for global permissions.
- Check job’s **Configure** page for project-based security settings.

### **Issue 4: Jenkins Restart Hangs**
**Problem**: Jenkins UI doesn’t reappear after restart.
**Fix**: Manually restart and check logs.
```bash
ssh root@jenkins
sudo systemctl restart jenkins
journalctl -u jenkins.service
```

---

## 🔧 Troubleshooting Configuration Failures

### **Error: UI Not Accessible**
**Symptoms**: Jenkins UI doesn’t load after clicking the **Jenkins** button.
**Solution**: Verify service status and port `8080`.
```bash
ssh root@jenkins
sudo systemctl status jenkins
netstat -tuln | grep 8080
```

### **Error: Anonymous Access Persists**
**Symptoms**: Anonymous users can still access Jenkins.
**Solution**: Ensure all Anonymous permissions are unchecked.
- Navigate to **Manage Jenkins** > **Security**, uncheck all Anonymous permissions, and save.

### **Error: Job Permissions Not Working**
**Symptoms**: `ravi` cannot view the job or has extra permissions.
**Solution**: Verify project-based security settings.
- Navigate to job’s **Configure** page, ensure only **Job/Read** is checked for `ravi`.

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was creating the `ravi` user, installing the Matrix Authorization Strategy plugin, configuring global and job-specific permissions, ensuring `admin` retains full control, removing Anonymous permissions, and documenting the process.

**💡 Solution Approach**:
1. **User Creation**: Created `ravi` with correct credentials via the Jenkins UI.
2. **Plugin Installation**: Installed the Matrix Authorization Strategy plugin and restarted Jenkins.
3. **Global Permissions**: Configured Project-based Matrix Authorization Strategy with `ravi` (Overall/Read), `admin` (all permissions), and Anonymous (none).
4. **Job Permissions**: Enabled project-based security for the job and set `ravi` to Job/Read only.
5. **Verification**: Tested `ravi`’s read-only access and `admin`’s full access.
6. **Documentation**: Captured screenshots for all steps and suggested screen recording.

**🎯 Key Success Factors**:
- **Correct Credentials**: Used `ravi`/`H85UJjhb` and `admin`/`Adm!n321`.
- **Plugin Installation**: Installed the correct version of Matrix Authorization Strategy.
- **Permission Configuration**: Set precise permissions for `ravi`, `admin`, and Anonymous.
- **Verification**: Confirmed `ravi`’s read-only access and `admin`’s full control.
- **Documentation**: Provided clear screenshot guidance.

**⚠️ Critical Fixes**:
- Corrected password typo (`ksH85UJjhb` to `H85UJjhb`).
- Ensured **Matrix Authorization Strategy** plugin is installed.
- Removed all Anonymous permissions explicitly.
- Verified job-specific permissions for `ravi`.

**🔒 Best Practices Applied**:
- **User Management**: Created users with secure credentials.
- **Permission Granularity**: Used Project-based Matrix Authorization for fine-grained control.
- **Security**: Removed Anonymous access to secure the instance.
- **Documentation**: Emphasized screenshots or recordings for transparency.
- **Verification**: Tested user access to ensure correct permissions.

**⚠️ Important Troubleshooting Concepts**:
- **User Verification**: Check **Manage Jenkins** > **Users** for user details.
- **Permission Issues**: Review global and job-specific permission matrices.
- **Service Status**: Use `systemctl status jenkins` to diagnose restart issues.
- **Plugin Installation**: Ensure network access to `updates.jenkins.io`.

---

## ⚠️ Important Production Notes

🔧 **Security**: Use HTTPS and a reverse proxy (e.g., Nginx) for Jenkins UI access in production.
🔐 **User Management**: Regularly audit user permissions and remove unused accounts.
📊 **Monitoring**: Monitor Jenkins server resources to prevent plugin installation or restart failures.
🛡️ **Backup**: Back up `/var/lib/jenkins` before making security changes.
📹 **Documentation**: Store screenshots or recordings in a version-controlled repository for audit purposes.

