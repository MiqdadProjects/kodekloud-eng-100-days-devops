# 🌟 Task 73 - Copy Apache Logs from App Server 2 to Storage Server

**📌 Task Description**  
The **xFusionCorp Industries DevOps team** is setting up a **centralized logging system**. To troubleshoot Apache issues on **App Server 2**, they need access and error logs copied regularly. A Jenkins job must run **every 10 minutes** to securely transfer logs from `stapp02` to `ststor01`.

**👉 Task Requirements**:  
- Access Jenkins UI and log in with **username**: `admin`, **password**: `Adm!n321`.  
- Create a **Freestyle project** job named `copy-logs`.  
- Schedule build **every 10 minutes** using cron syntax.  
- Copy both Apache logs:  
  - `/var/log/httpd/access_log`  
  - `/var/log/httpd/error_log`  
- **Destination**: `/usr/src/sysops` on Storage Server (`ststor01`).  
- Use SSH + SCP via `steve@stapp02` and `natasha@ststor01`.  
- Verify logs are copied successfully.

**💡 Note**: Use SSH plugins, credentials, and remote hosts. Password: `Bl@kW`. No interactive prompts.

---

## 🔹 Step 1: Access Jenkins UI

**Action**: Open Jenkins UI and log in with admin credentials.  
**Purpose**: Gain access to install plugins and configure job.

**Steps**:  
1. Click the **Jenkins** button in the top bar.  
2. Enter:  
   - **Username**: `admin`  
   - **Password**: `Adm!n321`  
3. Click **Log in**.

**Success Indicators**:  
- ✅ Login page loads successfully.  
- ✅ Dashboard appears after login.

---

## 🔹 Step 2: Install Required SSH Plugins

**Action**: Install plugins for remote SSH execution and file transfer.  
**Purpose**: Enable secure communication with `stapp02` and `ststor01`.

**Steps**:  
1. Go to **Manage Jenkins** → **Plugins** → **Available plugins**.  
2. Search and install:  
   - **SSH Plugin**  
   - **SSH Credentials Plugin**  
   - **Publish Over SSH Plugin**  
3. Check **Restart Jenkins when installation is complete and no jobs are running**.  
4. Re-login after restart.

**Success Indicators**:  
- ✅ All 3 plugins installed.  
- ✅ Jenkins restarts successfully.

---

## 🔹 Step 3: Add SSH Credentials for steve@stapp02 and natasha@ststor01

**Action**: Store SSH credentials securely in Jenkins.  
**Purpose**: Avoid hardcoding passwords in job commands.

**Steps**:  
1. Go to **Manage Jenkins** → **Credentials** → **System** → **Global credentials**.  
2. Click **Add Credentials** → **Username with password**.  
3. **For App Server 2**:  
   - **Username**: `steve`  
   - **Password**: `Bl@kW`  
   - **ID**: `stapp02-cred`  
   - **Description**: `App Server 2 SSH Access`  
4. **Repeat for Storage Server**:  
   - **Username**: `natasha`  
   - **Password**: `Bl@kW`  
   - **ID**: `ststor01-cred`  
   - **Description**: `Storage Server SSH Access`  
5. Click **OK**.

**Success Indicators**:  
- ✅ Credentials saved with correct IDs.  
- ✅ No errors.

---

## 🔹 Step 4: Configure SSH Remote Hosts

**Action**: Register `stapp02` and `ststor01` as trusted SSH hosts.  
**Purpose**: Allow Jenkins to execute commands and transfer files.

**Steps**:  
1. Go to **Manage Jenkins** → **System** → **SSH remote hosts**.  
2. **Add stapp02**:  
   - **Hostname**: `stapp02`  
   - **Port**: `22`  
   - **Username**: `steve`  
   - **Credentials**: `stapp02-cred`  
   - Click **Check Connection** → **Success**  
3. **Add ststor01**:  
   - **Hostname**: `ststor01`  
   - **Port**: `22`  
   - **Username**: `natasha`  
   - **Credentials**: `ststor01-cred`  
   - Click **Check Connection** → **Success**  
4. Click **Save**.

**Success Indicators**:  
- ✅ Both hosts show **Success** in connection test.

---

## 🔹 Step 5: Create Jenkins Job `copy-logs`

**Action**: Create a new Freestyle project.  
**Purpose**: Foundation for scheduled log collection.

**Steps**:  
1. From dashboard, click **New Item**.  
2. Enter:  
   - **Item name**: `copy-logs`  
   - **Type**: **Freestyle project**  
3. Click **OK**.

**Success Indicators**:  
- ✅ Job configuration page opens.

---

## 🔹 Step 6: Schedule Build Every 10 Minutes

**Action**: Configure cron trigger.  
**Purpose**: Automate log collection.

**Steps**:  
1. Check **Build periodically**.  
2. Enter cron schedule:  
```
   H/10 * * * *
```  
   *(Runs every 10 minutes)*

**Success Indicators**:  
- ✅ Schedule saved correctly.

---

## 🔹 Step 7: Add SSH Build Step to Copy Logs

**Action**: Execute SCP command from `stapp02` to `ststor01`.  
**Purpose**: Transfer both Apache logs securely.

**Steps**:  
1. Under **Build**, click **Add build step** → **Execute shell script on remote host using SSH**.  
2. Select:  
   - **SSH Server**: `stapp02`  
3. Enter command:  
```bash
   sshpass -p "Bl@kW" scp -p -o StrictHostKeyChecking=no /var/log/httpd/* natasha@ststor01:/usr/src/sysops
```  
4. Click **Save**.

**Success Indicators**:  
- ✅ Command saved.  
- ✅ No syntax errors.

---

## 🔹 Step 8: Trigger Build Manually

**Action**: Run job to test immediately.  
**Purpose**: Validate end-to-end functionality.

**Steps**:  
1. Go to `copy-logs` job.  
2. Click **Build Now**.  
3. Monitor **Console Output** for:  
```
   access_log  100%   XXMB
   error_log   100%   XXKB
```

**Success Indicators**:  
- ✅ Build succeeds.  
- ✅ Files transferred.

---

## 🔹 Step 9: Verify Logs on ststor01

**Action**: SSH into storage server and check files.  
**Purpose**: Confirm logs are copied correctly.

**Steps**:  
```bash
ssh natasha@ststor01
```
```bash
cd /usr/src/sysops/
ls -l
```

**Expected Output**:  
```
-rw-r--r-- 1 natasha natasha  ... access_log
-rw-r--r-- 1 natasha natasha  ... error_log
```
```bash
cat error_log | head
```

**Expected Output**:  
```
[Sat Nov 01 13:28:25.398041 2025] [suexec:notice] ...
AH00558: httpd: Could not reliably determine...
```
```bash
exit
```

**Success Indicators**:  
- ✅ Both files exist.  
- ✅ Content is readable.

---

## 🔹 Step 10: Document the Process

**Action**: Capture all configuration steps and build execution.  
**Purpose**: Enable replication and audit.

**Steps**:  
1. Document all configuration details:  
   - Login credentials  
   - Plugin installation  
   - Credentials configuration (2)  
   - SSH hosts configuration (2)  
   - Job configuration (cron + SCP)  
   - Build console output  
   - Verification on `ststor01`  

**Success Indicator**:  
- ✅ All steps documented.

---

## 📋 Quick Reference Guide

**Jenkins UI Steps**:  
1. **Login**: `admin` / `Adm!n321`  
2. **Install**: SSH, SSH Credentials, Publish Over SSH → Restart  
3. **Add Credentials**:  
   - `stapp02-cred`: steve / Bl@kW  
   - `ststor01-cred`: natasha / Bl@kW  
4. **Add SSH Hosts**:  
   - `stapp02` → steve + stapp02-cred  
   - `ststor01` → natasha + ststor01-cred  
5. **Create job**: `copy-logs` (Freestyle)  
6. **Schedule**: `H/10 * * * *`  
7. **Build Step** → Execute shell on remote host (stapp02):  
```bash
   sshpass -p "Bl@kW" scp -p -o StrictHostKeyChecking=no /var/log/httpd/* natasha@ststor01:/usr/src/sysops
```  
8. **Build Now** → Verify on ststor01:  
```bash
   ssh natasha@ststor01 "ls -l /usr/src/sysops/"
```

---

## 💡 Common SCP & SSH Issues & Fixes

### **Issue 1: sshpass: command not found**
**Problem**: `sshpass` missing on `stapp02`  
**Fix**:  
```bash
sudo yum install -y sshpass
```

### **Issue 2: Permission Denied**
**Problem**: `natasha@ststor01: Permission denied`  
**Fix**:  
- Ensure `Bl@kW` is correct  
- Check `/usr/src/sysops` exists and is writable

### **Issue 3: Host Key Verification Failed**
**Problem**: Host key verification failed  
**Fix**:  
- Use `-o StrictHostKeyChecking=no`

---

## 🔧 Troubleshooting Job Failures

### **Error: Connection Refused**
**Symptoms**: Cannot connect to port 22  
**Solution**:  
- Verify host is up  
- Check firewall:  
```bash
ssh steve@stapp02 "sudo firewall-cmd --list-all"
```

### **Error: File Not Found**
**Symptoms**: `/var/log/httpd/*: No such file or directory`  
**Solution**:  
- Confirm Apache is running:  
```bash
ssh steve@stapp02 "ls -l /var/log/httpd/"
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:  
Securely copying logs from App Server 2 to Storage Server using non-interactive SSH and scheduled Jenkins job.

**💡 Solution Approach**:  
1. Installed SSH + Publish Over SSH plugins  
2. Stored credentials securely in Jenkins  
3. Configured SSH remote hosts with connection test  
4. Used `sshpass` + `scp` in remote shell on `stapp02`  
5. Scheduled job every 10 minutes

**🎯 Key Success Factors**:  
- Correct credential IDs: `stapp02-cred`, `ststor01-cred`  
- Working **Check Connection**  
- `sshpass` + `scp` with `-p` and `StrictHostKeyChecking=no`  
- Verified file transfer and content  
- Cron: `H/10 * * * *`

**⚠️ Critical Fixes**:  
- Used `sshpass -p "Bl@kW"` for non-interactive auth  
- Added `-o StrictHostKeyChecking=no`  
- Ensured `/usr/src/sysops` exists on `ststor01`

**🔒 Best Practices Applied**:  
- Centralized logging prep  
- Secure credential storage  
- Idempotent file transfer  
- Scheduled automation  
- Full audit trail

**⚠️ Important Troubleshooting Concepts**:  
- Test SSH manually first  
- Use **Build Now** before scheduling  
- Check **Console Output** for SCP progress  
- Validate destination path permissions

---

## ⚠️ Important Production Notes

🔧 **Security**: Never hardcode passwords — use Jenkins Credentials or SSH keys  
🔐 **Reliability**: Use `H/10` to avoid thundering herd  
📊 **Scalability**: Extend to multiple app servers  
🛡️ **Monitoring**: Add email notification on failure  
📹 **Backup**: Rotate logs in `/usr/src/sysops` periodically

---

## ✅ Task Completion Checklist

- [ ] Logged into Jenkins with `admin`/`Adm!n321`  
- [ ] Installed SSH Plugin, SSH Credentials Plugin, Publish Over SSH Plugin  
- [ ] Added credentials: `stapp02-cred` (steve) and `ststor01-cred` (natasha)  
- [ ] Configured SSH remote hosts: `stapp02` and `ststor01`  
- [ ] Created job named `copy-logs`  
- [ ] Configured cron schedule: `H/10 * * * *`  
- [ ] Added SSH build step with SCP command  
- [ ] Triggered manual build successfully  
- [ ] Verified logs exist in `/usr/src/sysops` on `ststor01`  
- [ ] Documented all steps  

**🎉 Success Criteria Met When**:  
- Job builds successfully every 10 minutes  
- Both `access_log` and `error_log` are copied  
- Files are readable on `ststor01`  
- Console output shows 100% transfer completion  
