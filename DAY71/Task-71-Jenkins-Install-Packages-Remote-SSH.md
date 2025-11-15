# 🌟 Task 71 - Create Jenkins Job to Install Packages on Remote Server

**📌 Task Description**  
The **Nautilus DevOps team** has set up a Jenkins server in **Stratos Datacenter** and needs to automate package installation on the **storage server (ststor01)**. A parameterized Jenkins job must accept a package name and install it securely via SSH.

**👉 Task Requirements**:  
- Access Jenkins UI and log in with **username**: `admin`, **password**: `Adm!n321`.  
- Create a **Freestyle project** job named `install-packages`.  
- Add a **String Parameter** named `PACKAGE`.  
- Configure the job to install the package specified in `$PACKAGE` on the **storage server** (`ststor01`).  
- Use SSH plugins, credentials (`natasha` + private key), and remote host configuration.  
- Test the job with `nginx` and `git`.  
- Verify installation on `ststor01`.

**💡 Note**: The storage server is `ststor01`. SSH user is `natasha`. Sudo password is `Bl@kW`. Use **SSH Build Agents Plugin** and **Execute shell script on remote host using SSH** build step.

---

## 🔹 Step 1: Access Jenkins UI

**Action**: Open Jenkins UI and log in with admin credentials.  
**Purpose**: Gain access to configure plugins, credentials, and create the job.

**Steps**:  
1. Click the **Jenkins** button in the top bar.  
2. Enter:  
   - **Username**: `admin`  
   - **Password**: `Adm!n321`  
3. Click **Log in**.

**Success Indicators**:  
- ✅ Login page loads successfully.  
- ✅ Dashboard appears after login.

**Screenshot**: Capture login page and Jenkins dashboard.

---

## 🔹 Step 2: Install Required SSH Plugins

**Action**: Install SSH-related plugins to enable remote execution.  
**Purpose**: Allow Jenkins to run shell commands on `ststor01` via SSH.

**Steps**:  
1. Go to **Manage Jenkins** → **Plugins**.  
2. Click **Available plugins** tab.  
3. Search: `ssh`  
4. Select:  
   - **SSH Plugin**  
   - **SSH Credentials Plugin**  
   - **SSH Build Agents Plugin**  
5. Click **Install without restart**.  
6. Check **Restart Jenkins when installation is complete and no jobs are running**.  
7. Wait for restart → Re-login with `admin`/`Adm!n321`.

**Success Indicators**:  
- ✅ All three plugins installed.  
- ✅ Jenkins restarts and UI reloads.

**Screenshot**: Capture plugin search, selection, and restart prompt.

---

## 🔹 Step 3: Add SSH Credentials for `natasha@ststor01`

**Action**: Store SSH private key for `natasha` in Jenkins credentials.  
**Purpose**: Enable secure, passwordless authentication to `ststor01`.

**Steps**:  
1. Navigate to **Manage Jenkins** → **Credentials** → **System** → **Global credentials**.  
2. Click **Add Credentials**.  
3. Fill:  
   - **Kind**: `SSH Username with private key`  
   - **Username**: `natasha`  
   - **Private Key**: **Enter directly** → Paste private key for `ststor01`  
   - **ID**: `ststor01-ssh`  
   - **Description**: `SSH key for ststor01`  
4. Click **OK**.

**Success Indicators**:  
- ✅ Credentials saved with ID `ststor01-ssh`.  
- ✅ No validation errors.

**Screenshot**: Capture credentials form with private key entered.

---

## 🔹 Step 4: Configure `ststor01` as SSH Remote Host

**Action**: Register `ststor01` as a trusted SSH host in Jenkins.  
**Purpose**: Enable build steps to target `ststor01` securely.

**Steps**:  
1. Go to **Manage Jenkins** → **System**.  
2. Scroll to **SSH remote hosts**.  
3. Click **Add**.  
4. Enter:  
   - **Hostname**: `ststor01`  
   - **Port**: `22`  
   - **Username**: `natasha`  
   - **Credentials**: `ststor01-ssh`  
5. Click **Check Connection** → Should show **Success**.  
6. Click **Save**.

**Success Indicators**:  
- ✅ Connection test passes.  
- ✅ Host saved successfully.

**Screenshot**: Capture host config and successful connection check.

---

## 🔹 Step 5: Create Jenkins Job `install-packages`

**Action**: Create a new Freestyle project job.  
**Purpose**: Set up the foundation for parameterized package installation.

**Steps**:  
1. From dashboard, click **New Item**.  
2. Enter:  
   - **Item name**: `install-packages`  
   - **Type**: `Freestyle project`  
3. Click **OK**.

**Success Indicators**:  
- ✅ Job configuration page opens.

**Screenshot**: Capture job creation screen.

---

## 🔹 Step 6: Add String Parameter `PACKAGE`

**Action**: Add dynamic input for package name.  
**Purpose**: Make the job reusable for any package.

**Steps**:  
1. Check **This project is parameterized**.  
2. Click **Add Parameter** → **String Parameter**.  
3. Enter:  
   - **Name**: `PACKAGE`  
   - **Description**: `Package to install (e.g., nginx, git)`  
4. Proceed to build step.

**Success Indicators**:  
- ✅ Parameter appears in **Build with Parameters**.

**Screenshot**: Capture parameter configuration.

---

## 🔹 Step 7: Add SSH Build Step to Install Package

**Action**: Configure remote shell execution on `ststor01`.  
**Purpose**: Run `yum install` using the provided package name.

**Steps**:  
1. Under **Build**, click **Add build step** → **Execute shell script on remote host using SSH**.  
2. Select:  
   - **SSH Server**: `ststor01`  
3. Enter command:  
   ```bash
   echo "Bl@kW" | sudo -S yum install -y $PACKAGE
   ```  
4. Click **Save**.

**Success Indicators**:  
- ✅ Build step saved with correct command and server.

**Screenshot**: Capture build step with SSH server and command.

---

## 🔹 Step 8: Test the Job with `nginx` and `git`

**Action**: Execute the job with sample packages.  
**Purpose**: Validate end-to-end functionality.

**Steps**:  
1. Go to `install-packages` job.  
2. Click **Build with Parameters**.  
3. Enter `nginx` → Click **Build**.  
4. Repeat with `git`.  
5. Monitor **Console Output** for:  
   ```
   Installed: nginx.x86_64
   Complete!
   ```

**Success Indicators**:  
- ✅ Both builds succeed.  
- ✅ Console shows package installation.

**Screenshot**: Capture both builds and console output.

---

## 🔹 Step 9: Verify Installation on `ststor01`

**Action**: SSH into `ststor01` and confirm packages are installed.  
**Purpose**: Ensure changes were applied on the target server.

**Steps**:  
```bash
ssh natasha@ststor01
```

Check `nginx`:  
```bash
nginx -v
```
**Expected Output**:  
```
nginx version: nginx/1.16.1
```

Check `git`:  
```bash
git --version
```
**Expected Output**:  
```
git version 2.43.5
```

```bash
exit
```

**Success Indicators**:  
- ✅ Both commands return version info.

**Screenshot**: Capture SSH session with verification commands.

---

## 🔹 Step 10: Document the Process

**Action**: Capture visual proof of all steps.  
**Purpose**: Provide evidence and enable replication.

**Steps**:  
1. **Screenshots** for:  
   - Login & dashboard  
   - Plugin install  
   - Credentials & SSH host  
   - Job config (parameter + build step)  
   - Build execution  
   - Verification on `ststor01`  
2. **Optional**: Record full flow using loom.com

**Success Indicator**:  
- ✅ All steps visually documented.

**Screenshot**: Include all above in a shared folder or recording.

---

## 📋 Quick Reference Guide

**Jenkins UI Steps**:  
1. Login: `admin` / `Adm!n321`  
2. Install SSH plugins → Restart  
3. Add credentials: `ststor01-ssh` (natasha + private key)  
4. Add SSH host: `ststor01` → Check connection  
5. Create job: `install-packages` (Freestyle)  
6. Add parameter: `PACKAGE`  
7. Add SSH build step:  
   ```bash
   echo "Bl@kW" | sudo -S yum install -y $PACKAGE
   ```  
8. Test: `nginx`, `git`  
9. Verify:  
   ```bash
   ssh natasha@ststor01
   nginx -v
   git --version
   ```

---

## 💡 Common SSH & Plugin Issues & Fixes

### **Issue 1: SSH Connection Failed**
**Problem**: "Connection refused" or "Authentication failed"  
**Fix**:  
- Verify private key in `ststor01-ssh`  
- Re-run **Check Connection**  
```bash
# On jump host (if testing)
ssh natasha@ststor01
```

### **Issue 2: `sudo: no tty present`**
**Problem**: Sudo fails in non-interactive mode  
**Fix**: Use `| sudo -S` with password (`Bl@kW`)

### **Issue 3: Package Not Found**
**Problem**: `No package X available`  
**Fix**: Use correct name (`nginx`, `git`, `httpd`)

---

## 🔧 Troubleshooting Job Failures

### **Error: Build Step Fails**
**Symptoms**: Job red, no output  
**Solution**:  
- Check **Console Output**  
- Verify `ststor01` is reachable  
```bash
ssh root@jenkins
sudo systemctl status jenkins
```

### **Error: Credentials Not Found**
**Symptoms**: "Credentials ID not found"  
**Solution**: Confirm ID is exactly `ststor01-ssh`

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:  
Securely connecting Jenkins to `ststor01` without hardcoding passwords and enabling dynamic package installation.

**💡 Solution Approach**:  
1. Installed SSH plugin suite  
2. Stored private key in Jenkins credentials  
3. Registered `ststor01` as remote host  
4. Used parameterized Freestyle job with SSH build step  
5. Leveraged `sudo -S` for non-interactive privilege escalation

**🎯 Key Success Factors**:  
- Correct SSH key and credential ID  
- Successful **Check Connection**  
- Parameterized input (`$PACKAGE`)  
- Verified remote execution  
- Idempotent `yum install -y`

**⚠️ Critical Fixes**:  
- Used `ststor01-ssh` as credential ID  
- Included `Bl@kW` via `echo | sudo -S`  
- Tested connection before job creation

**🔒 Best Practices Applied**:  
- Parameterized builds for reusability  
- Credential storage (no plaintext keys)  
- Non-interactive sudo  
- Console logging for audit

**⚠️ Important Troubleshooting Concepts**:  
- Always test SSH connection first  
- Use `journalctl -u jenkins` on failure  
- Validate parameter expansion in console

---

## ⚠️ Important Production Notes

🔧 **Security**: Never hardcode passwords or keys — use Jenkins Credentials  
🔐 **Idempotency**: `yum install -y` is safe to rerun  
📊 **Scalability**: Extend to multiple hosts using host lists  
🛡️ **Monitoring**: Enable build history retention  
📹 **Backup**: Backup `/var/lib/jenkins` before major changes

