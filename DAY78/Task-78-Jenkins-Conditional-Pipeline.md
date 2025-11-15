# 🌟 Task 78 - Conditional Jenkins Pipeline with Branch Parameter

**📌 Task Description**  
The **xFusionCorp Industries** team is enhancing their static website deployment using **Jenkins Pipeline**. Now, they need **conditional deployment** based on a `BRANCH` parameter (`master` or `feature`) from the **Gitea** `web_app` repo on **Storage Server**.

The pipeline must clone and deploy only the selected branch to `/var/www/html`, which is mounted to all **App Servers** via **Load Balancer (LB)**.

**👉 Task Requirements**:  
- Access Jenkins UI and log in with **username**: `admin`, **password**: `Adm!n321`.  
- Access Gitea UI and log in with **username**: `sarah`, **password**: `Sarah_pass123`.  
- Add **Storage Server** as SSH slave node:  
  - **Name**: `Storage Server`  
  - **Label**: `ststor01`  
  - **Remote Root**: `/var/www/html`  
- Create **Pipeline job** (not Multibranch): `nautilus-webapp-job`  
- **String Parameter**: `BRANCH` (default: `master`)  
- Single stage: `Deploy` (case-sensitive)  
- **Conditional logic**:  
  - If `BRANCH = master` → deploy master  
  - If `BRANCH = feature` → deploy feature  
- Clone from Gitea: `http://git.stratos.xfusioncorp.com/sarah/web_app.git`  
- Deploy to: `/var/www/html` (overwrite)  
- **Final URL**: `https://<LBR-URL>` → No subfolder  
- App shows: **"Welcome to xFusionCorp Industries!"**

---

## 🔹 Step 1: Access Jenkins & Gitea UI

**Action**: Log in to both systems.  
**Purpose**: Verify access and repo structure.

**Steps**:  
1. **Jenkins**: `admin` / `Adm!n321`  
2. **Gitea**: `sarah` / `Sarah_pass123`  
3. Confirm: `sarah/web_app` has `master` and `feature` branches  

**Success Indicators**:  
- ✅ Both UIs accessible.  
- ✅ Branches visible.

---

## 🔹 Step 2: Install Required Plugins

**Action**: Install SSH Build Agents and Pipeline plugins.  
**Purpose**: Enable SSH nodes and parameterized pipelines.

**Steps**:  
1. **Manage Jenkins** → **Plugins** → **Available plugins**.  
2. Install:  
   - **SSH Build Agents**  
   - **Pipeline**  
3. **Restart Jenkins**.  
4. Re-login.

**Success Indicators**:  
- ✅ Plugins active.  
- ✅ Pipeline job type available.

---

## 🔹 Step 3: Prepare Storage Server (ststor01)

**Action**: Install Java, fix permissions.  
**Purpose**: Run Jenkins agent and allow file ops.

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
# Fix ownership
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

## 🔹 Step 4: Add SSH Credentials

**Action**: Store natasha credentials.  
**Purpose**: Secure SSH access.

**Steps**:  
1. **Manage Jenkins** → **Credentials** → **System** → **Global**.  
2. **Add Credentials** → **Username with password**.  
3. Enter:  
   - **Username**: `natasha`  
   - **Password**: `Bl@kW`  
   - **ID**: `ststor01`  
   - **Description**: `Storage Server Access`  
4. Click **OK**.

**Success Indicators**:  
- ✅ Credential ID: `ststor01`.

---

## 🔹 Step 5: Add Storage Server as SSH Agent

**Action**: Register `ststor01` as Jenkins slave.  
**Purpose**: Run pipeline on correct host.

**Steps**:  
1. **Manage Jenkins** → **Nodes** → **New Node**.  
2. Enter:  
   - **Node name**: `Storage Server`  
   - **Type**: `Permanent Agent`  
3. Click **OK**.  
4. Configure:  
   - **Remote root directory**: `/var/www/html`  
   - **Labels**: `ststor01`  
   - **Launch method**: `Launch agent via SSH`  
   - **Host**: `ststor01`  
   - **Credentials**: `ststor01`  
   - **Host Key Verification Strategy**: `Non verifying Verification Strategy`  
5. Click **Save**.

**Success Indicators**:  
- ✅ Node online.

---

## 🔹 Step 6: Create Parameterized Pipeline Job

**Action**: Create `nautilus-webapp-job` with `BRANCH` parameter.  
**Purpose**: Enable conditional deployment.

**Steps**:  
1. **New Item** →  
   - **Name**: `nautilus-webapp-job`  
   - **Type**: `Pipeline`  
2. Click **OK**.  
3. Configure:  
   - Check **This project is parameterized**  
   - **Add Parameter** → **String Parameter**  
     - **Name**: `BRANCH`  
     - **Default Value**: `master`  
     - **Description**: `Branch to deploy (master or feature)`  
4. **Pipeline** → **Definition**: `Pipeline script`  
5. Paste script:  
```groovy
pipeline {
    agent { label 'ststor01' }
    stages {
        stage('Deploy') {
            steps {
                script {
                    sh """
                        rm -rf /tmp/web_app
                        git clone -b ${BRANCH} http://git.stratos.xfusioncorp.com/sarah/web_app.git /tmp/web_app
                        ls -la /tmp/web_app
                        echo 'Bl@kW' | sudo -S cp -r /tmp/web_app/* /var/www/html/
                        rm -rf /tmp/web_app
                    """
                }
            }
        }
    }
}
```

6. Click **Save**.

**Success Indicators**:  
- ✅ Job saved.  
- ✅ Parameter visible.

---

## 🔹 Step 7: Test Pipeline with master Branch

**Action**: Build with default master.  
**Purpose**: Verify standard deployment.

**Steps**:  
1. Go to `nautilus-webapp-job`.  
2. Click **Build with Parameters**.  
3. Keep `BRANCH = master` → **Build**.  
4. Check **Console Output**:  
```
   Cloning into '/tmp/web_app'...
   total X
   -rw-r--r-- 1 natasha natasha  ... index.html
```

**Success Indicators**:  
- ✅ Build succeeds.  
- ✅ Files copied to `/var/www/html`.

---

## 🔹 Step 8: Test with feature Branch

**Action**: Build with feature.  
**Purpose**: Confirm conditional logic.

**Steps**:  
1. **Build with Parameters**.  
2. Set `BRANCH = feature` → **Build**.  
3. Verify console shows:  
```
   Switched to a new branch 'feature'
```

**Success Indicators**:  
- ✅ feature branch cloned.

---

## 🔹 Step 9: Verify Final Website

**Action**: Access via App button.  
**Purpose**: Confirm content is live.

**Steps**:  
1. Click **App** button.  
2. URL: `https://<LBR-URL>`  
3. Confirm:  
   - ✅ "Welcome to xFusionCorp Industries!"  
   - ✅ No `/web_app` in path  
   - ✅ Content matches selected branch  

**Success Indicators**:  
- ✅ Site loads at root.  
- ✅ Correct version displayed.

---

## 🔹 Step 10: Document the Process

**Action**: Capture all configuration steps and verification results.  
**Purpose**: Audit and replication.

**Steps**:  
1. Document all configuration details:  
   - Jenkins & Gitea login  
   - Plugin installation  
   - Java & permissions  
   - Credentials  
   - Node configuration  
   - Parameter + pipeline script  
   - master & feature builds  
   - Final website  

**Success Indicator**:  
- ✅ Full documentation completed.

---

## 📋 Quick Reference Guide

**Pipeline Script**:  
```groovy
pipeline {
    agent { label 'ststor01' }
    stages {
        stage('Deploy') {
            steps {
                script {
                    sh """
                        rm -rf /tmp/web_app
                        git clone -b ${BRANCH} http://git.stratos.xfusioncorp.com/sarah/web_app.git /tmp/web_app
                        echo 'Bl@kW' | sudo -S cp -r /tmp/web_app/* /var/www/html/
                        rm -rf /tmp/web_app
                    """
                }
            }
        }
    }
}
```

**Parameter**:  
- **Name**: `BRANCH`  
- **Default**: `master`  
- **Values**: `master`, `feature`

**Final URL**: `https://<LBR-URL>` → Root only

---

## 💡 Common Issues & Fixes

### **Issue 1: git clone Fails**
**Problem**: Repository not found  
**Fix**:  
- Confirm Gitea URL  
- Test manually:  
```bash
git clone -b master http://git.stratos.xfusioncorp.com/sarah/web_app.git
```

### **Issue 2: cp: cannot stat**
**Problem**: No files in `/tmp/web_app`  
**Fix**:  
- Add `ls -la /tmp/web_app` in script  
- Check clone output

### **Issue 3: Old Content Persists**
**Problem**: Previous files not removed  
**Fix**:  
- Use `rm -rf /var/www/html/*` before `cp` if needed

---

## 🔧 Troubleshooting Pipeline Failures

### **Error: sudo: no tty present**
**Symptoms**: sudo fails  
**Solution**:  
- Use `echo 'Bl@kW' | sudo -S ...`

### **Error: Branch Not Found**
**Symptoms**: `fatal: couldn't find remote ref feature`  
**Solution**:  
- Confirm branch exists in Gitea

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:  
Conditional deployment of two Git branches using a Jenkins parameter, with clean overwrite and no subfolder in final URL.

**💡 Solution Approach**:  
1. Used String Parameter `BRANCH`  
2. Cloned to temporary directory  
3. Used `sudo -S cp` to overwrite `/var/www/html`  
4. Cleaned up temp files  
5. Verified via LB root URL

**🎯 Key Success Factors**:  
- `BRANCH` parameter with default `master`  
- `git clone -b ${BRANCH}`  
- `sudo -S cp -r` with password  
- No subfolder in URL  
- Content: "Welcome to xFusionCorp Industries!"

**⚠️ Critical Fixes**:  
- `rm -rf /tmp/web_app` before/after  
- `sudo -S` for non-interactive  
- `http://git.stratos...` URL  
- Mount confirmed working

**🔒 Best Practices Applied**:  
- Parameterized builds  
- Temporary workspace  
- Idempotent deployment  
- Clean state  
- Full traceability

**⚠️ Important Troubleshooting Concepts**:  
- Always test Gitea URL manually  
- Use console logs to debug `sh`  
- Verify mount on app servers  
- Check Apache docroot

---

## ⚠️ Important Production Notes

🔧 **Security**: Use SSH keys + Gitea deploy keys  
🔐 **CI/CD**: Add Gitea webhook trigger  
📊 **Validation**: Add curl health check  
🛡️ **Rollback**: Keep last 3 versions  
📹 **Monitoring**: Alert on build failure

---

## ✅ Task Completion Checklist

- [ ] Logged into Jenkins with `admin`/`Adm!n321`  
- [ ] Logged into Gitea with `sarah`/`Sarah_pass123`  
- [ ] Verified `master` and `feature` branches exist  
- [ ] Installed SSH Build Agents and Pipeline plugins  
- [ ] Installed Java 21 on `ststor01`  
- [ ] Fixed ownership of `/var/www/html` to natasha  
- [ ] Added SSH credentials for `ststor01`  
- [ ] Created Storage Server node with label `ststor01`  
- [ ] Verified Storage Server node is **Online**  
- [ ] Created Pipeline job `nautilus-webapp-job`  
- [ ] Added String Parameter `BRANCH` with default `master`  
- [ ] Added single stage named `Deploy`  
- [ ] Configured pipeline with conditional `git clone -b ${BRANCH}`  
- [ ] Successfully built with `master` branch  
- [ ] Successfully built with `feature` branch  
- [ ] Verified website loads at `https://<LBR-URL>` (no subfolder)  
- [ ] Confirmed content shows "Welcome to xFusionCorp Industries!"  
- [ ] Documented all steps  

**🎉 Success Criteria Met When**:  
- Pipeline job accepts `BRANCH` parameter  
- Stage name is exactly `Deploy`  
- Pipeline runs on `ststor01` agent  
- Git clone uses `${BRANCH}` variable  
- Files deployed to `/var/www/html`  
- Website accessible via Load Balancer at root path  
- Both `master` and `feature` branches deploy successfully  
- Correct content displayed based on selected branch

