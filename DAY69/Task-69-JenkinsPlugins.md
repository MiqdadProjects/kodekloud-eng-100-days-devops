# 🌟 Task 69 - Install Git and GitLab Plugins in Jenkins

**📌 Task Description**

The **Nautilus DevOps team** has set up a Jenkins server for CI/CD pipelines and needs to install specific plugins to support their jobs. The task involves accessing the Jenkins UI, logging in with provided credentials, installing the Git and GitLab plugins, restarting Jenkins if necessary, and verifying the plugin installation.

**👉 Task Requirements**:
- **Access Jenkins UI**:
  - Click the **Jenkins** button on the top bar to access the Jenkins UI (typically at `http://<jenkins-server-ip>:8080`).
  - Log in with:
    - Username: `admin`
    - Password: `Adm!n321`
- **Install Plugins**:
  - Install the **Git** and **GitLab** plugins via the Jenkins UI.
  - If prompted, select **Restart Jenkins when installation is complete and no jobs are running** on the plugin installation/update page (Update Center).
- **Post-Restart**:
  - Wait for the Jenkins login page to reappear after the restart before proceeding.
- **Verification**:
  - Verify that the Git and GitLab plugins are installed.
- **Documentation**:
  - Capture screenshots of the Jenkins UI steps (e.g., login, plugin installation, verification) for review.
  - Optionally, use screen recording software (e.g., loom.com) to document the process.
- Use the Jenkins UI on the Jenkins server, accessible via the jump host.

**💡 Note**: Infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Access Jenkins UI

**Action**: In the KodeKloud lab interface, click the **Jenkins** button to open the Jenkins UI (typically at `http://<jenkins-server-ip>:8080`).

**Purpose**: Access the Jenkins web interface to perform plugin installation.

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

## 🔹 Step 2: Navigate to Plugin Management

**Steps**:
1. From the Jenkins dashboard, click **Manage Jenkins** (left sidebar or main page).
2. Click **Plugins** (or **Manage Plugins** in some versions).

**Purpose**: Access the plugin management interface to install new plugins.

**Success Indicator**:
- ✅ Redirects to the Plugins page (URL: `http://<jenkins-server-ip>:8080/pluginManager`).

**Screenshot**: Capture the **Manage Jenkins** and **Plugins** pages.

---

## 🔹 Step 3: Search for Git and GitLab Plugins

**Steps**:
1. On the Plugins page, click the **Available plugins** tab.
2. In the search bar, type `Git` to locate the **Git** plugin.
3. Select the checkbox for the **Git** plugin.
4. In the search bar, type `GitLab` to locate the **GitLab** plugin.
5. Select the checkbox for the **GitLab** plugin.

**Purpose**: Identify and select the required plugins for installation.

**Success Indicators**:
- ✅ **Git** plugin appears in search results (e.g., "Git plugin").
- ✅ **GitLab** plugin appears in search results (e.g., "GitLab Plugin").
- ✅ Both plugins are checked for installation.

**Screenshot**: Capture the **Available plugins** tab with search results and selected plugins.

---

## 🔹 Step 4: Install Plugins

**Steps**:
1. Click **Install without restart** or **Download now and install after restart** (depending on the Jenkins version).
2. If prompted, check the box for **Restart Jenkins when installation is complete and no jobs are running**.
3. Click **Go** or **Install** to begin the installation process.
4. Monitor the installation progress on the Update Center page.

**Purpose**: Install the Git and GitLab plugins and initiate a Jenkins restart if required.

**Success Indicators**:
- ✅ Installation progress bar shows plugins downloading and installing.
- ✅ If restart is selected, Jenkins UI displays a "Preparing for shutdown" message.

**Screenshot**: Capture the installation progress and restart confirmation (if applicable).

---

## 🔹 Step 5: Wait for Jenkins Restart

**Steps**:
1. If a restart was triggered, wait for the Jenkins login page to reappear (typically 1-2 minutes).
2. Refresh the browser periodically to check if the UI is accessible again.
3. Once the login page reappears, log in again with:
   - **Username**: `admin`
   - **Password**: `Adm!n321`

**Purpose**: Ensure Jenkins restarts successfully and the UI becomes accessible.

**Success Indicators**:
- ✅ Jenkins login page reappears after restart.
- ✅ Successful re-login to the dashboard.

**Screenshot**: Capture the login page post-restart and the dashboard after re-login.

---

## 🔹 Step 6: Verify Plugin Installation

**Steps**:
1. From the Jenkins dashboard, navigate to **Manage Jenkins** > **Plugins**.
2. Click the **Installed plugins** tab.
3. Search for `Git` and `GitLab` to confirm both plugins are listed.

**Purpose**: Verify that the Git and GitLab plugins are installed successfully.

**Expected Output** (Installed Plugins page):
- **Git plugin** (e.g., version 5.x.x or higher).
- **GitLab Plugin** (e.g., version 1.x.x or higher).

**Success Indicators**:
- ✅ Both plugins appear in the **Installed plugins** list.
- ✅ No errors or warnings associated with the plugins.

**Screenshot**: Capture the **Installed plugins** tab showing the Git and GitLab plugins.

---

## 🔹 Step 7: Document the Process

**Steps**:
1. **Screenshots**: Capture screenshots for each step:
   - Jenkins login page.
   - Dashboard after login.
   - Manage Jenkins and Plugins pages.
   - Available plugins with Git and GitLab selected.
   - Installation progress and restart confirmation.
   - Installed plugins list.
2. **Screen Recording (Optional)**:
   - Use software like loom.com to record the entire process (login, navigation, plugin installation, restart, and verification).
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

**Purpose**: Confirm the Jenkins service is running after the restart.

**Expected Output (Excerpt)**:
```
● jenkins.service - Jenkins Continuous Integration Server
   Active: active (running) since Sun 2025-10-26 16:45:00 UTC; 5min ago
```

**Success Indicator**:
- ✅ Service status: `active (running)`

---

## 📋 Quick Reference Guide

**Jenkins UI Steps**:
1. Click **Jenkins** button to access UI.
2. Log in:
   - Username: `admin`
   - Password: `Adm!n321`
3. Navigate: **Manage Jenkins** > **Plugins** > **Available plugins**.
4. Search and select: **Git** and **GitLab** plugins.
5. Click **Install** and check **Restart Jenkins when installation is complete**.
6. Wait for restart and re-login.
7. Verify: **Manage Jenkins** > **Plugins** > **Installed plugins**.
8. Capture screenshots for each step or record the process (e.g., via loom.com).

**SSH Verification (Optional)**:
```bash
ssh root@jenkins
sudo systemctl status jenkins
```

---

## 💡 Common Jenkins Plugin Installation Issues & Fixes

### **Issue 1: Login Failure**
**Problem**: Invalid credentials (`admin`/`Adm!n321`).
**Fix**: Verify credentials and reset password if needed.
```bash
ssh root@jenkins
cat /var/lib/jenkins/secrets/initialAdminPassword
```
- If the admin user is misconfigured, edit `/var/lib/jenkins/config.xml` to disable security temporarily (set `<useSecurity>false</useSecurity>`), restart Jenkins, and reconfigure the admin user.

### **Issue 2: Plugin Installation Failure**
**Problem**: Plugins fail to download or install.
**Fix**: Check network connectivity and Jenkins Update Center.
```bash
curl -I https://updates.jenkins.io/update-center.json
```
- Retry installation or manually download plugins from `https://plugins.jenkins.io`.

### **Issue 3: Jenkins Restart Hangs**
**Problem**: Jenkins UI doesn’t reappear after restart.
**Fix**: Verify service status and restart manually.
```bash
ssh root@jenkins
sudo systemctl status jenkins
sudo systemctl restart jenkins
```

### **Issue 4: Plugins Not Listed**
**Problem**: Git or GitLab plugins not visible in **Installed plugins**.
**Fix**: Confirm installation and restart completion.
```bash
ssh root@jenkins
ls /var/lib/jenkins/plugins
```

---

## 🔧 Troubleshooting Plugin Installation Failures

### **Error: UI Not Accessible**
**Symptoms**: Jenkins UI doesn’t load after clicking the **Jenkins** button.
**Solution**: Verify service status and port `8080`.
```bash
ssh root@jenkins
sudo systemctl status jenkins
netstat -tuln | grep 8080
```

### **Error: Plugin Not Found**
**Symptoms**: Git or GitLab plugins not listed in **Available plugins**.
**Solution**: Refresh the plugin list or check the Update Center.
- In Jenkins UI, go to **Manage Jenkins** > **Plugins** > **Advanced** > **Check now**.
- Ensure internet connectivity for the Jenkins server.

### **Error: Restart Failure**
**Symptoms**: Jenkins doesn’t restart after plugin installation.
**Solution**: Manually restart and check logs.
```bash
ssh root@jenkins
sudo systemctl restart jenkins
journalctl -u jenkins.service
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was accessing the Jenkins UI, installing the Git and GitLab plugins, ensuring a successful restart, and verifying the installation, all while documenting the process with screenshots or a screen recording.

**💡 Solution Approach**:
1. **UI Access**: Logged into Jenkins with `admin`/`Adm!n321` via the **Jenkins** button.
2. **Plugin Installation**: Navigated to **Manage Jenkins** > **Plugins**, selected Git and GitLab plugins, and installed with a restart.
3. **Restart Management**: Waited for the Jenkins UI to reappear post-restart and re-logged in.
4. **Verification**: Confirmed plugin installation in the **Installed plugins** tab.
5. **Documentation**: Captured screenshots for each step and suggested screen recording for clarity.

**🎯 Key Success Factors**:
- **Correct Credentials**: Used `admin`/`Adm!n321` for login.
- **Plugin Selection**: Installed exact plugins (Git and GitLab).
- **Restart Handling**: Ensured Jenkins restarted successfully.
- **Verification**: Confirmed plugins in the **Installed plugins** list.
- **Documentation**: Provided clear screenshot guidance.

**⚠️ Critical Fixes**:
- Ensure correct credentials (`admin`/`Adm!n321`).
- Select both Git and GitLab plugins accurately.
- Wait for the Jenkins UI to reappear post-restart before proceeding.
- Verify plugin installation to avoid missing plugins.

**🔒 Best Practices Applied**:
- **UI Navigation**: Followed the standard Jenkins plugin installation process.
- **Restart Management**: Used the safe restart option to avoid job interruptions.
- **Documentation**: Emphasized screenshots or screen recordings for transparency.
- **Verification**: Checked installed plugins to confirm task completion.

**⚠️ Important Troubleshooting Concepts**:
- **Service Status**: Use `systemctl status jenkins` to diagnose restart issues.
- **Plugin Verification**: Check `/var/lib/jenkins/plugins` for installed plugin files.
- **Network Issues**: Ensure Jenkins can access `updates.jenkins.io`.
- **UI Errors**: Monitor the Jenkins UI for error messages during installation.

---

## ⚠️ Important Production Notes

🔧 **Plugin Management**: Regularly update plugins via **Manage Jenkins** > **Plugins** > **Updates**.
🔐 **Security**: Restrict Jenkins UI access with authentication and HTTPS in production.
📊 **Monitoring**: Monitor Jenkins server resources to prevent plugin installation failures.
🛡️ **Backup**: Back up `/var/lib/jenkins` before major plugin updates or restarts.
📹 **Documentation**: Store screenshots or recordings in a version-controlled repository for audit purposes.

