# 🌟 Task 80 - Chained Jenkins Jobs: Deploy + Restart Apache

**📌 Task Description**  
The **Nautilus DevOps team** wants **zero-downtime automation**:  
1. Deploy latest code from **Gitea** to **Storage Server**  
2. Only if deployment succeeds → Restart Apache on all 3 **App Servers**

This is achieved using **Jenkins chained builds** (upstream/downstream) with conditional trigger.

**👉 Task Requirements**:  
- Access Jenkins UI and log in with **username**: `admin`, **password**: `Adm!n321`.  
- Access Gitea UI and log in with **username**: `sarah`, **password**: `Sarah_pass123`.  
- **Repo**: `sarah/web`  
- **Storage Server**: `/var/www/html` → shared volume with all App Servers  
- **App Servers**: `stapp01`, `stapp02`, `stapp03`  
- **Apache**: Already running on port **8080**  
- **Job 1**: `nautilus-app-deployment`  
  - Pull from Gitea → master branch  
  - Deploy to `/var/www/html` on Storage Server  
- **Job 2**: `manage-services`  
  - Downstream of `nautilus-app-deployment`  
  - Trigger only if upstream is stable  
  - Restart `httpd` on all 3 app servers  
- **Final URL**: `https://<LBR-URL>` → No subfolder  
- **Content**: `Welcome to KodeKloud!`

---

## 🔹 PART 1: Install & Configure Plugins

**Action**: Install required plugins.  
**Purpose**: Enable Git, SCP, and SSH execution.

**Steps**:  
1. **Manage Jenkins** → **Plugins** → **Available**.  
2. Install:  
   - **Git**  
   - **Publish Over SSH**  
   - **SSH Credentials**  
3. **Restart Jenkins**.

**Success Indicators**:  
- ✅ Plugins active.

---

## 🔹 PART 2: Configure SSH Servers (App Servers)

**Action**: Add `stapp01`, `stapp02`, `stapp03` as SSH hosts.  
**Purpose**: Allow Jenkins to run `systemctl restart httpd`.

**Steps**:  
1. **Manage Jenkins** → **System** → **Publish Over SSH**.  
2. Add **3 SSH Servers**:  

| **Name** | **Hostname** | **Port** | **Username** | **Password** | **Description** |
|----------|--------------|----------|--------------|--------------|-----------------|
| stapp01 | stapp01.stratos.xfusioncorp.com | 22 | tony | Ir0nM@n | Nautilus App 1 |
| stapp02 | stapp02.stratos.xfusioncorp.com | 22 | steve | Am3ric@ | Nautilus App 2 |
| stapp03 | stapp03.stratos.xfusioncorp.com | 22 | banner | BigGr33n | Nautilus App 3 |

3. **Test Configuration** → **Success**

**Success Indicators**:  
- ✅ All 3 show **Success**.

---

## 🔹 PART 3: Create Upstream Job - nautilus-app-deployment

**Action**: Deploy code from Gitea to Storage Server.  
**Purpose**: Pull and sync code to shared volume.

### **Step 3.1: Create Job**

**Steps**:  
1. **New Item** →  
   - **Name**: `nautilus-app-deployment`  
   - **Type**: `Freestyle project`  
2. Click **OK**.

---

### **Step 3.2: Configure Git SCM**

**Steps**:  
1. **Source Code Management** → **Git**  
2. Enter:  
   - **Repository URL**: `http://git.stratos.xfusioncorp.com/sarah/web.git`  
   - **Credentials**: `None`  
   - **Branch Specifier**: `*/master`  

**Success Indicators**:  
- ✅ URL correct.  
- ✅ No auth needed.

---

### **Step 3.3: Add Build Step - SCP to Storage Server**

**Steps**:  
1. **Build** → **Add build step** → **Execute shell**.  
2. Paste:  
```bash
   echo "Deploying to Storage Server..."
   sshpass -p "Bl@kW" scp -r -o StrictHostKeyChecking=no ./* natasha@ststor01:/var/www/html/
   echo "Deployment complete."
```

**Note**: `sshpass` must be installed on Jenkins server or use SSH key.

**Success Indicators**:  
- ✅ Command saved.

---

### **Step 3.4: Add Post-Build Action - Trigger Downstream**

**Steps**:  
1. **Post-build Actions** → **Build other projects**.  
2. Enter:  
   - **Projects to build**: `manage-services`  
   - Check: **Trigger only if build is stable**  

**Success Indicators**:  
- ✅ Downstream linked.  
- ✅ Condition set.

---

## 🔹 PART 4: Create Downstream Job - manage-services

**Action**: Restart Apache on all app servers.  
**Purpose**: Apply new content without manual intervention.

### **Step 4.1: Create Job**

**Steps**:  
1. **New Item** →  
   - **Name**: `manage-services`  
   - **Type**: `Freestyle project`  
2. Click **OK**.

---

### **Step 4.2: Add 3 SSH Build Steps**

**Steps**:  
1. **Build** → **Add build step** → **Send files or execute commands over SSH**

**For stapp01**:  
- **SSH Server**: `stapp01`  
- **Exec command**:  
```bash
  echo "Ir0nM@n" | sudo -S systemctl restart httpd
```

**For stapp02**:  
- **SSH Server**: `stapp02`  
- **Exec command**:  
```bash
  echo "Am3ric@" | sudo -S systemctl restart httpd
```

**For stapp03**:  
- **SSH Server**: `stapp03`  
- **Exec command**:  
```bash
  echo "BigGr33n" | sudo -S systemctl restart httpd
```

**Success Indicators**:  
- ✅ All 3 steps added.

---

## 🔹 PART 5: Test Full Chain

### **Step 5.1: Push Change to Gitea**

**On Storage Server (as sarah)**:  
```bash
cd ~/web
echo "Welcome to KodeKloud!" > index.html
git add .
git commit -m "Update welcome message"
git push origin master
# Login: sarah / Sarah_pass123
```

---

### **Step 5.2: Jenkins Auto-Triggers**

1. **nautilus-app-deployment** runs  
   - Clones latest code  
   - SCPs to `/var/www/html` on `ststor01`  

2. If stable → Triggers **manage-services**  
   - Restarts `httpd` on all 3 app servers  

**Console Output (nautilus-app-deployment)**:  
```
Deploying to Storage Server...
index.html
Deployment complete.
Finished: SUCCESS
```

**Console Output (manage-services)**:  
```
Restarting httpd on stapp01... OK
Restarting httpd on stapp02... OK
Restarting httpd on stapp03... OK
Finished: SUCCESS
```

---

### **Step 5.3: Verify Final Website**

**Action**: Click **App** button  
**URL**: `https://<LBR-URL>`  
**Content**:  
```
Welcome to KodeKloud!
```

**Success Indicators**:  
- ✅ No subfolder.  
- ✅ Content updated.  
- ✅ LB serves instantly.

---

## 📋 Quick Reference Guide

**Upstream Job**: `nautilus-app-deployment`  
```bash
sshpass -p "Bl@kW" scp -r -o StrictHostKeyChecking=no ./* natasha@ststor01:/var/www/html/
```
→ **Post-build**: Trigger `manage-services` only if stable

**Downstream Job**: `manage-services`  
```bash
echo "PASS" | sudo -S systemctl restart httpd
```
→ One per app server

**Gitea Repo**: `http://git.stratos.xfusioncorp.com/sarah/web.git`

---

## 💡 Common Issues & Fixes

| **Issue** | **Fix** |
|-----------|---------|
| sshpass: command not found | `yum install sshpass -y` on Jenkins |
| Permission denied on SCP | `chown natasha:natasha /var/www/html` |
| Downstream not triggering | Ensure "Trigger only if stable" is checked |
| Old content | Verify shared volume is mounted |

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge**:  
Conditional service restart only after successful deployment using chained Jenkins jobs.

**💡 Solution**:  
1. Upstream: `nautilus-app-deployment` → Git + SCP  
2. Downstream: `manage-services` → SSH restart  
3. Trigger condition: Only if stable  
4. Shared volume ensures instant sync

**🎯 Key Success Factors**:  
- Publish Over SSH for app servers  
- `sshpass` for Storage Server  
- Post-build action with stable trigger  
- No subfolder in URL  
- Content: `Welcome to KodeKloud!`

---

## ⚠️ Important Production Notes

🔧 **Security**: Use SSH keys + Gitea webhooks  
🔐 **Reliability**: Add health check before restart  
📊 **Idempotency**: Use `systemctl try-restart httpd`  
🛡️ **Monitoring**: Alert on failed downstream  
📹 **Rollback**: Keep last deploy in `/var/www/html.old`

---

## ✅ Task Completion Checklist

- [ ] Logged into Jenkins with `admin`/`Adm!n321`  
- [ ] Logged into Gitea with `sarah`/`Sarah_pass123`  
- [ ] Installed Git, Publish Over SSH, and SSH Credentials plugins  
- [ ] Configured SSH servers for all 3 app servers  
- [ ] Tested SSH connections successfully  
- [ ] Created upstream job `nautilus-app-deployment`  
- [ ] Configured Git SCM with correct repository URL  
- [ ] Added SCP build step to deploy to Storage Server  
- [ ] Added post-build action to trigger `manage-services` if stable  
- [ ] Created downstream job `manage-services`  
- [ ] Added 3 SSH build steps to restart httpd on all app servers  
- [ ] Pushed changes to Gitea  
- [ ] Verified upstream job runs successfully  
- [ ] Verified downstream job triggers automatically  
- [ ] Confirmed Apache restarted on all app servers  
- [ ] Verified website loads at `https://<LBR-URL>`  
- [ ] Confirmed content shows "Welcome to KodeKloud!"  
- [ ] Documented all steps  

**🎉 Success Criteria Met When**:  
- Upstream job `nautilus-app-deployment` pulls from Gitea  
- Code deployed to `/var/www/html` on Storage Server  
- Downstream job `manage-services` triggers only if upstream is stable  
- Apache restarts on all 3 app servers automatically  
- Website accessible via Load Balancer at root path  
- Content reflects latest Git commit  
- Zero manual intervention required

