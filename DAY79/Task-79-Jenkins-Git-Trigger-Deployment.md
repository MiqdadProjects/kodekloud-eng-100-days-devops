# 🌟 Task 79 - Auto-Deploy Web App on Git Push via Jenkins

**📌 Task Description**  
The **Nautilus DevOps team** wants automatic deployment of the web app whenever developer **Sarah** pushes to the **Gitea master branch**. Jenkins must detect the push, pull the latest code, and deploy it to **Storage Server** at `/var/www/html`, which is shared across all **App Servers**.

**👉 Task Requirements**:  
- Access Jenkins UI and log in with **username**: `admin`, **password**: `Adm!n321`.  
- Access Gitea UI and log in with **username**: `sarah`, **password**: `Sarah_pass123`.  
- **Repo**: `sarah/web` → cloned under `/home/sarah/web` on Storage Server  
- **App Servers**: `stapp01`, `stapp02`, `stapp03`  
- **Apache**: Install `httpd`, serve on port **8080**  
- **Jenkins Job**: `nautilus-app-deployment`  
  - Freestyle project  
  - Git SCM: `http://git.stratos.xfusioncorp.com/sarah/web.git`  
  - Branch: `*/master`  
  - Trigger: Poll SCM → `* * * * *`  
  - Deploy: Copy all files to `/var/www/html` on Storage Server  
- **Ownership**: `/var/www/html` → `sarah:sarah`  
- **Final URL**: `https://<LBR-URL>` → No subfolder  
- **Content**: `Welcome to the xFusionCorp Industries`

---

## 🔹 PART 1: Install Apache on All App Servers

**Action**: Use Jenkins to install and configure httpd on port 8080.  
**Purpose**: Serve content from shared storage.

### **Step 1.1: Install Required Plugins**

**Steps**:  
1. **Manage Jenkins** → **Plugins** → **Available**.  
2. Install:  
   - **Publish Over SSH**  
   - **SSH Credentials**  
3. **Restart Jenkins**.

**Success Indicators**:  
- ✅ Plugins active.

---

### **Step 1.2: Add SSH Credentials for App Servers**

**Steps**:  
1. **Manage Jenkins** → **Credentials** → **System** → **Global**.  
2. Add **3 credentials**:  

| **Host** | **Username** | **Password** | **ID** |
|----------|--------------|--------------|--------|
| stapp01 | tony | Ir0nM@n | stapp01 |
| stapp02 | steve | Am3ric@ | stapp02 |
| stapp03 | banner | BigGr33n | stapp03 |

**Success Indicators**:  
- ✅ All 3 saved.

---

### **Step 1.3: Configure SSH Servers**

**Steps**:  
1. **Manage Jenkins** → **System** → **Publish Over SSH**.  
2. Add **3 servers**:  

| **Name** | **Hostname** | **Port** | **Username** | **Credentials** |
|----------|--------------|----------|--------------|-----------------|
| stapp01 | stapp01.stratos.xfusioncorp.com | 22 | tony | stapp01 |
| stapp02 | stapp02.stratos.xfusioncorp.com | 22 | steve | stapp02 |
| stapp03 | stapp03.stratos.xfusioncorp.com | 22 | banner | stapp03 |

3. **Test Connection** → Success

**Success Indicators**:  
- ✅ All 3 connections **Success**.

---

### **Step 1.4: Create apache-install-job**

**Steps**:  
1. **New Item** → `apache-install-job` → **Freestyle project**.  
2. **Build** → **Add build step** → **Send files or execute commands over SSH**.  
3. Add **3 steps** (one per server):  

**For stapp01**:  
```bash
echo "Installing Apache on stapp01..."
echo 'Ir0nM@n' | sudo -S yum install -y httpd
echo 'Ir0nM@n' | sudo -S sed -i 's/Listen 80/Listen 8080/' /etc/httpd/conf/httpd.conf
echo 'Ir0nM@n' | sudo -S systemctl enable httpd
echo 'Ir0nM@n' | sudo -S systemctl restart httpd
```

**For stapp02**:  
```bash
echo "Installing Apache on stapp02..."
echo 'Am3ric@' | sudo -S yum install -y httpd
echo 'Am3ric@' | sudo -S sed -i 's/Listen 80/Listen 8080/' /etc/httpd/conf/httpd.conf
echo 'Am3ric@' | sudo -S systemctl enable httpd
echo 'Am3ric@' | sudo -S systemctl restart httpd
```

**For stapp03**:  
```bash
echo "Installing Apache on stapp03..."
echo 'BigGr33n' | sudo -S yum install -y httpd
echo 'BigGr33n' | sudo -S sed -i 's/Listen 80/Listen 8080/' /etc/httpd/conf/httpd.conf
echo 'BigGr33n' | sudo -S systemctl enable httpd
echo 'BigGr33n' | sudo -S systemctl restart httpd
```

4. **Save** → **Build Now**.

**Success Indicators**:  
- ✅ All 3 commands succeed.  
- ✅ Apache running on port 8080.

---

## 🔹 PART 2: Configure Auto-Deployment Job

### **Step 2.1: Install Git & SSH Build Agents**

**Steps**:  
1. **Manage Plugins** → Install:  
   - **Git**  
   - **SSH Build Agents**  
2. **Restart Jenkins**.

---

### **Step 2.2: Set Up Passwordless SSH (Jenkins → Storage Server)**

**On Jenkins Server**:  
```bash
ssh jenkins@jenkins
sudo su - jenkins
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa -N ""
cat ~/.ssh/id_rsa.pub
```

**On Storage Server**:  
```bash
ssh natasha@ststor01
sudo su - sarah
mkdir -p ~/.ssh
chmod 700 ~/.ssh
vi ~/.ssh/authorized_keys
# Paste Jenkins public key
chmod 600 ~/.ssh/authorized_keys
```

**Test**:  
```bash
ssh sarah@ststor01 -i /var/lib/jenkins/.ssh/id_rsa
```

**Success Indicators**:  
- ✅ No password prompt.

---

### **Step 2.3: Fix /var/www/html Ownership**

**On Storage Server**:  
```bash
ssh natasha@ststor01
sudo chown -R sarah:sarah /var/www/html
ls -ld /var/www/html
```

**Expected**:  
```
drwxr-xr-x 3 sarah sarah ... /var/www/html
```

---

### **Step 2.4: Create nautilus-app-deployment Job**

**Steps**:  
1. **New Item** → `nautilus-app-deployment` → **Freestyle project**.  
2. **Source Code Management** → **Git**:  
   - **Repository URL**: `http://git.stratos.xfusioncorp.com/sarah/web.git`  
   - **Credentials**: `None`  
   - **Branches**: `*/master`  
3. **Build Triggers** → **Poll SCM**:  
   - **Schedule**: `* * * * *`  
4. **Build** → **Add build step** → **Execute shell**:  
```bash
   echo "Deploying to Storage Server..."
   scp -o StrictHostKeyChecking=no \
       -i /var/lib/jenkins/.ssh/id_rsa \
       * sarah@ststor01:/var/www/html/
   echo "Deployment complete."
```  
5. **Save**.

**Success Indicators**:  
- ✅ Job configured.

---

## 🔹 PART 3: Push Change & Verify Auto-Deployment

### **Step 3.1: Update index.html and Push**

**On Storage Server**:  
```bash
ssh sarah@ststor01
cd ~/web
cat > index.html
Welcome to the xFusionCorp Industries
# Press Ctrl+D
git add .
git commit -m "Updated index.html"
git push origin master
# Enter: sarah / Sarah_pass123
```

**Success Indicators**:  
- ✅ Push successful.

---

### **Step 3.2: Jenkins Auto-Triggers**

Wait **1–2 minutes** → Jenkins polls Gitea → detects change → builds.

**Console Output**:  
```
Started by an SCM change
...
scp ... index.html sarah@ststor01:/var/www/html/
Deployment complete.
Finished: SUCCESS
```

---

### **Step 3.3: Verify Final Website**

**Action**: Click **App** button  
**URL**: `https://<LBR-URL>`  
**Content**:  
```
Welcome to the xFusionCorp Industries
```

**Success Indicators**:  
- ✅ No subfolder.  
- ✅ All files deployed.  
- ✅ LB serves correct content.

---

## 📋 Quick Reference Guide

**Apache Install Script (per server)**:  
```bash
echo 'PASS' | sudo -S yum install -y httpd
echo 'PASS' | sudo -S sed -i 's/Listen 80/Listen 8080/' /etc/httpd/conf/httpd.conf
echo 'PASS' | sudo -S systemctl enable --now httpd
```

**Jenkins Deployment Command**:  
```bash
scp -o StrictHostKeyChecking=no -i /var/lib/jenkins/.ssh/id_rsa * sarah@ststor01:/var/www/html/
```

**Poll SCM Schedule**: `* * * * *` (every minute)

---

## 💡 Common Issues & Fixes

| **Issue** | **Fix** |
|-----------|---------|
| httpd not starting | Check Listen 8080 in config |
| SCP fails | Verify id_rsa path and permissions |
| Old content | Ensure scp overwrites all files |
| Poll SCM not triggering | Check Gitea URL and branch |

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge**:  
Auto-deploy on Git push with shared storage, passwordless SCP, and correct ownership.

**💡 Solution**:  
1. Apache on 8080 via Publish Over SSH  
2. Passwordless SSH using RSA key  
3. Poll SCM every minute  
4. SCP all files to `/var/www/html`  
5. Ownership: `sarah:sarah`

**🎯 Key Success Factors**:  
- `http://git.stratos.../sarah/web.git`  
- `* * * * *` polling  
- `scp -i /var/lib/jenkins/.ssh/id_rsa`  
- `chown -R sarah:sarah /var/www/html`  
- No subfolder in URL  
- Full repo deployed

---

## ⚠️ Important Production Notes

🔧 **Security**: Use Gitea webhooks instead of polling  
🔐 **Reliability**: Add post-deploy health check  
📊 **Scalability**: Use shared NFS for HA  
🛡️ **Monitoring**: Alert on failed builds  
📹 **Backup**: Git is source of truth

---

## ✅ Task Completion Checklist

- [ ] Logged into Jenkins with `admin`/`Adm!n321`  
- [ ] Logged into Gitea with `sarah`/`Sarah_pass123`  
- [ ] Installed Publish Over SSH and SSH Credentials plugins  
- [ ] Added credentials for all 3 app servers  
- [ ] Configured SSH servers in Publish Over SSH  
- [ ] Created and ran `apache-install-job`  
- [ ] Verified Apache running on port 8080 on all app servers  
- [ ] Installed Git and SSH Build Agents plugins  
- [ ] Set up passwordless SSH from Jenkins to Storage Server  
- [ ] Fixed ownership of `/var/www/html` to `sarah:sarah`  
- [ ] Created `nautilus-app-deployment` job  
- [ ] Configured Git SCM with correct repository URL  
- [ ] Set Poll SCM to `* * * * *`  
- [ ] Added SCP deployment command  
- [ ] Pushed changes to Gitea  
- [ ] Verified Jenkins auto-triggered build  
- [ ] Confirmed website loads at `https://<LBR-URL>`  
- [ ] Verified content shows "Welcome to the xFusionCorp Industries"  
- [ ] Documented all steps  

**🎉 Success Criteria Met When**:  
- Apache installed and running on port 8080 on all app servers  
- Jenkins job `nautilus-app-deployment` configured correctly  
- Poll SCM triggers build every minute  
- Git changes automatically deployed to `/var/www/html`  
- Website accessible via Load Balancer at root path  
- Content matches latest Git commit  
- No manual intervention required for deployment

