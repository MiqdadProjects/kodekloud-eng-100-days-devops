# 🌟 Task 81 - Jenkins Pipeline: Deploy + Test Static Website

**📌 Task Description**  
The **xFusionCorp Industries** team wants a robust **Jenkins Pipeline** to deploy the web app from **Gitea** to **Storage Server** and verify it works via **LB URL** (`http://stlb01:8091`). The pipeline must **fail fast** if deployment or test fails.

**👉 Task Requirements**:  
- Access Jenkins UI and log in with **username**: `admin`, **password**: `Adm!n321`.  
- Access Gitea UI and log in with **username**: `sarah`, **password**: `Sarah_pass123`.  
- **Repo**: `sarah/web` → cloned at `/var/www/html` on Storage Server  
- Update `index.html` → `Welcome to xFusionCorp Industries`  
- **Apache**: Running on port **8080**  
- **Pipeline Job**: `deploy-job` (not Multibranch)  
- **Two stages** (case-sensitive):  
  - **Deploy** → git pull on Storage Server  
  - **Test** → curl LB URL + content check  
- **Final URL**: `http://stlb01:8091` → No subfolder  
- **Content**: `Welcome to xFusionCorp Industries`

---

## 🔹 PART 1: Install Plugins & Add Credentials

### **Step 1.1: Install Required Plugins**

**Action**: Install Pipeline and Credentials plugins.  
**Purpose**: Enable pipeline jobs and secure auth.

**Steps**:  
1. **Manage Jenkins** → **Plugins** → **Available**.  
2. Install:  
   - **Pipeline**  
   - **Credentials Binding**  
3. **Restart Jenkins**.

**Success Indicators**:  
- ✅ Plugins active.

---

### **Step 1.2: Add Storage Server Credentials**

**Action**: Store natasha credentials securely.  
**Purpose**: Use in pipeline with `withCredentials`.

**Steps**:  
1. **Manage Jenkins** → **Credentials** → **System** → **Global**.  
2. **Add Credentials** → **Username with password**.  
3. Enter:  
   - **Username**: `natasha`  
   - **Password**: `Bl@kW`  
   - **ID**: `ststor01`  
   - **Description**: `Storage Server SSH Access`  
4. Click **OK**.

**Success Indicators**:  
- ✅ Credential ID: `ststor01`.

---

## 🔹 PART 2: Update Gitea Repository

### **Step 2.1: Update index.html**

**Action**: Push new content to Gitea.  
**Purpose**: Trigger deployment.

**Steps (via Gitea UI)**:  
1. Click **Gitea** → Login: `sarah` / `Sarah_pass123`  
2. Go to `sarah/web` → `index.html`  
3. Click **Edit**  
4. Replace content with:  
```
   Welcome to xFusionCorp Industries
```  
5. **Commit changes** → **Commit to master**

**Success Indicators**:  
- ✅ Commit visible in Gitea.

---

## 🔹 PART 3: Create Pipeline Job deploy-job

### **Step 3.1: Create Job**

**Steps**:  
1. **New Item** →  
   - **Name**: `deploy-job`  
   - **Type**: `Pipeline`  
2. Click **OK**.

---

### **Step 3.2: Add Full Pipeline Script**

**Steps**:  
1. **Pipeline** → **Definition**: `Pipeline script`  
2. Paste exact script:  
```groovy
pipeline {
    agent any

    environment {
        GIT_REPO = 'http://git.stratos.xfusioncorp.com/sarah/web.git'
        REMOTE_HOST = 'ststor01'
        REMOTE_PATH = '/var/www/html'
        LB_URL = 'http://stlb01:8091'
    }

    stages {
        stage('Deploy') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'ststor01', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                        sh """
                            echo "Deploying to Storage Server..."
                            sshpass -p "\$PASS" ssh -o StrictHostKeyChecking=no \$USER@\$REMOTE_HOST "
                                cd \$REMOTE_PATH && 
                                git pull origin master
                            "
                            echo "Deployment complete."
                        """
                    }
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    sh """
                        echo "Testing website accessibility..."
                        response=\$(curl -s -o /dev/null -w "%{http_code}" \$LB_URL)

                        if [ \$response -eq 200 ]; then
                            echo "Website is accessible. HTTP Status: \$response"

                            echo "Verifying content..."
                            curl -s \$LB_URL | grep -q "Welcome to xFusionCorp Industries"

                            if [ \$? -eq 0 ]; then
                                echo "Content verification successful!"
                            else
                                echo "Content verification failed!"
                                exit 1
                            fi
                        else
                            echo "Website is not accessible. HTTP Status: \$response"
                            exit 1
                        fi
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment and testing completed successfully!'
        }
        failure {
            echo 'Deployment or testing failed. Check the logs above.'
        }
    }
}
```

**Success Indicators**:  
- ✅ Script saved.  
- ✅ No syntax errors.

---

## 🔹 PART 4: Run Pipeline & Verify

### **Step 4.1: Trigger Build**

**Steps**:  
1. Go to `deploy-job`.  
2. Click **Build Now**.

---

### **Step 4.2: Check Console Output**

**Expected Output**:  
```
[Deploy] Deploying to Storage Server...
Already up to date.
Deployment complete.

[Test] Testing website accessibility...
Website is accessible. HTTP Status: 200
Verifying content...
Content verification successful!

[Post] Deployment and testing completed successfully!
Finished: SUCCESS
```

**Success Indicators**:  
- ✅ Both stages green.  
- ✅ No failures.

---

### **Step 4.3: Verify Website**

**Action**: Click **App** button  
**URL**: `http://stlb01:8091`  
**Content**:  
```
Welcome to xFusionCorp Industries
```

**Success Indicators**:  
- ✅ No subfolder.  
- ✅ Exact content.  
- ✅ Fast load.

---

## 📋 Quick Reference Guide

**Pipeline Summary**:  
```
stage('Deploy') → git pull on ststor01:/var/www/html
stage('Test')   → curl http://stlb01:8091 → check 200 + content
```

**LB URL**: `http://stlb01:8091`  
**Final Content**: `Welcome to xFusionCorp Industries`

---

## 💡 Common Issues & Fixes

| **Issue** | **Fix** |
|-----------|---------|
| sshpass: command not found | Install on Jenkins agent: `yum install sshpass -y` |
| git pull fails | Ensure repo is cloned and tracked |
| curl timeout | Check LB and Apache status |
| Test stage fails | Verify exact content in index.html |

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge**:  
Two-stage pipeline with deployment and strict validation (HTTP 200 + content match), failing on any error.

**💡 Solution**:  
1. Used `withCredentials` for secure SSH  
2. `git pull` in Deploy stage  
3. `curl + grep` in Test stage  
4. `exit 1` on failure → pipeline fails  
5. `post { success/failure }` for clear feedback

**🎯 Key Success Factors**:  
- Stage names: `Deploy`, `Test`  
- `LB_URL = 'http://stlb01:8091'`  
- Content check: `grep -q "Welcome to xFusionCorp Industries"`  
- No subfolder  
- Pipeline fails if test fails

---

## ⚠️ Important Production Notes

🔧 **Security**: Use SSH keys + Gitea webhook  
🔐 **Reliability**: Add retry on git pull  
📊 **Validation**: Check SSL, headers, load time  
🛡️ **Alerting**: Notify on failure  
📹 **Idempotency**: git pull is safe

---

## ✅ Task Completion Checklist

- [ ] Logged into Jenkins with `admin`/`Adm!n321`  
- [ ] Logged into Gitea with `sarah`/`Sarah_pass123`  
- [ ] Installed Pipeline and Credentials Binding plugins  
- [ ] Added Storage Server credentials with ID `ststor01`  
- [ ] Updated `index.html` in Gitea with "Welcome to xFusionCorp Industries"  
- [ ] Created Pipeline job `deploy-job`  
- [ ] Added complete pipeline script with two stages  
- [ ] Configured environment variables correctly  
- [ ] Stage 1 (Deploy) performs `git pull` on Storage Server  
- [ ] Stage 2 (Test) performs HTTP check and content verification  
- [ ] Added post-build actions for success/failure messages  
- [ ] Triggered build successfully  
- [ ] Both stages executed without errors  
- [ ] Verified HTTP 200 response from `http://stlb01:8091`  
- [ ] Confirmed content matches exactly  
- [ ] Website accessible at Load Balancer URL  
- [ ] Documented all steps  

**🎉 Success Criteria Met When**:  
- Pipeline job named `deploy-job` exists  
- Two stages named exactly `Deploy` and `Test`  
- Deploy stage pulls latest code from Gitea  
- Test stage validates HTTP 200 and content match  
- Pipeline fails if either stage fails  
- Website shows "Welcome to xFusionCorp Industries"  
- Accessible via `http://stlb01:8091` with no subfolder  
- Console output shows clear success/failure messages

