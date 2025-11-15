# 🌟 Task 75 - Configure App Servers as Jenkins SSH Slave Nodes

**📌 Task Description**  
The **Nautilus DevOps team** has set up a new Jenkins server in **Stratos DC** for CI/CD and automation. All three app servers (`stapp01`, `stapp02`, `stapp03`) must be added as **SSH slave nodes** so Jenkins can run tasks remotely.

**👉 Task Requirements**:  
- Access Jenkins UI and log in with **username**: `admin`, **password**: `Adm!n321`.  
- Add **3 SSH slave nodes**:  
  - `App_server_1` → `stapp01`  
  - `App_server_2` → `stapp02`  
  - `App_server_3` → `stapp03`  
- **Labels**: `stapp01`, `stapp02`, `stapp03`  
- **Remote Root Directory**:  
  - `App_server_1` → `/home/tony/jenkins`  
  - `App_server_2` → `/home/steve/jenkins`  
  - `App_server_3` → `/home/banner/jenkins`  
- Launch via SSH, ensure all nodes online.  
- Test with a simple job.

**💡 Note**: Install Java 21 on each app server. Use SSH Build Agents plugin.

---

## 🔹 Step 1: Access Jenkins UI

**Action**: Open Jenkins UI and log in.  
**Purpose**: Gain access to manage nodes and credentials.

**Steps**:  
1. Click the **Jenkins** button in the top bar.  
2. Enter:  
   - **Username**: `admin`  
   - **Password**: `Adm!n321`  
3. Click **Log in**.

**Success Indicators**:  
- ✅ Login successful.  
- ✅ Dashboard loads.

---

## 🔹 Step 2: Install SSH Build Agents Plugin

**Action**: Install required plugin.  
**Purpose**: Enable SSH-based slave node connectivity.

**Steps**:  
1. Go to **Manage Jenkins** → **Plugins** → **Available plugins**.  
2. Search: `SSH Build Agents`  
3. Select and install.  
4. Check **Restart Jenkins when installation is complete**.  
5. Re-login after restart.

**Success Indicators**:  
- ✅ Plugin installed.  
- ✅ Jenkins restarts.

---

## 🔹 Step 3: Install Java 21 on All App Servers

**Action**: SSH into each app server and install Java.  
**Purpose**: Required for Jenkins agent JAR to run.

**Steps** (repeat for `stapp01`, `stapp02`, `stapp03`):  
```bash
ssh tony@stapp01   # or steve@stapp02, banner@stapp03
```
```bash
sudo yum install java-21-openjdk -y
java -version
```

**Expected Output**:  
```
openjdk version "21" ...
```

**Success Indicators**:  
- ✅ Java 21 installed on all 3 servers.

---

## 🔹 Step 4: Add SSH Credentials for All App Servers

**Action**: Store login credentials securely in Jenkins.  
**Purpose**: Enable secure SSH agent launch.

**Steps**:  
1. Go to **Manage Jenkins** → **Credentials** → **System** → **Global credentials**.  
2. Click **Add Credentials** → **Username with password**.  
3. Repeat for each server:  

| **Node** | **Username** | **Password** | **ID** | **Description** |
|----------|--------------|--------------|--------|-----------------|
| stapp01 | tony | Ir0nM@n | stapp01-cred | App Server 1 |
| stapp02 | steve | Am3ric@ | stapp02-cred | App Server 2 |
| stapp03 | banner | BigGr33n | stapp03-cred | App Server 3 |

4. Click **OK**.

**Success Indicators**:  
- ✅ 3 credentials saved with correct IDs.

---

## 🔹 Step 5: Create Slave Node - App_server_1

**Action**: Add `stapp01` as Jenkins agent.  
**Purpose**: Enable remote task execution.

**Steps**:  
1. Go to **Manage Jenkins** → **Nodes** → **New Node**.  
2. Enter:  
   - **Node name**: `App_server_1`  
   - **Type**: `Permanent Agent`  
3. Click **OK**.  
4. Configure:  
   - **Description**: `App Server 1 - stapp01`  
   - **# of executors**: `1`  
   - **Remote root directory**: `/home/tony/jenkins`  
   - **Labels**: `stapp01`  
   - **Usage**: `Use this node as much as possible`  
   - **Launch method**: `Launch agent via SSH`  
   - **Host**: `stapp01`  
   - **Credentials**: `stapp01-cred`  
   - **Host Key Verification Strategy**: `Non verifying Verification Strategy`  
5. Click **Save**.

**Success Indicators**:  
- ✅ Node appears in list.  
- ✅ Status: **Online**.

---

## 🔹 Step 6: Create App_server_2 and App_server_3

**Action**: Repeat for `stapp02` and `stapp03`.  
**Purpose**: Complete all 3 slave nodes.

**Steps**: Follow Step 5 with these changes:  

| **Node** | **Host** | **Remote Dir** | **Label** | **User** | **Cred ID** |
|----------|----------|----------------|-----------|----------|-------------|
| App_server_2 | stapp02 | /home/steve/jenkins | stapp02 | steve | stapp02-cred |
| App_server_3 | stapp03 | /home/banner/jenkins | stapp03 | banner | stapp03-cred |

**Success Indicators**:  
- ✅ All 3 nodes online.

---

## 🔹 Step 7: Create Test Job `testNode`

**Action**: Create a Freestyle job to validate nodes.  
**Purpose**: Confirm agent connectivity and execution.

**Steps**:  
1. Click **New Item**.  
2. Enter:  
   - **Item name**: `testNode`  
   - **Type**: `Freestyle project`  
3. Click **OK**.  
4. Configure:  
   - Check **Restrict where this project can be run**  
   - **Label Expression**: `stapp01 || stapp02 || stapp03`  
5. Under **Build**, add **Execute shell**:  
```bash
   echo "Hello from Agent"
   pwd
   echo "Running as: $USER"
   java -version
```  
6. Click **Save**.

**Success Indicators**:  
- ✅ Job saved.

---

## 🔹 Step 8: Run testNode on Each Agent

**Action**: Trigger build and verify output.  
**Purpose**: Confirm all nodes work.

**Steps**:  
1. Go to `testNode`.  
2. Click **Build Now**.  
3. Repeat 3 times to hit different nodes.  
4. Check **Console Output**:  

**Expected Output** (example):  
```
Hello from Agent
/home/tony/jenkins
Running as: tony
openjdk version "21" ...
```

**Success Indicators**:  
- ✅ Build succeeds.  
- ✅ Output shows correct user, path, and Java.

---

## 🔹 Step 9: Verify All Nodes Online

**Action**: Final check in Jenkins UI.  
**Purpose**: Confirm stability.

**Steps**:  
1. Go to **Manage Jenkins** → **Nodes**.  
2. Confirm:  
   - All 3 nodes: **Online**  
   - No errors  

**Success Indicators**:  
- ✅ All nodes green and connected.

---

## 🔹 Step 10: Document the Process

**Action**: Capture all configuration steps and execution results.  
**Purpose**: Audit and replication.

**Steps**:  
1. Document all configuration details:  
   - Login credentials  
   - Plugin installation  
   - Java installation on servers  
   - 3 credentials  
   - 3 node configurations  
   - Nodes list (online)  
   - testNode configuration  
   - Build console output  

**Success Indicator**:  
- ✅ Full documentation completed.

---

## 📋 Quick Reference Guide

**Jenkins UI Steps**:  
1. **Login**: `admin` / `Adm!n321`  
2. **Install**: SSH Build Agents → Restart  
3. **On each app server**:  
```bash
   sudo yum install java-21-openjdk -y
```  
4. **Add Credentials**:  
   - `stapp01-cred`: tony / Ir0nM@n  
   - `stapp02-cred`: steve / Am3ric@  
   - `stapp03-cred`: banner / BigGr33n  
5. **Add Nodes**:  

| **Name** | **Host** | **Root Dir** | **Label** | **Cred** |
|----------|----------|--------------|-----------|----------|
| App_server_1 | stapp01 | /home/tony/jenkins | stapp01 | stapp01-cred |
| App_server_2 | stapp02 | /home/steve/jenkins | stapp02 | stapp02-cred |
| App_server_3 | stapp03 | /home/banner/jenkins | stapp03 | stapp03-cred |

6. **Launch**: SSH, Non verifying  
7. **Test job**: `testNode` → Label: `stapp01 || stapp02 || stapp03`  
8. **Build** → Verify output

---

## 💡 Common Node Issues & Fixes

### **Issue 1: Node Offline - Connection Refused**
**Problem**: SSH port 22 blocked  
**Fix**:  
```bash
ssh tony@stapp01 "sudo systemctl status sshd"
```

### **Issue 2: Java Not Found**
**Problem**: `java: command not found`  
**Fix**:  
```bash
sudo yum install java-21-openjdk -y
```

### **Issue 3: Permission Denied on Root Dir**
**Problem**: Cannot create `/home/tony/jenkins`  
**Fix**:  
```bash
mkdir -p /home/tony/jenkins
chown tony:tony /home/tony/jenkins
```

---

## 🔧 Troubleshooting Slave Failures

### **Error: Authentication Failed**
**Symptoms**: Authentication failed  
**Solution**:  
- Verify password in credentials  
- Test manually:  
```bash
ssh tony@stapp01
```

### **Error: Agent JAR Download Fails**
**Symptoms**: Failed to download agent  
**Solution**:  
- Check internet on app server  
- Allow jenkins-url in firewall

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:  
Adding 3 remote app servers as SSH slave nodes with custom root directories, labels, and secure authentication.

**💡 Solution Approach**:  
1. Installed Java on all app servers  
2. Used SSH Build Agents plugin  
3. Stored passwords securely in Jenkins credentials  
4. Configured non-verifying SSH for first-time connect  
5. Set unique remote root dirs and labels  
6. Verified with `testNode` job

**🎯 Key Success Factors**:  
- Correct node names, labels, root dirs  
- All nodes online  
- `java -version` works on agents  
- `testNode` runs on all 3 nodes  
- Output shows correct user and path

**⚠️ Critical Fixes**:  
- Used **Non verifying Verification Strategy**  
- Created root directories manually if needed  
- Installed Java 21 on all servers

**🔒 Best Practices Applied**:  
- Labeled nodes for targeted builds  
- Secure credential storage  
- Remote root isolation  
- Health check via test job  
- Full documentation

**⚠️ Important Troubleshooting Concepts**:  
- Always test SSH manually first  
- Check **Node → Log** for connection errors  
- Use **Build on Agent** to debug  
- Monitor **System Log** for auth issues

---

## ⚠️ Important Production Notes

🔧 **Security**: Use SSH keys instead of passwords in prod  
🔐 **Reliability**: Set Availability to "Keep this agent online"  
📊 **Scalability**: Add more nodes with same pattern  
🛡️ **Monitoring**: Enable Node Monitoring plugin  
📹 **Backup**: Backup JENKINS_HOME regularly

---

## ✅ Task Completion Checklist

- [ ] Logged into Jenkins with `admin`/`Adm!n321`  
- [ ] Installed SSH Build Agents Plugin  
- [ ] Installed Java 21 on all 3 app servers  
- [ ] Added 3 credentials: `stapp01-cred`, `stapp02-cred`, `stapp03-cred`  
- [ ] Created node `App_server_1` with correct configuration  
- [ ] Created node `App_server_2` with correct configuration  
- [ ] Created node `App_server_3` with correct configuration  
- [ ] All 3 nodes show **Online** status  
- [ ] Created test job `testNode` with label expression  
- [ ] Successfully ran builds on all 3 nodes  
- [ ] Verified console output shows correct user, path, and Java version  
- [ ] Documented all steps  

**🎉 Success Criteria Met When**:  
- All 3 nodes are online and connected  
- Test job runs successfully on each node  
- Console output confirms correct user and workspace  
- Java 21 is installed and working on all agents  
- Remote root directories are created and accessible

