# 🌟 Task 77 - Deploy Static Web App via Jenkins Pipeline

**📌 Task Description**  
The **xFusionCorp Industries** development team is building a static website hosted on **Nautilus App Servers**. The code resides in **Gitea** (`sarah/web_app`) and is already cloned on **Storage Server** at `/var/www/html`. This path is mounted to all app servers' Apache document root.

We must create a **Jenkins Pipeline job** to automatically deploy the latest code from Gitea to the Storage Server, making it live instantly via the **Load Balancer (LB)**.

**👉 Task Requirements**:  
- Access Jenkins UI and log in with **username**: `admin`, **password**: `Adm!n321`.  
- Access Gitea UI and log in with **username**: `sarah`, **password**: `Sarah_pass123`.  
- Add **Storage Server** as SSH slave node:  
  - **Name**: `Storage Server`  
  - **Label**: `ststor01`  
  - **Remote Root**: `/var/www/html`  
- Create **Pipeline job** (not Multibranch): `datacenter-webapp-job`  
- Single stage: `Deploy` (case-sensitive)  
- Run on agent: `ststor01`  
- Execute: `git pull origin master` in `/var/www/html/web_app`  
- **Final URL**: `https://<LBR-URL>` → No subfolder (e.g., not `/web_app`)  
- Apache on port 8080 on all app servers  
- LB already configured

---

## 🔹 Step 1: Access Jenkins & Gitea UI

**Action**: Log in to both systems.  
**Purpose**: Verify access and repository.

**Steps**:  
1. **Jenkins**:  
   - Click **Jenkins** → `admin` / `Adm!n321`  
2. **Gitea**:  
   - Click **Gitea** → `sarah` / `Sarah_pass123`  
   - Confirm repo: `sarah/web_app` exists  

**Success Indicators**:  
- ✅ Both UIs accessible.  
- ✅ Repo visible in Gitea.

---

## 🔹 Step 2: Install Required Plugins

**Action**: Install SSH Build Agents and Pipeline plugins.  
**Purpose**: Enable SSH nodes and pipeline jobs.

**Steps**:  
1. Go to **Manage Jenkins** → **Plugins** → **Available plugins**.  
2. Install:  
   - **SSH Build Agents**  
   - **Pipeline**  
3. Check **Restart Jenkins when installation is complete**.  
4. Re-login.

**Success Indicators**:  
- ✅ Plugins installed.  
- ✅ Pipeline job type available.

---

## 🔹 Step 3: Install Java & Fix Permissions on Storage Server

**Action**: Prepare `ststor01` for Jenkins agent.  
**Purpose**: Run agent JAR and allow Git operations.

**Steps**:  
```bash
ssh natasha@ststor01
```
```bash
# Install Java
sudo yum install java-21-openjdk -y
java -version
```
```bash
# Fix ownership of web root
sudo chown -R natasha:natasha /var/www/html
ls -ld /var/www/html
```
```bash
exit
```

**Success Indicators**:  
- ✅ Java 21 installed.  
- ✅ `/var/www/html` owned by natasha.

---

## 🔹 Step 4: Add SSH Credentials for Storage Server

**Action**: Store natasha credentials securely.  
**Purpose**: Enable SSH agent launch.

**Steps**:  
1. Go to **Manage Jenkins** → **Credentials** → **System** → **Global credentials**.  
2. Click **Add Credentials** → **Username with password**.  
3. Enter:  
   - **Username**: `natasha`  
   - **Password**: `Bl@kW`  
   - **ID**: `ststor01`  
   - **Description**: `Storage Server SSH Access`  
4. Click **OK**.

**Success Indicators**:  
- ✅ Credential saved with ID `ststor01`.

---

## 🔹 Step 5: Add Storage Server as SSH Slave Node

**Action**: Register `ststor01` as Jenkins agent.  
**Purpose**: Run pipeline on correct server.

**Steps**:  
1. Go to **Manage Jenkins** → **Nodes** → **New Node**.  
2. Enter:  
   - **Node name**: `Storage Server`  
   - **Type**: `Permanent Agent`  
3. Click **OK**.  
4. Configure:  
   - **Remote root directory**: `/var/www/html`  
   - **Labels**: `ststor01`  
   - **Launch method**: `Launch agent via SSH`  
   - **Host**: `ststor01`  
   - **Credentials**: `ststor01` (natasha/Bl@kW)  
   - **Host Key Verification Strategy**: `Non verifying Verification Strategy`  
5. Click **Save**.

**Success Indicators**:  
- ✅ Node appears.  
- ✅ Status: **Online**.

---

## 🔹 Step 6: Create Pipeline Job `datacenter-webapp-job`

**Action**: Create pipeline to deploy web app.  
**Purpose**: Automate git pull on storage server.

**Steps**:  
1. Click **New Item**.  
2. Enter:  
   - **Item name**: `datacenter-webapp-job`  
   - **Type**: `Pipeline`  
3. Click **OK**.  
4. Configure:  
   - **Definition**: `Pipeline script`  
   - Paste script:  
```groovy
   pipeline {
       agent { label 'ststor01' }
       stages {
           stage('Deploy') {
               steps {
                   sh '''
                       cd /var/www/html/web_app
                       git pull origin master
                   '''
               }
           }
       }
   }
```  
5. Click **Save**.

**Success Indicators**:  
- ✅ Job saved.  
- ✅ Syntax valid.

---

## 🔹 Step 7: Trigger Build & Verify Deployment

**Action**: Run pipeline and check website.  
**Purpose**: Confirm code is live.

**Steps**:  
1. Go to `datacenter-webapp-job`.  
2. Click **Build Now**.  
3. Monitor **Console Output**:  
```
   Already up to date.
   # or
   Updating...
```

**Success Indicators**:  
- ✅ Build succeeds.  
- ✅ No errors.

---

## 🔹 Step 8: Verify Website via Load Balancer

**Action**: Access final URL.  
**Purpose**: Confirm deployment is live and correct.

**Steps**:  
1. Open browser → Go to:  
```
   https://<LBR-URL>
```
   *(Replace `<LBR-URL>` with actual LB URL from lab)*  
2. Confirm:  
   - ✅ Website loads  
   - ✅ No `/web_app` in URL  
   - ✅ Content from `web_app` repo is displayed  

**Success Indicators**:  
- ✅ Site loads at root URL.  
- ✅ Latest changes visible.

---

## 🔹 Step 9: Document the Process

**Action**: Capture all configuration steps and verification results.  
**Purpose**: Audit and replication.

**Steps**:  
1. Document all configuration details:  
   - Jenkins & Gitea login  
   - Plugin installation  
   - Java & permissions on `ststor01`  
   - Credentials  
   - Node configuration (Storage Server)  
   - Pipeline job script  
   - Build console output  
   - Final website  

**Success Indicator**:  
- ✅ Full documentation completed.

---

## 📋 Quick Reference Guide

**Jenkins Pipeline Script**:  
```groovy
pipeline {
    agent { label 'ststor01' }
    stages {
        stage('Deploy') {
            steps {
                sh '''
                    cd /var/www/html/web_app
                    git pull origin master
                '''
            }
        }
    }
}
```

**Key Commands on ststor01**:  
```bash
sudo yum install java-21-openjdk -y
sudo chown -R natasha:natasha /var/www/html
```

**Final URL**: `https://<LBR-URL>` → No subfolder

---

## 💡 Common Issues & Fixes

### **Issue 1: Node Offline**
**Problem**: Storage Server not connecting  
**Fix**:  
```bash
ssh natasha@ststor01 "java -version"
```

### **Issue 2: Permission Denied on Git Pull**
**Problem**: `fatal: could not read from remote`  
**Fix**:  
```bash
sudo chown -R natasha:natasha /var/www/html/web_app
```

### **Issue 3: Website Shows Directory Listing**
**Problem**: Not loading index.html  
**Fix**:  
- Ensure `index.html` exists in `/var/www/html/web_app`  
- Mount is correct on app servers

---

## 🔧 Troubleshooting Pipeline Failures

### **Error: git: command not found**
**Symptoms**: Build fails  
**Solution**:  
```bash
sudo yum install git -y
```

### **Error: Agent Not Found**
**Symptoms**: No such agent  
**Solution**:  
- Confirm label: `ststor01`  
- Node is online

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:  
Deploying a static website from Gitea to a shared storage mount using a Jenkins Pipeline with exact stage name, agent label, and no subfolder in URL.

**💡 Solution Approach**:  
1. Added Storage Server as SSH agent with correct root dir  
2. Fixed ownership and installed Java + Git  
3. Created Pipeline job with single `Deploy` stage  
4. Used `git pull` on mounted path  
5. Verified via LB URL

**🎯 Key Success Factors**:  
- Node label: `ststor01`  
- Remote root: `/var/www/html`  
- Stage name: `Deploy`  
- `git pull` in correct path  
- No subfolder in final URL  
- Mount working across app servers

**⚠️ Critical Fixes**:  
- `chown -R natasha:natasha /var/www/html`  
- Used **Non verifying** SSH  
- Pipeline runs on `ststor01` only  
- Apache on port 8080

**🔒 Best Practices Applied**:  
- Infrastructure as Code (Pipeline)  
- Shared storage for HA  
- Zero-downtime deploy  
- Agent isolation  
- Full traceability

**⚠️ Important Troubleshooting Concepts**:  
- Always verify mount on app servers  
- Check Apache docroot → `/var/www/html`  
- Use console output to debug `sh` steps  
- Confirm Gitea → Storage sync

---

## ⚠️ Important Production Notes

🔧 **Security**: Use SSH keys instead of passwords  
🔐 **Reliability**: Add post-build cleanup  
📊 **CI/CD**: Trigger on Gitea push (webhook)  
🛡️ **Monitoring**: Add health check stage  
📹 **Backup**: Version control in Gitea is backup

---

## ✅ Task Completion Checklist

- [ ] Logged into Jenkins with `admin`/`Adm!n321`  
- [ ] Logged into Gitea with `sarah`/`Sarah_pass123`  
- [ ] Verified `sarah/web_app` repository exists  
- [ ] Installed SSH Build Agents and Pipeline plugins  
- [ ] Installed Java 21 on `ststor01`  
- [ ] Fixed ownership of `/var/www/html` to natasha  
- [ ] Added SSH credentials for `ststor01`  
- [ ] Created Storage Server node with label `ststor01`  
- [ ] Verified Storage Server node is **Online**  
- [ ] Created Pipeline job `datacenter-webapp-job`  
- [ ] Added single stage named `Deploy`  
- [ ] Configured agent to run on `ststor01`  
- [ ] Added `git pull origin master` command  
- [ ] Triggered build successfully  
- [ ] Verified website loads at `https://<LBR-URL>` (no subfolder)  
- [ ] Documented all steps  

**🎉 Success Criteria Met When**:  
- Storage Server node is online with label `ststor01`  
- Pipeline job has exactly one stage named `Deploy`  
- Pipeline runs on `ststor01` agent  
- `git pull` executes in `/var/www/html/web_app`  
- Website is accessible via Load Balancer URL at root path  
- No errors in console output  
- Latest code from Gitea is deployed

