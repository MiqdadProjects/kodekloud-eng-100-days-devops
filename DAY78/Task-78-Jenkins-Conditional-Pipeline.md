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
- Clone from Gitea: `http://git.stratos.xfusioncorp.com/sarah/web_app.git` (or get URL from Gitea UI)  
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

**Action**: Install Java 21 and set it as default, fix permissions.  
**Purpose**: Run Jenkins agent with Java 21 and allow file operations.

**Steps**:  
```bash
ssh natasha@ststor01
```
```bash
# Install Java 21
sudo yum install java-21-openjdk -y
java -version
```

**Note**: The above will still show Java 11. Now set Java 21 as default:

```bash
# Set Java 21 as default using alternatives
sudo alternatives --config java
```

**Expected output**:
```
There are 2 programs which provide 'java'.

  Selection    Command
-----------------------------------------------
*+ 1           java-11-openjdk.x86_64 (/usr/lib/jvm/java-11-openjdk-11.0.20.1.1-2.el9.x86_64/bin/java)
   2           java-21-openjdk.x86_64 (/usr/lib/jvm/java-21-openjdk-21.0.11.0.10-2.el9.x86_64/bin/java)

Enter to keep the current selection[+], or type selection number: 2
```

Select option `2` to set Java 21 as default. Or use non-interactive command:

```bash
# Set Java 21 as default (non-interactive)
sudo alternatives --set java /usr/lib/jvm/java-21-openjdk-*/bin/java
```

Verify Java 21 is now active:
```bash
java -version
```

**Expected Output**:
```
openjdk version "21.0.11.0.10" 2024-08-XX LTS
OpenJDK Runtime Environment (Red_Hat-21.0.11.0.10-X)
OpenJDK 64-Bit Server VM (Red_Hat-21.0.11.0.10-X, mixed mode, sharing)
```

Now fix ownership:
```bash
# Fix ownership
sudo chown -R natasha:natasha /var/www/html
ls -ld /var/www/html
```
```bash
exit
```

**Success Indicators**:  
- ✅ Java 21 installed and set as default.  
- ✅ `java -version` shows Java 21.
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

## 🔹 Step 6: Get Gitea Repository URL

**Action**: Copy the correct HTTPS clone URL from Gitea.  
**Purpose**: Gitea DNS may change; use the URL directly from the UI.

**Steps**:  
1. Log into **Gitea** with `sarah` / `Sarah_pass123`  
2. Navigate to **sarah/web_app** repository  
3. Click **Clone** button (usually top-right or near repo info)  
4. Copy the **HTTPS** URL (should look like: `https://git.stratos.xfusioncorp.com/sarah/web_app.git`)  
5. **Note**: If the HTTP URL is used, it may be: `http://git.stratos.xfusioncorp.com/sarah/web_app.git`

**⚠️ Important**: The URL format may have changed from the original task. Use the URL directly from Gitea UI to ensure it works.

**Success Indicators**:  
- ✅ URL copied from Gitea  
- ✅ URL is accessible and contains the correct repository path

---

## 🔹 Step 7: Create Parameterized Pipeline Job

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
5. Paste script (replace `<GITEA_URL>` with the URL copied from Gitea in Step 6):  
```groovy
pipeline {
    agent { label 'ststor01' }
    stages {
        stage('Deploy') {
            steps {
                script {
                    sh """
                        rm -rf /tmp/web_app
                        git clone -b ${BRANCH} <GITEA_URL> /tmp/web_app
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

**Example** (if using the default HTTP URL):
```groovy
git clone -b ${BRANCH} http://git.stratos.xfusioncorp.com/sarah/web_app.git /tmp/web_app
```

6. Click **Save**.

**Success Indicators**:  
- ✅ Job saved.  
- ✅ Parameter visible.

---

## 🔹 Step 8: Test Pipeline with master Branch

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

## 🔹 Step 9: Test with feature Branch

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

## 🔹 Step 10: Verify Final Website

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

## 🔹 Step 11: Document the Process

**Action**: Capture all configuration steps and verification results.  
**Purpose**: Audit and replication.

**Steps**:  
1. Document all configuration details:  
   - Jenkins & Gitea login  
   - Plugin installation  
   - Java 21 setup with alternatives
   - Gitea URL used for clone  
   - Permissions setup  
   - Credentials  
   - Node configuration  
   - Parameter + pipeline script  
   - master & feature builds  
   - Final website  

**Success Indicator**:  
- ✅ Full documentation completed.

---

## 📋 Quick Reference Guide

**Pipeline Script Template**:  
```groovy
pipeline {
    agent { label 'ststor01' }
    stages {
        stage('Deploy') {
            steps {
                script {
                    sh """
                        rm -rf /tmp/web_app
                        git clone -b ${BRANCH} <GITEA_URL> /tmp/web_app
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

### **Issue 1: git clone Fails - Unknown URL Format**
**Problem**: Repository not found / Connection timeout  
**Fix**:  
- Copy the exact HTTPS URL from Gitea UI  
- Do NOT assume the URL format  
- Test manually on ststor01:  
```bash
ssh natasha@ststor01
git clone -b master https://git.stratos.xfusioncorp.com/sarah/web_app.git
```

### **Issue 2: Java Version Mismatch on ststor01**
**Problem**: Node setup fails, Jenkins cannot launch agent  
**Fix**:  
- Verify Java 21 is set as default using:  
```bash
java -version
```
- Should show Java 21, not Java 11  
- Use `sudo alternatives --config java` to manually select Java 21

### **Issue 3: cp: cannot stat**
**Problem**: No files in `/tmp/web_app`  
**Fix**:  
- Add `ls -la /tmp/web_app` in script  
- Check clone output  
- Verify git clone actually succeeded

### **Issue 4: Old Content Persists**
**Problem**: Previous files not removed  
**Fix**:  
- Use `rm -rf /var/www/html/*` before `cp` if needed  
- Or use `cp -r --remove-destination`

### **Issue 5: Gitea URL Changed**
**Problem**: DNS entry no longer works  
**Fix**:  
- Always get fresh URL from Gitea UI  
- Log into Gitea and copy from repository clone button  
- Update Jenkins pipeline script with new URL

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
- Use correct branch name (case-sensitive)

### **Error: Node Offline**
**Symptoms**: `ststor01` agent status shows `offline`  
**Solution**:  
- Verify Java 21 is default (not Java 11)  
- Check SSH connectivity: `ssh natasha@ststor01`  
- Review Jenkins agent logs

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenges Encountered**:  
1. Gitea URL is no longer a fixed DNS entry - requires manual lookup from UI
2. Java 21 installation doesn't set it as default - Java 11 remains active, causing Jenkins agent failures
3. Conditional deployment with parameter-based branch selection

**💡 Solution Approach**:  
1. **Get Dynamic Gitea URL** - Always copy from Gitea UI, not hardcoded
2. **Set Java 21 as Default** - Use `alternatives` command after installation
3. **Use String Parameter** `BRANCH` with conditional git clone
4. **Cloned to temporary directory**  
5. **Used `sudo -S cp` to overwrite `/var/www/html`**  
6. **Cleaned up temp files**  
7. **Verified via LB root URL**

**🎯 Key Success Factors**:  
- ✅ Java 21 set as default on storage server (critical!)
- ✅ Dynamic Gitea URL copied from UI (not hardcoded)
- ✅ `BRANCH` parameter with default `master`  
- ✅ `git clone -b ${BRANCH}` with correct URL
- ✅ `sudo -S cp -r` with password  
- ✅ No subfolder in URL  
- ✅ Content: "Welcome to xFusionCorp Industries!"

**⚠️ Critical Fixes Applied**:  
- ✅ **NEW**: `sudo alternatives --set java` to set Java 21 as default
- ✅ **NEW**: Instructions to get Gitea URL from UI instead of assuming fixed DNS
- ✅ `rm -rf /tmp/web_app` before/after  
- ✅ `sudo -S` for non-interactive sudo  
- ✅ Dynamic Gitea URL (not hardcoded)
- ✅ Mount confirmed working

**🔒 Best Practices Applied**:  
- Parameterized builds  
- Temporary workspace  
- Idempotent deployment  
- Clean state  
- Full traceability  
- Java version management

**⚠️ Important Troubleshooting Concepts**:  
- **Java Defaults**: Always verify default Java version after installation
- **Dynamic URLs**: Never hardcode Gitea URLs - get from UI
- **Test Gitea URL manually**: Ensure clone works before Jenkins build
- **Use console logs** to debug `sh` commands  
- **Verify mount** on app servers  
- **Check Apache docroot**

---

## ⚠️ Important Production Notes

🔧 **Security**: Use SSH keys + Gitea deploy keys  
🔐 **CI/CD**: Add Gitea webhook trigger  
📊 **Validation**: Add curl health check  
🛡️ **Rollback**: Keep last 3 versions  
📹 **Monitoring**: Alert on build failure  
☕ **Java Management**: Pin specific Java versions and use `alternatives` to manage defaults

---

## ✅ Task Completion Checklist

- [ ] Logged into Jenkins with `admin`/`Adm!n321`  
- [ ] Logged into Gitea with `sarah`/`Sarah_pass123`  
- [ ] Verified `master` and `feature` branches exist  
- [ ] Installed SSH Build Agents and Pipeline plugins  
- [ ] Installed Java 21 on `ststor01`  
- [ ] **NEW**: Set Java 21 as default using `alternatives`  
- [ ] Verified `java -version` shows Java 21 (not 11)
- [ ] Fixed ownership of `/var/www/html` to natasha  
- [ ] Added SSH credentials for `ststor01`  
- [ ] Created Storage Server node with label `ststor01`  
- [ ] Verified Storage Server node is **Online**  
- [ ] Copied Gitea URL from Gitea UI (repository clone button)
- [ ] Created Pipeline job `nautilus-webapp-job`  
- [ ] Added String Parameter `BRANCH` with default `master`  
- [ ] Added single stage named `Deploy`  
- [ ] Configured pipeline with conditional `git clone -b ${BRANCH}` using correct Gitea URL
- [ ] Successfully built with `master` branch  
- [ ] Successfully built with `feature` branch  
- [ ] Verified website loads at `https://<LBR-URL>` (no subfolder)  
- [ ] Confirmed content shows "Welcome to xFusionCorp Industries!"  
- [ ] Documented all steps including Gitea URL used

**🎉 Success Criteria Met When**:  
- Pipeline job accepts `BRANCH` parameter  
- Stage name is exactly `Deploy`  
- Pipeline runs on `ststor01` agent  
- Java 21 is the default Java version on storage server
- Git clone uses correct Gitea URL and `${BRANCH}` variable  
- Files deployed to `/var/www/html`  
- Website accessible via Load Balancer at root path  
- Both `master` and `feature` branches deploy successfully  
- Correct content displayed based on selected branch
